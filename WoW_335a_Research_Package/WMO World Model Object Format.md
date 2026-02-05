'''
# WMO World Model Object Format

## Summary

The World of Warcraft WMO (World Model Object) file format is a comprehensive structure for defining large environmental objects like buildings, dungeons, and other complex static geometry. For version 3.3.5a, the format is composed of a primary root file and one or more group files. The root file acts as a central directory, containing metadata, material definitions, and references to textures, doodads, and the associated group files. Each group file then contains the actual geometric data, including vertices, normals, texture coordinates, and rendering batches for a specific part of the overall WMO.

This document provides a detailed technical breakdown of the WMO format as it exists in World of Warcraft patch 3.3.5a. It covers the structure of both root and group files, detailing the purpose and layout of each data chunk. This includes in-depth descriptions of struct definitions, field meanings, and the various flags that control rendering and object behavior. The relationships between the WMO format and other critical game data files, such as ADT, M2, and BLP, are also explored to provide a complete picture of how these objects are integrated into the game world.
'''

## Struct Definitions

The WMO format is composed of a series of chunks, each identified by a four-character code. The following are the primary struct definitions for the chunks found in WMO files in version 3.3.5a.

### Root File Structs

#### MOHD (Header)

The `MOHD` chunk serves as the header for the root WMO file, providing essential metadata about the object.

```c
struct SMOHeader
{
  uint32_t nTextures;       // Number of textures (BLP files) used in this WMO.
  uint32_t nGroups;         // Number of WMO group files.
  uint32_t nPortals;        // Number of portals.
  uint32_t nLights;         // Number of lights.
  uint32_t nDoodadNames;    // Number of doodad names.
  uint32_t nDoodadDefs;     // Number of doodad instances.
  uint32_t nDoodadSets;     // Number of doodad sets.
  uint32_t ambColor;        // Ambient color in BGRA format.
  uint32_t wmoID;           // Foreign key to WMOAreaTable.dbc.
  CAaBox   boundingBox;     // The bounding box of the entire WMO.
  uint16_t flags;           // WMO flags.
  uint16_t numLod;          // Number of LOD levels (for LOD-enabled WMOs).
};
```

#### MOMT (Materials)

The `MOMT` chunk defines the materials used in the WMO, referencing textures and specifying rendering properties.

```c
struct SMOMaterial
{
  uint32_t flags;           // Material flags.
  uint32_t shader;          // Index into a shader metadata table.
  uint32_t blendMode;       // The blending mode for the material.
  uint32_t texture_1;       // Offset into the MOTX chunk for the first texture.
  uint32_t color_1;         // Color in BGRA format.
  uint32_t flags_1;         // Additional flags for the first texture.
  uint32_t texture_2;       // Offset into the MOTX chunk for the second texture.
  uint32_t color_2;         // Color in BGRA format.
  uint32_t flags_2;         // Additional flags for the second texture.
  uint32_t color_3;         // Color in BGRA format.
  float    f_unk1;          // Unknown float.
  float    f_unk2;          // Unknown float.
  uint32_t dx_texture_1;    // DirectX texture related.
  uint32_t dx_texture_2;    // DirectX texture related.
  uint32_t dx_texture_3;    // DirectX texture related.
  uint32_t dx_texture_4;    // DirectX texture related.
  uint32_t dx_texture_5;    // DirectX texture related.
  uint32_t dx_texture_6;    // DirectX texture related.
  uint32_t dx_texture_7;    // DirectX texture related.
  uint32_t dx_texture_8;    // DirectX texture related.
};
```

### Group File Structs

#### MOGP (Group Header)

The `MOGP` chunk is the header for a WMO group file, containing information about the group's properties and contents.

