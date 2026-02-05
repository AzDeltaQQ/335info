# ADT Terrain Format (v18) for WoW 3.3.5a

This document details the technical specifications of the ADT (Arclight Terrain) file format for World of Warcraft version 3.3.5a (build 12340), corresponding to the v18 of the format.

## Overview

ADT files contain the terrain, object, and liquid information for a single map tile (also known as a block or chunk) in the game world. Each map is divided into a 64x64 grid of tiles, and each tile is further subdivided into 16x16 chunks. The ADT files use a chunked structure, similar to WDT files, where each piece of information is stored in a tagged data block.

## File Structure

The overall structure of an ADT file is a sequence of chunks. Each chunk has a 4-byte identifier (magic code) and a 4-byte size field, followed by the chunk data.

### Main Chunks

| Chunk | Description |
|---|---|
| `MVER` | File version. For 3.3.5a, this is always 18. |
| `MHDR` | Main header, containing offsets to other chunks. |
| `MCIN` | Index for `MCNK` chunks, providing offsets and sizes. |
| `MTEX` | A list of texture filenames used in this tile. |
| `MMDX` | A list of M2 model filenames (doodads). |
| `MMID` | Offsets for `MMDX` filenames. |
| `MWMO` | A list of WMO model filenames (world map objects). |
| `MWID` | Offsets for `MWMO` filenames. |
| `MDDF` | Placement information for M2 models (doodads). |
| `MODF` | Placement information for WMOs. |
| `MFBO` | Flight boundaries (bounding box). |
| `MH2O` | Liquid (water, lava, etc.) data. |
| `MCRF` | References to doodads and objects within each chunk. |
| `MCSH` | Shadow map data. |
| `MCNK` | Map chunk data (256 per ADT file). |

### MCNK Sub-chunks

Each `MCNK` chunk contains its own set of sub-chunks:

| Chunk | Description |
|---|---|
| `MCVT` | Height map data. |
| `MCNR` | Normal vectors. |
| `MCLY` | Texture layer definitions. |
| `MCRF` | Doodad and object references for this chunk. |
| `MCSH` | Shadow map for this chunk. |
| `MCAL` | Alpha maps for texture blending. |
| `MCSE` | Sound emitters. |
| `MCLQ` | Liquid data for this chunk (older format). |
| `MCCV` | Vertex colors. |

## Struct Definitions

