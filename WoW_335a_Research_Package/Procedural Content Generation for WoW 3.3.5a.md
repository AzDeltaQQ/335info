# Procedural Content Generation for WoW 3.3.5a

This document provides a detailed analysis of the technical specifications related to procedural content generation in World of Warcraft patch 3.3.5a, with a focus on terrain generation (WDT and ADT files) and an investigation into procedural dungeon generation.

## WDT File Format

The WDT (World Definition Template) file is a crucial component of World of Warcraft's terrain system. It acts as a top-level index that defines the layout of a world map, specifying which map tiles (ADTs) are present and whether a global World Model Object (WMO) is used. WDT files employ a chunk-based structure, similar to other WoW file formats.

### MPHD Chunk

The `MPHD` chunk serves as the main header for the WDT file, containing flags and other data that affect the entire map.

```c
struct SMMapHeader {
   uint32_t flags;
   uint32_t something; // In WotLK, this is used to initialize flags and other data for the MAIN chunk entries.
   uint32_t unused[6];
};
```

#### MPHD Flags

- `wdt_uses_global_map_obj (0x1)`: Indicates that the map uses a global WMO.
- `adt_has_mccv (0x2)`: Specifies that the ADT files for this map contain vertex coloring data.
- `adt_has_big_alpha (0x4)`: Enables the use of larger 4096-byte alpha maps for terrain texturing.
- `adt_has_doodadrefs_sorted_by_size_cat (0x8)`: Doodad references within the ADTs are sorted by size category.
- `FLAG_LIGHTINGVERTICES (0x10)`: The ADTs contain vertex lighting information.
- `adt_has_upside_down_ground (0x20)`: Renders the ground geometry upside down, creating a ceiling effect.

### MAIN Chunk

The `MAIN` chunk consists of a 64x64 grid of `SMAreaInfo` structures, corresponding to the 4096 possible map tiles in a world. Each structure contains flags that indicate the status of a specific tile.

```c
struct SMAreaInfo {
  uint32_t Flag_HasADT : 1;
  uint32_t Flag_AllWater : 1;
  uint32_t Flag_Loaded : 1;
  uint32_t asyncId;    // Used by the client at runtime.
};
```

### MWMO and MODF Chunks

For maps that do not have terrain and instead consist of a single, large model (e.g., indoor instances), the WDT file will contain `MWMO` and `MODF` chunks.

- **MWMO Chunk**: A null-terminated string that specifies the file path to the global WMO.
- **MODF Chunk**: Contains placement data for the global WMO, including its position, rotation, and scale.

```c
struct SMMapObjDef {
  uint nameId; // Unused
  uint uniqueId; // Unused
  float pos[3];
  float rot[3];
  float extents_upper[3];
  float extents_lower[3];
  uint16 flags;
  uint16 doodadSet;
  uint16 nameSet;
  uint16 pad;
};
```

## ADT File Format

The ADT (Area Definition Template) files contain the detailed terrain data for each map tile, including the heightmap, texture information, and placement of models and other objects. Like WDT files, ADTs are chunk-based.

### MVER Chunk

A simple chunk that contains the version of the ADT file. For patch 3.3.5a, this value is always 18.

```c
struct MVER {
  uint32_t version; // 18
};
```

### MHDR Chunk

The header for the ADT file, containing offsets to other important chunks.

```c
struct SMMapHeader {
  uint32_t flags;
  uint32_t mcin;      // Offset to MCIN chunk
  uint32_t mtex;      // Offset to MTEX chunk
  uint32_t mmdx;      // Offset to MMDX chunk
  uint32_t mmid;      // Offset to MMID chunk
  uint32_t mwmo;      // Offset to MWMO chunk
  uint32_t mwid;      // Offset to MWID chunk
  uint32_t mddf;      // Offset to MDDF chunk
  uint32_t modf;      // Offset to MODF chunk
  uint32_t mfbo;      // Offset to MFBO chunk (if flags & 1)
  uint32_t mh2o;      // Offset to MH2O chunk
  uint32_t mtxf;      // Offset to MTXF chunk
  uint8_t mamp_value; // Added in Cata+
  uint8_t padding[3];
  uint32_t unused[3];
};
```

### MCIN Chunk

This chunk contains an array of 256 `SMChunkInfo` structures, one for each of the 16x16 map chunks within the ADT. Each structure provides the offset and size of the corresponding `MCNK` chunk.

```c
struct SMChunkInfo {
  uint32_t offset;    // Absolute offset to the MCNK chunk
  uint32_t size;      // Size of the MCNK chunk
  uint32_t flags;     // Always 0 in file, used by client
  uint32_t asyncId;   // Not in file, used by client
} mcin[256];
```

### MTEX, MMDX, and MWMO Chunks

These chunks contain lists of file paths for textures, M2 models (doodads), and WMOs, respectively. The file paths are stored as null-terminated strings.