```c
struct SMOGroup
{
  uint32_t groupName;           // Offset into the MOGN chunk for the group name.
  uint32_t descriptiveGroupName;// Offset into the MOGN chunk for a descriptive name.
  uint32_t flags;               // Group flags.
  CAaBox   boundingBox;         // The bounding box of the group.
  uint16_t portalStart;         // Starting index into the MOPR chunk.
  uint16_t portalCount;         // Number of portal references.
  uint16_t transBatchCount;     // Number of transparent render batches.
  uint16_t intBatchCount;       // Number of interior render batches.
  uint16_t extBatchCount;       // Number of exterior render batches.
  uint16_t padding_or_batch_type_d; // Unknown.
  uint8_t  fogIds[4];           // Indices into the MFOG chunk.
  uint32_t groupLiquid;         // Liquid type information.
  uint32_t uniqueID;            // Foreign key to WMOAreaTable.dbc.
  uint32_t flags2;              // Additional group flags.
  uint32_t unk;                 // Unknown.
};
```

#### MOVT, MOVI, MONR, and MOTV (Geometry Data)

These chunks contain the core geometry data for the WMO group.

```c
// MOVT - Vertices
struct SMOVertex
{
  float x, y, z;
};

// MOVI - Vertex Indices
struct SMOIndices
{
  uint16_t indices[3];
};

// MONR - Normals
struct SMONormal
{
  float nx, ny, nz;
};

// MOTV - Texture Coordinates
struct SMOTexCoord
{
  float u, v;
};
```

#### MOBA (Render Batches)

The `MOBA` chunk defines the render batches, which group triangles that share the same material.

```c
struct SMOBatch
{
  int16_t  unknown_box[6];
  uint32_t startIndex;      // Starting index into the MOVI chunk.
  uint16_t count;           // Number of indices to draw.
  uint16_t minIndex;        // Minimum vertex index in the batch.
  uint16_t maxIndex;        // Maximum vertex index in the batch.
  uint8_t  flag_unknown_1;
  uint8_t  material_id;     // Index into the MOMT chunk.
};
```

#### MOCV (Vertex Colors)

The `MOCV` chunk contains vertex color information, used for pre-calculated lighting.

```c
struct SMOColor
{
  uint8_t b, g, r, a;
};
```

#### MOBN and MOBR (BSP Tree)

These chunks define the Binary Space Partitioning (BSP) tree, which is used for collision detection.

```c
// MOBN - BSP Nodes
struct CAaBspNode
{
  uint16_t flags;       // Node flags (leaf, plane axis).
  int16_t  negChild;    // Index of the negative child node.
  int16_t  posChild;    // Index of the positive child node.
  uint16_t nFaces;      // Number of faces in this node.
  uint32_t faceStart;   // Starting index into the MOBR chunk.
  float    planeDist;   // Distance of the splitting plane.
};

// MOBR - BSP Face Indices
struct SMOBspFace
{
  uint16_t indices[3];
};
```

## Constants and Flags

Various flags within the WMO chunks control rendering and behavior. The following tables detail the most important flags for version 3.3.5a.

| MOHD Flag                                               | Value | Description                                                                                                                              |
| ------------------------------------------------------- | ----- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `flag_do_not_attenuate_vertices_based_on_distance_to_portal` | 0x1   | Prevents vertex attenuation based on portal distance.                                                                                    |
| `flag_use_unified_render_path`                          | 0x2   | In 3.3.5a, this flag switches between the classic and unified rendering paths.                                                           |
| `flag_use_liquid_type_dbc_id`                           | 0x4   | Uses the liquid type ID from the `LiquidType.dbc` file instead of a local one.                                                           |
| `flag_do_not_fix_vertex_color_alpha`                    | 0x8   | Prevents the `CMapObjGroup::FixColorVertexAlpha` function from being executed, which affects how vertex colors are processed.              |