```c
// MVER Chunk
struct MVER {
  uint32_t version; // Always 18 for 3.3.5a
};

// MHDR Chunk
struct MHDR {
  enum MHDRFlags {
    mhdr_MFBO = 1,      // Contains an MFBO chunk.
    mhdr_northrend = 2, // Set for Northrend maps.
  };
  uint32_t flags;
  uint32_t mcin;      // Offset to MCIN chunk
  uint32_t mtex;      // Offset to MTEX chunk
  uint32_t mmdx;      // Offset to MMDX chunk
  uint32_t mmid;      // Offset to MMID chunk
  uint32_t mwmo;      // Offset to MWMO chunk
  uint32_t mwid;      // Offset to MWID chunk
  uint32_t mddf;      // Offset to MDDF chunk
  uint32_t modf;      // Offset to MODF chunk
  uint32_t mfbo;      // Offset to MFBO chunk (if flags & mhdr_MFBO)
  uint32_t mh2o;      // Offset to MH2O chunk
  uint32_t mtxf;      // Offset to MTXF chunk
  uint8_t mamp_value; // Cata+, explicit MAMP chunk overrides data
  uint8_t padding[3];
  uint32_t unused[3];
};

// MCIN Chunk
struct MCIN_Entry {
  uint32_t offset; // Absolute offset to MCNK chunk
  uint32_t size;   // Size of the MCNK chunk
  uint32_t flags;  // Always 0 in file, 1 when loaded in client
  uint32_t asyncId; // Client-side use only
};

struct MCIN {
  MCIN_Entry entries[256];
};

// MTEX Chunk
struct MTEX {
  char filenames[]; // Null-terminated strings of texture paths
};

// MMDX Chunk
struct MMDX {
  char filenames[]; // Null-terminated strings of M2 model paths
};

// MMID Chunk
struct MMID {
  uint32_t offsets[]; // Offsets into MMDX for model filenames
};

// MWMO Chunk
struct MWMO {
  char filenames[]; // Null-terminated strings of WMO model paths
};

// MWID Chunk
struct MWID {
  uint32_t offsets[]; // Offsets into MWMO for model filenames
};

// MDDF Chunk
struct MDDF_Entry {
  uint32_t nameId;        // Index into MMID
  uint32_t uniqueId;      // A unique ID for this doodad instance
  float position[3];    // X, Y, Z coordinates
  float rotation[3];    // Rotation in degrees
  uint16_t scale;         // Scale factor * 1024
  uint16_t flags;         // See MDDFFlags
};

// MODF Chunk
struct MODF_Entry {
  uint32_t nameId;        // Index into MWID
  uint32_t uniqueId;      // A unique ID for this WMO instance
  float position[3];    // X, Y, Z coordinates
  float rotation[3];    // Rotation in degrees
  float extents[6];     // Bounding box
  uint16_t flags;         // See MODFFlags
  uint16_t doodadSet;     // Which doodad set to use from the WMO
  uint16_t nameSet;       // Which name set to use
  uint16_t scale;         // Scale factor
};

// MCNK Chunk Header
struct MCNK_Header {
  uint32_t flags;
  uint32_t indexX;
  uint32_t indexY;
  uint32_t nLayers;
  uint32_t nDoodadRefs;
  uint32_t ofsHeight;
  uint32_t ofsNormal;
  uint32_t ofsLayer;
  uint32_t ofsRefs;
  uint32_t ofsAlpha;
  uint32_t sizeAlpha;
  uint32_t ofsShadow;
  uint32_t sizeShadow;
  uint32_t areaid;
  uint32_t nMapObjRefs;
  uint16_t holes_low_res;
  uint16_t unknown_but_used;
  uint8_t predominantTexture[8][8];
  uint8_t noEffectDoodad[8][8];
  uint32_t ofsSndEmitters;
  uint32_t nSndEmitters;
  uint32_t ofsLiquid;
  uint32_t sizeLiquid;
  float position[3];
  uint32_t ofsMCCV;
  uint32_t ofsMCLV; // Cata+
  uint32_t unused;
};

// MCVT Sub-chunk
struct MCVT {
  float height[9*9 + 8*8];
};

// MCNR Sub-chunk
struct MCNR {
  int8_t normal[3][9*9 + 8*8];
};

// MCLY Sub-chunk
struct MCLY_Entry {
  uint32_t textureId; // Index into MTEX
  uint32_t flags;
  uint32_t offsetInMCAL;
  int16_t effectId;
  int16_t padding;
};

// MCRF Sub-chunk
struct MCRF {
  uint32_t doodad_refs[]; // Indices into MDDF
  uint32_t object_refs[]; // Indices into MODF
};

// MCSH Sub-chunk
struct MCSH {
  uint8_t shadow_map[64 * 64 / 8]; // 1 bit per pixel
};

// MCAL Sub-chunk
// See detailed explanation in the wiki page.

// MH2O Chunk
struct MH2O_Header {
  uint32_t ofsInformation;
  uint32_t nLayers;
  uint32_t ofsData;
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
  uint32_t ofsBitmap;
  uint32_t ofsVertexData;
};
```

## Constants and Flags

### MHDR Flags

*   `mhdr_MFBO` = 1: Contains an `MFBO` chunk.
*   `mhdr_northrend` = 2: Set for Northrend maps.

### MCNK Flags

*   `has_mcsh` = 0x1: Has `MCSH` shadow chunk.
*   `impass` = 0x2: Impassable terrain.
*   `lq_river` = 0x4: River water.
*   `lq_ocean` = 0x8: Ocean water.
*   `lq_magma` = 0x10: Magma/lava.
*   `lq_slime` = 0x20: Slime.
*   `has_mccv` = 0x40: Has `MCCV` vertex color chunk.
*   `do_not_fix_alpha_map` = 0x8000: Don't fix 63x63 alpha maps to 64x64.

### MCLY Flags

*   `animation_rotation` = 0x1
*   `animation_speed` = 0x2
*   `animation_enabled` = 0x4
*   `overbright` = 0x8
*   `use_alpha_map` = 0x10
*   `alpha_map_compressed` = 0x20
*   `use_cube_map_reflection` = 0x40

## Relationships to other systems/formats

*   **WDT:** The World Definition Tile file (`.wdt`) defines which ADT tiles exist for a given map and contains global map information.
*   **M2:** Doodads (small objects and decorations) are stored in the M2 model format and are referenced by the `MMDX` and `MDDF` chunks.
*   **WMO:** World Map Objects (large structures like buildings and dungeons) are stored in the WMO format and are referenced by the `MWMO` and `MODF` chunks.
*   **BLP:** Textures used for terrain and models are stored in the BLP (Blizzard Picture) format.

## Tools

*   **Noggit:** A popular World of Warcraft map editor that can view and edit ADT files.
*   **010 Editor:** A hex editor with templates available for various WoW file formats, including ADT.
*   **AdtTools:** A C# framework for manipulating ADT files.
*   **wow-adt:** A Rust crate for parsing ADT files.

## Sources

*   [https://wowdev.wiki/ADT/v18](https://wowdev.wiki/ADT/v18)