### MMID and MWID Chunks

These chunks contain arrays of offsets that point to the file paths within the `MMDX` and `MWMO` chunks.

### MDDF and MODF Chunks

These chunks define the placement of doodads and WMOs within the ADT.

```c
// MDDF (Doodad Definition)
struct SMDoodadDef {
    uint32_t nameId;        // Index into the MMID chunk
    uint32_t uniqueId;      // Unique ID for this doodad
    float pos[3];
    float rot[3];
    uint16_t scale;
    uint16_t flags;
};

// MODF (WMO Definition)
struct SMMapObjDef {
    uint32_t nameId;        // Index into the MWID chunk
    uint32_t uniqueId;      // Unique ID for this WMO
    float pos[3];
    float rot[3];
    float extents_upper[3];
    float extents_lower[3];
    uint16_t flags;
    uint16_t doodadSet;
    uint16_t nameSet;
    uint16_t pad;
};
```

### MCNK Chunk (Map Chunk)

Each ADT file is composed of 256 `MCNK` chunks, which represent a 33.33x33.33 yard area of terrain. These chunks contain the actual terrain geometry and other details.

```c
struct MCNK {
    uint32_t flags;
    uint32_t indexX;
    uint32_t indexY;
    uint32_t nLayers;
    uint32_t nDoodadRefs;
    uint32_t ofsMCVT;     // Offset to MCVT chunk
    uint32_t ofsMCNR;     // Offset to MCNR chunk
    uint32_t ofsMCLY;     // Offset to MCLY chunk
    uint32_t ofsMCRF;     // Offset to MCRF chunk
    uint32_t ofsMCAL;     // Offset to MCAL chunk
    uint32_t sizeAlpha;
    uint32_t ofsMCSH;     // Offset to MCSH chunk
    uint32_t sizeShadow;
    uint32_t areaid;
    uint32_t nMapObjRefs;
    uint32_t holes;
    uint16_t ReallyLowQualityTextureingMap[8];
    uint32_t predTex;
    uint32_t nEffectDoodad;
    uint32_t ofsMCSE;     // Offset to MCSE chunk
    uint32_t nSndEmitters;
    uint32_t ofsMCLQ;     // Offset to MCLQ chunk
    uint32_t sizeLiquid;
    float  zpos;
    float  xpos;
    float  ypos;
    uint32_t textureId;   // Only used for MCSH version > 1
    uint32_t props;       // Only used for MCSH version > 1
    uint32_t effectId;    // Only used for MCSH version > 1
};
```

#### MCNK Sub-chunks

- **MCVT**: Height map data, consisting of 145 float values.
- **MCNR**: Vertex normals.
- **MCLY**: Texture layer definitions.
- **MCRF**: References to doodads.
- **MCAL**: Alpha maps for blending textures.
- **MCSH**: Shadow map data.
- **MCSE**: Sound emitters.
- **MCLQ**: Liquid data (pre-WotLK format).

### MH2O Chunk (Liquid Data)

Introduced in Wrath of the Lich King, the `MH2O` chunk provides a more sophisticated way to represent liquids.

```c
struct MH2O_Header {
    uint32_t ofsInformation; // Offset to information block
    uint32_t layerCount;     // Always 1 in WotLK
    uint32_t ofsData;        // Offset to data block
};

struct MH2O_Information {
    uint16_t liquidType;
    uint16_t flags;
    float heightLevel1;
    float heightLevel2;
    uint8_t xOffset;
    uint8_t yOffset;
    uint8_t width;
    uint8_t height;
    uint32_t ofsBitmap; // Offset to existence bitmap
    uint32_t ofsVertexData; // Offset to vertex data
};
```

## Procedural Dungeon Generation

Research into procedural dungeon generation within World of Warcraft 3.3.5a did not yield any evidence of its implementation. While the concept was a topic of discussion among the player community, there are no indications that Blizzard used procedural algorithms to create dungeons during this era. Dungeons and raids were manually constructed using the established WMO and ADT file formats, which allowed for detailed and bespoke level design.
'''

## Tools

Several community-developed tools are available for working with WDT and ADT files for World of Warcraft 3.3.5a:

- **Noggit**: A popular and powerful map editor for WotLK, allowing for the creation and modification of ADT and WDT files. It provides a graphical interface for terrain sculpting, texture painting, and object placement.
- **AdtTools**: A C# framework for programmatically manipulating ADT files. It provides libraries for reading, writing, and modifying various chunks within the ADT file format.
- **wow-adt** and **wow-wdt**: Rust crates for parsing and manipulating ADT and WDT files, respectively. These are suitable for developers who prefer to work with Rust.
- **010 Editor**: A hex editor with available templates for various WoW file formats, including ADT and WDT. This allows for low-level inspection and modification of the files.
'''