| MOMT Flag      | Value | Description                                                                 |
| -------------- | ----- | --------------------------------------------------------------------------- |
| `F_UNLIT`      | 0x1   | Disables the lighting logic in the shader.                                  |
| `F_UNFOGGED`   | 0x2   | Disables fog shading.                                                       |
| `F_UNCULLED`   | 0x4   | Makes the material two-sided.                                               |
| `F_EXTLIGHT`   | 0x8   | Darkens the material.                                                       |
| `F_SIDN`       | 0x10  | Makes the material bright at night and unshaded.                            |
| `F_WINDOW`     | 0x20  | Related to lighting, particularly for windows.                              |
| `F_CLAMP_S`    | 0x40  | Forces texture coordinate S to be clamped.                                  |
| `F_CLAMP_T`    | 0x80  | Forces texture coordinate T to be clamped.                                  |

| MOGP Flag         | Value     | Description                                                              |
| ----------------- | --------- | ------------------------------------------------------------------------ |
| `Has BSP tree`    | 0x1       | The group has a BSP tree, defined in the `MOBN` and `MOBR` chunks.       |
| `Has vertex colors` | 0x4       | The group has vertex colors, defined in the `MOCV` chunk.                |
| `EXTERIOR`        | 0x8       | The group is an exterior environment.                                    |
| `EXTERIOR_LIT`    | 0x40      | Disables local diffuse lighting for the group.                           |
| `UNREACHABLE`     | 0x80      | The group is considered unreachable.                                     |
| `Has lights`      | 0x200     | The group has light references, defined in the `MOLR` chunk.             |
| `Has doodads`     | 0x800     | The group has doodad references, defined in the `MODR` chunk.            |
| `LIQUIDSURFACE`   | 0x1000    | The group contains water or other liquids, defined in the `MLIQ` chunk.  |
| `INTERIOR`        | 0x2000    | The group is an interior environment.                                    |
| `Show skybox`     | 0x40000   | The skybox should be rendered for this group.                            |
| `CVERTS2`         | 0x1000000 | The group has a second `MOCV` chunk.                                     |
| `TVERTS2`         | 0x2000000 | The group has a second `MOTV` chunk.                                     |

## Relationships

The WMO format is deeply integrated with other file formats in World of Warcraft. The following table outlines the key relationships:

| File Format | Relationship                                                                                                                                                                                            |
| :--- | :--- |
| **WDT** | The World Definition Template (`.wdt`) files act as a high-level map of the world, containing a grid of map tiles. For each tile, a WDT file can specify the placement of a WMO, including its position, rotation, and scale.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   - **ADT** | Area Data Template (`.adt`) files define the terrain for each map tile. They can also contain references to WMOs, specifying their placement and orientation within the tile. Additionally, the doodad sets to be used for a WMO instance are defined in the ADT file. |
| **M2/MDX** | The M2 (`.m2`) format is used for dynamic models such as characters, creatures, and doodads. WMO files reference M2 models for the doodads that are placed within them, which are decorative objects that add detail to the environment. |
| **BLP** | Blizzard Picture (`.blp`) files are the texture format used throughout the game. WMO files reference BLP files for the textures that are applied to their surfaces, which are defined in the `MOMT` chunk. |

## Related Tools

A variety of tools are available for working with WMO files, ranging from viewers to full-fledged editors.

| Tool | Description |
| :--- | :--- |
| **WoW Model Viewer** | A 3D model viewer for World of Warcraft that can be used to view WMO and M2 models. It is a useful tool for inspecting the contents of WMO files and understanding their structure. |
| **Blender WMO import/export scripts** | An addon for the 3D modeling software Blender that allows for the import, export, and editing of WMO files. This is a powerful tool for creating and modifying WMOs. |
| **wow_wmo (Rust library)** | A library for the Rust programming language that provides a comprehensive set of tools for parsing, editing, and converting WMO files. |
| **010 Editor** | A hex editor that has a template for the WMO file format. This allows for the direct viewing and editing of the raw data in a WMO file. |

## Sources

*   [wowdev.wiki/WMO](https://wowdev.wiki/WMO)
*   [wowdev.wiki/WMO/Rendering](https://wowdev.wiki/WMO/Rendering)

## Code Examples

The following code examples are taken from the client and illustrate how the WMO data is used in practice.

### CMapObj::QueryLighting (C3Segment)

This function is used to query the lighting at a specific point within a WMO group. It uses a `C3Segment` to perform a ranged query against the BSP tree to find the relevant triangle and then interpolates the vertex colors to determine the light value.

```cpp
bool CMapObj::QueryLighting ( CMapObj * this , uint32_t groupIndex , const C3Segment * seg , CImVector * color , bool * a5 ) { 

 CMapObjGroup group = this -> groupList [ groupIndex ]; 

 if ( ! this -> unk6 [ 16 ] || ! ( group -> unk14 & 1 ) || group -> flags & ( SMOGroup :: EXTERIOR | SMOGroup :: EXTERIOR_LIT )) { 

 return 0 ; 

 } 

 World :: TriData :: resultFlags = 0 ; 
 World :: TriData :: nBatches = 0 ; 
 World :: TriData :: nTriIndices = 0 ; 
 World :: TriData :: nVertexIndices = 0 ; 
 World :: TriData :: nMatrices = 0 ; 

 float hitT = 1.0 ; 

 // Query the BSP tree for the group to find appropriate tris 

 bool triRes = CMapObjGroup :: GetTris ( group , seg , & hitT , 0 , 0x8 , ( int ) & a2 + 3 , 0 ); 

 if ( ! triRes ) { 

 return 0 ; 

 } 

 // Obtain point matching intersection between segment and tri 

 C3Vector point ; 

 point . x = seg -> start . x + hitT * ( seg -> end . x - seg -> start . x ); 
 point . y = seg -> start . y + hitT * ( seg -> end . y - seg -> start . y ); 
 point . z = seg -> start . z + hitT * ( seg -> end . z - seg -> start . z ); 

 unsigned __int16 hitPoly = word_CD8094 ; 

 bool lightRes = CMapObjGroup :: QueryLighting ( group , & point , hitPoly , color , a5 ); 

 return lightRes ; 

 }
```

### CMapObjGroup::FixColorVertexAlpha (3.3.5a)

This function is called to adjust the vertex colors, particularly the alpha channel, based on the batch type (transparent, interior, or exterior). This is a key part of the pre-calculated lighting process for WMOs.

```cpp
void CMapObjGroup::FixColorVertexAlpha () 
 { 
 int32_t intBatchStart ; 

 if ( this -> transBatchCount > 0 ) 
 { 
 intBatchStart = this -> batchList [ this -> transBatchCount - 1 ]. maxIndex + 1 ; 
 } 
 else 
 { 
 intBatchStart = 0 ; 
 } 

 if ( this -> colorVertexCount > 0 ) 
 { 
 for ( int32_t i = 0 ; i < this -> colorVertexCount ; i ++ ) 
 { 
 CImVector * color = & this -> colorVertexList [ i ]; 

 // Int / ext batches 
 if ( i >= intBatchStart ) 
 { 
 int32_t v6 = ( color -> r + ( color -> a * color -> r / 64 )) / 2 ; 
 int32_t v7 = ( color -> g + ( color -> a * color -> g / 64 )) / 2 ; 
 int32_t v8 = ( color -> b + ( color -> a * color -> b / 64 )) / 2 ; 

 v6 = v6 > 255 ? 255 : v6 ; 
 v7 = v7 > 255 ? 255 : v7 ; 
 v8 = v8 > 255 ? 255 : v8 ; 

 color -> r = v6 ; 
 color -> g = v7 ; 
 color -> b = v8 ; 

 color -> a = 255 ; 
 // Trans batches 
 } 
 else 
 { 
 color -> r /= 2 ; 
 color -> g /= 2 ; 
 color -> b /= 2 ; 
 } 
 } 
 } 
 }
```
