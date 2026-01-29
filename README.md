# Technical Specifications and Binary Internals of the World of Warcraft: Wrath of the Lich King Engine Assets

The digital architecture of **World of Warcraft: Wrath of the Lich King** (build 3.3.5a, version 12340) represents a sophisticated culmination of early-2000s graphics engineering, balancing the constraints of limited video memory with the requirements of a vast, seamless open world. At the heart of this system lies a proprietary suite of binary file formats designed for rapid streaming from high-compression archives.

Understanding these internals is essential for modern engine developers seeking to build custom parsers or generative pipelines. The system is built upon the **MoPaQ (MPQ)** container, which houses terrain (`ADT`/`WDT`), large static geometry (`WMO`), dynamic skeletal models (`M2`/`SKIN`), and high-performance textures (`BLP`). This investigation provides an exhaustive technical analysis of these formats, their sub-chunks, and the mathematical foundations of their coordinate systems.

---

## Spatial Foundations and Coordinate Systems

Precision in coordinate transformation is the prerequisite for any implementation of terrain generators or model compilers. World of Warcraft employs a **right-handed coordinate system** with a unique axis orientation that frequently confuses developers transitioning from standard DCC tools. In the 3.3.5a client:

*   **Positive X-axis:** Points North
*   **Positive Y-axis:** Points West
*   **Positive Z-axis:** Represents vertical height

This orientation is consistent across the global map and local model space, though pivot conventions vary depending on the asset type.

The fundamental unit of measurement is the **yard**. The global world is partitioned into a 64x64 grid of tiles, designated as ADT blocks. Each tile measures exactly 1600 feet, or 533.3333 yards. Consequently, the entire map span is 34,133.33312 yards. The coordinate origin $(0, 0, 0)$ is situated at the geographic center of the map, meaning that the maximum and minimum world coordinates are $\pm 17,066.66656$ yards.

> **Note:** A common error in external tools is the inversion of the Y-axis; it must be remembered that moving "Right" in the game world (facing North) corresponds to decreasing Y-values.

### Spatial Unit Conversion Table

| Spatial Unit | Yard Measurement | Metric Equivalent (Approx) |
| :--- | :--- | :--- |
| **Global Map Width** | 34,133.33312 | 31,211.5 meters |
| **ADT Tile Width** | 533.33333 | 487.68 meters |
| **MCNK Chunk Width** | 33.33333 | 30.48 meters |
| **Terrain Unit Width** | 4.16666 | 3.81 meters |
| **Player Run Speed** | 7.11111 | 6.50 meters/sec |

### Coordinate Mapping Formulas

The mapping between tile indices $(I_x, I_y)$ and world coordinates $(W_x, W_y)$ is defined by the following linear formulas:

```text
Wₓ = (32 − Iₓ) × 533.33333
Wᵧ = (32 − Iᵧ) × 533.33333
```

For individual MCNK chunks within a tile (where chunk indices $C_x, C_y$ range from 0 to 15), the local offset is:

$$ O_x = C_x \times 33.33333 $$
$$ O_y = C_y \times 33.33333 $$

### Time Representation
Time in the engine is also numerically represented. The client handles time through a float value ranging from **0 to 1** from the Alpha through Cataclysm, where `0.5` represents midday and `0.0` represents dawn. In build 3.3.5a, the memory offsets for time values are critical for synchronizing environmental lighting and scripted events.

---

## MoPaQ (MPQ) Archive Architecture

The MoPaQ (MPQ) format is the container for all assets, optimized for high-speed file lookup without storing plaintext filenames. The format relies on a hash table where files are indexed by multiple 32-bit hashes generated from the normalized file path (using uppercase and backslashes).

### MPQ Header and Shunt
Archives can be embedded within other files, such as executables. The client searches for the `ID_MPQ` ('MPQ\x1A') signature at 512-byte (`0x200`) intervals. If an `ID_MPQ_SHUNT` ('MPQ\x1B') is found, it points to the true header location.

| Offset | Type | Field | Description |
| :--- | :--- | :--- | :--- |
| `0x00` | `uint32` | `Magic` | 'MPQ\x1A' signature |
| `0x04` | `uint32` | `HeaderSize` | Size of the header (at least 0x20) |
| `0x08` | `uint32` | `ArchiveSize` | Total size of the archive |
| `0x0C` | `uint16` | `FormatVersion` | 0 for original, 1 for Burning Crusade/Wrath |
| `0x10` | `uint16` | `BlockSize` | Power of two for sector size ($512 \times 2^{BlockSize}$) |
| `0x12` | `uint32` | `HashTablePos` | Offset to the Hash Table |
| `0x14` | `uint32` | `BlockTablePos` | Offset to the Block Table |
| `0x18` | `uint32` | `HashTableSize` | Number of entries (must be power of 2) |
| `0x1C` | `uint32` | `BlockTableSize` | Number of entries in the Block Table |

### Hashing and Collision Resolution
The **HashString** algorithm uses a seed-based approach to generate three distinct hashes for every path: `HASH_OFFSET`, `HASH_A`, and `HASH_B`. `HASH_OFFSET` determines the starting index in the table. If a collision occurs (where the hashes do not match the target), the engine uses open addressing with linear probing, shifting to the next slot until the file is found or an empty slot (`0xFFFFFFFF`) is encountered.

The encryption of files within an MPQ is a bitwise XOR stream cipher where the key is derived from the filename. If the `MPQ_FILE_ENCRYPT_ADJUSTED` flag (`0x00020000`) is set, the key is modified by the file's offset and size, a mechanism intended to prevent decryption if the file's position is changed without re-encrypting.

---

## Terrain Systems: WDT and ADT Specifications

World maps are defined by a **World Definition Table (WDT)** and a series of **Area Data Tables (ADT)**. The WDT specifies which tiles are present in the 64x64 world grid. For maps without terrain (such as dungeons), the WDT can reference a global World Map Object (WMO).

### ADT Chunk Layout
The ADT format is strictly chunk-based. In build 3.3.5a, all terrain data resides in a single ADT file, unlike later expansions where it is split into root, texture, and object files.

| Chunk | Description | Implementation Relevance |
| :--- | :--- | :--- |
| **MHDR** | Main Header | Contains offsets to all other chunks |
| **MCIN** | Chunk Index | 256 entries providing offsets to MCNK chunks |
| **MTEX** | Texture List | Filenames of BLP textures used for terrain layering |
| **MMDX** | M2 List | Filenames for dynamic doodads placed on this tile |
| **MWMO** | WMO List | Filenames for large static map objects |
| **MDDF** | M2 Placement | Placement info: ID, unique ID, position, rotation, scale |
| **MODF** | WMO Placement | Placement info: ID, unique ID, position, rotation, extents |

### MCNK: The Terrain Cell Deep Dive
Each ADT tile contains 256 `MCNK` chunks, each representing a 33.3333-yard cell. The MCNK header is 128 bytes and contains bitmasks that are vital for rendering.

| Offset | Type | Field | Meaning |
| :--- | :--- | :--- | :--- |
| `0x00` | `uint32` | `Flags` | Bitfield for data presence (e.g., 0x1: shadow, 0x4: vertex color) |
| `0x04` | `int` | `IndexX` | X-index (0-15) |
| `0x08` | `int` | `IndexY` | Y-index (0-15) |
| `0x0C` | `uint32` | `nLayers` | Number of texture layers (max 4 in 3.3.5a) |
| `0x10` | `uint16` | `Holes` | 16-bit hole mask for 8x8 units |
| `0x1C` | `uint32` | `nDoodadRefs` | Number of M2 objects in this chunk |
| `0x60` | `uint32` | `areaId` | Reference to AreaTable.dbc |

The **Holes** field is a 16-bit bitmask that controls terrain visibility. In 3.3.5a, these 16 bits are mapped row-wise to a 4x4 grid of "sub-chunks." Each bit corresponds to a 2x2 area of the 8x8 terrain units. Setting a bit to 1 prevents the engine from rendering that part of the mesh, allowing for cave entrances or cellar cutouts.

### Heightmap Encoding (MCVT)
The terrain grid in an MCNK consists of an interleaved set of vertices: an "outer" 9x9 grid and an "inner" 8x8 grid, for a total of 145 float values. The outer grid points are sampled at the corners of the 8x8 units, while the inner grid points are at the centers of the units. This configuration enables the engine to render terrain with a higher level of surface detail than a simple 9x9 grid would allow.

The height values are relative to the Z-coordinate specified in the MCNK header. To calculate the absolute world height $H_{abs}$ of a vertex $v$:

$$ H_{abs} = Z_{mcnk} + H_{mcvt}[v] $$

### Texture Layering and Alpha Packing (MCLY and MCAL)
Texture blending is handled by the `MCLY` and `MCAL` chunks. `MCLY` defines which textures from the `MTEX` list are applied and their properties. `MCAL` contains the alpha maps.

In build 3.3.5a, the MCAL alpha map is typically 64x64 pixels. The engine supports two main encodings for these maps to save memory:
1.  **Uncompressed:** 4096-byte format (8 bits per pixel).
2.  **Compressed:** 2048-byte format (4 bits per pixel) or a compressed RLE-style format used when the `alpha_map_compressed` flag (`0x200`) is set.

---

## Liquid Systems: MH2O

The `MH2O` chunk is the modernized liquid system introduced in the Burning Crusade and refined for Wrath of the Lich King. It allows for multiple liquid types (water, ocean, lava, slime) and varying heights within a single ADT tile. The data is organized into headers, instances, and vertex data.

Liquid vertex data is stored in one of four formats, identified by a multiplier calculated from the total size of the vertex buffer:
*   **Case 0 (Multiplier 5):** `float height`, `uint8 depth`.
*   **Case 1 (Multiplier 8):** `float height`, `uint16 U`, `uint16 V`.
*   **Case 2 (Multiplier 1):** `uint8 depth` only (height is fixed).
*   **Case 3 (Multiplier 9):** `float height`, `uint16 U`, `uint16 V`, `uint8 depth`.

The depth values are used for procedural foam and shoreline transparency, while the UV coordinates allow for scrolling texture effects such as flowing rivers or lava.

---

## World Map Objects (WMO): Root and Group Internals

Large static structures use the **WMO** format, which is divided into a single root file (`.wmo`) and multiple group files (`_000.wmo`, `_001.wmo`, etc.). This separation allows the engine to load only the geometry that is currently visible or relevant to the player's position.

### WMO Root File Chunks
The root file contains global metadata, material definitions, and the portal system.

| Chunk | Structure | Description |
| :--- | :--- | :--- |
| **MOHD** | Header | Counts of textures, groups, portals, and lights |
| **MOTX** | Textures | Null-terminated string list of BLP paths |
| **MOMT** | Materials | SMOMaterial entries (64 bytes each) |
| **MOGI** | Group Info | Metadata for each group, including flags and bounding boxes |
| **MOPV** | Portal Verts | List of C3Vector vertex positions |
| **MOPT** | Portals | Defines portal planes and vertex start/count |
| **MOPR** | Portal Refs | Links portals to specific groups |

The portal system is critical for interior occlusion culling. Each portal is a planar polygon. When the camera's frustum intersects a portal, the engine adds the connected group to the render list. This allows for complex interiors where one room is completely culled while the player is in another, despite both being part of the same WMO.

### WMO Group File and BSP Tree
Group files contain the actual geometry. To optimize collision detection and camera queries, each group includes a **Binary Space Partitioning (BSP)** tree in the `MOBN` and `MOBR` chunks.

*   **MOBN** (Nodes):
    *   **Flags:** If bit `0x01` is set, it is a leaf node; if `0x04` is set, it is clipped.
    *   **Split Value:** The coordinate along the axis (X, Y, or Z) where the partition occurs.
    *   **nFaces:** For leaf nodes, the number of triangles in this partition.
    *   **negChild / posChild:** Indices to child nodes or the face list in MOBR.
*   **MOBR**: A list of `uint16` indices that map leaf nodes to the triangles in the `MOVI` chunk.

This architecture enables $O(\log N)$ collision lookups, which is essential for maintaining high frame rates in dense environments like Dalaran.

---

## M2 Models and SKIN Profiles

The **M2** format handles skeletal meshes, including players and NPCs. In the 3.3.5a client, the rendering-specific data (indices, submeshes) is decoupled from the main M2 file and stored in `.skin` files.

### M2 Header (MD20) and Vertex Layout
The M2 header is a fixed-size block that uses `M2Array` (count and offset) to point to data blocks.

| Offset | Type | Field | Relevance |
| :--- | :--- | :--- | :--- |
| `0x00` | `uint32` | `Magic` | "MD20" |
| `0x04` | `uint32` | `Version` | 264 for Wrath |
| `0x2C` | `M2Array` | `Bones` | Skeletal hierarchy and bone animations |
| `0x3C` | `M2Array` | `Vertices` | Position, weights, indices, normals, UVs |
| `0x128` | `M2Array` | `Particles` | Particle emitter definitions |

The **M2Vertex** structure is optimized for hardware skinning:
*   **Position:** `C3Vector` (float x, y, z)
*   **Bone Weights:** Four `uint8` values (0-255)
*   **Bone Indices:** Four `uint8` indices into the bone lookup table
*   **Normal:** `C3Vector`
*   **UV Coordinates:** Two floats

For a vertex to deform correctly, the bone weights must be normalized such that:

$$ \sum_{i=1}^{4} \text{Weight}_i = 255 $$

### Skeletal Animation Blocks
Animation data in M2 is stored in `M2Track` blocks. In build 3.3.5a, the system shifted from start/end frames to explicit durations in milliseconds. The engine supports three interpolation types:
1.  **None (0):** Step function.
2.  **Linear (1):** Standard lerp.
3.  **Hermite (2):** Cubic spline interpolation using tangents for smooth movement.

For Hermite tracks, each keyframe provides a value $P$, an "in" tangent $T_{in}$, and an "out" tangent $T_{out}$. The interpolation is calculated using the Hermite basis functions over the normalized time $s$:

$$ h_1(s) = 2s^3 - 3s^2 + 1 $$
$$ h_2(s) = -2s^3 + 3s^2 $$
$$ h_3(s) = s^3 - 2s^2 + s $$
$$ h_4(s) = s^3 - s^2 $$
$$ \text{Result} = h_1(s)P_i + h_2(s)P_{i+1} + h_3(s)T_{out,i} + h_4(s)T_{in,i+1} $$

### SKIN Profiles and Bone Remapping
The `.skin` files (named `Model00.skin` through `Model03.skin` for LOD levels) contain the submesh and index data.

| Field | Type | Description |
| :--- | :--- | :--- |
| `vertices` | `M2Array` | Subset of global M2 vertices used in this skin |
| `indices` | `M2Array` | Triangle indices |
| `submeshes` | `M2Array` | M2SkinSection definitions |
| `batches` | `M2Array` | Texture unit rendering properties |

A critical component of the SKIN format is the **Bone Remap Table**. Because old GPUs had limited constant registers for bone matrices, a submesh cannot reference the entire skeleton. Each submesh defines a subset of bones (up to 256 in Wrath), and the vertex bone indices are local to this subset. The `boneComboIndex` in the `M2SkinSection` points to the starting position in the skin's global bone lookup table.

---

## BLP2: Texture Compression and Alpha Encoding

The **BLP** format is optimized for direct uploading to GPU memory, primarily utilizing DXT compression. The 'BLP2' header (version 1) is used throughout 3.3.5a.

### BLP Header and Mipmap Offsets

| Offset | Type | Field | Description |
| :--- | :--- | :--- | :--- |
| `0x08` | `uint8` | `Compression` | 1: Palette, 2: DXT, 3: ARGB8888 |
| `0x09` | `uint8` | `AlphaDepth` | 0, 1, 4, or 8 bits |
| `0x0A` | `uint8` | `AlphaType` | DXT version or palette type |
| `0x0C` | `uint32` | `Width` | Power of 2 |
| `0x14` | `uint32` | `MipOffsets` | File offsets for up to 16 mipmap levels |

### DXT Compression and Size Correction
In the 3.3.5a client, some mipmap sizes stored in the BLP header for compressed textures are technically incorrect. Custom parsers must recalculate the expected size to avoid buffer underflows. The valid size for a DXT mipmap is:

$$ \text{Blocks}_x = \frac{\text{Width} + 3}{4} $$
$$ \text{Blocks}_y = \frac{\text{Height} + 3}{4} $$
$$ \text{Size}_{DXT1} = \text{Blocks}_x \times \text{Blocks}_y \times 8 $$
$$ \text{Size}_{DXT3/5} = \text{Blocks}_x \times \text{Blocks}_y \times 16 $$

For uncompressed, palettized textures, the header is followed by a 256-entry BGRA palette (1024 bytes). The image data follows, where each byte is an index into the palette. Alpha data, if present, is appended after the index data for each mipmap level.

---

## Client Databases (DBC)

The **WDBC** format is a flat-file database used for client-side logic. Each file contains a header, a sequence of records, and a string block.

| Offset | Type | Field | Description |
| :--- | :--- | :--- | :--- |
| `0x00` | `uint32` | `Magic` | 'WDBC' |
| `0x04` | `uint32` | `Records` | Total number of rows |
| `0x08` | `uint32` | `Fields` | Total number of columns |
| `0x0C` | `uint32` | `RecordSize` | Bytes per row |
| `0x10` | `uint32` | `StringSize` | Bytes in the string block |

Records do not store strings directly; instead, they store a 4-byte offset into the string block. The `RecordSize` is the sum of all field sizes. Since the field types (int, float, string offset) are not defined in the file, parsers must rely on external definitions such as those provided by community reverse engineering projects.

---

## Asset Generation Pipelines and Implementation Logic

Building assets for the 3.3.5a engine requires a precise sequence of operations to maintain compatibility with the client's internal validation.

### Terrain Generation (ADT)
1.  **Heightmap Processing:** A 128x128 grayscale heightmap (representing the 16x16 chunks with 8x8 units each) is mapped to the float values of the `MCVT` chunks.
2.  **Normal Calculation:** Terrain normals in `MCNR` are 3-byte signed vectors. For each vertex $V$, the normal $N$ is the average of the face normals of the surrounding quads.
3.  **Alpha Layering:** To generate the `MCAL` alpha maps, the developer must determine the blend weight of each of the 4 texture layers. If more than 4 textures are required, the terrain must be split into separate chunks or the textures must be pre-composited.
4.  **Hole Creation:** To create a hole, the 16-bit mask in the `MCNK` header must be updated. This bitmask is crucial for pathfinding and collision, as the client's physics engine respects holes even if the geometry is technically present.

### Skeletal Mesh Compilation (M2/SKIN)
1.  **Rigging:** Ensure the model uses a maximum of 4 bone influences per vertex.
2.  **LOD Generation:** Create the `.skin` files by selectively removing vertices and triangles. The engine expects `Model00.skin` to be the highest detail.
3.  **Submesh Sorting:** Submeshes must be assigned "geoset" IDs (e.g., 201 for boots, 101 for hair). This allows the game to toggle specific parts of the model for character customization.
4.  **Bounding Volumes:** The `CAaBox` and sphere radius in the M2 header must be accurately calculated. If the bounding box is too small, the model will flicker or disappear when viewed at certain angles due to frustum culling.

### Texture Pipeline (BLP)
1.  **Mipmap Generation:** Textures must have a full mipmap chain down to 1x1.
2.  **DXT Selection:** Use DXT1 for simple textures to save space. Use DXT3 for textures with sharp alpha transitions (like foliage) and DXT5 for smooth alpha gradients (like smoke).
3.  **Power of Two:** All textures must have dimensions that are powers of two (e.g., 256, 512, 1024).

---

## Hard Limits and Engine Constraints

The 3.3.5a engine is governed by several hardcoded limits that reflect the technological state of 2008. Custom content that exceeds these limits will cause client instability or failure to render.

| System | Limit | Detail |
| :--- | :--- | :--- |
| **M2 Bones** | 256 | Maximum bones in the global list per model. |
| **M2 Vertex Weights** | 4 | Max number of bones that can influence a single vertex. |
| **ADT Layers** | 4 | Max number of blended textures per terrain chunk. |
| **WMO Groups** | 512 | Maximum number of group files per root WMO. |
| **MPQ Archive Size** | 4 GB | Limit for the standard 32-bit archive size field. |
| **ADT Tile Count** | 4096 | The fixed 64x64 grid layout. |
| **MCNK Units** | 8x8 | Fixed grid density for terrain heightmap subdivisions. |

---

## Tooling and Community Intelligence

The reverse engineering of the 3.3.5a client is well-documented through several key community projects. **StormLib** remains the gold standard for MPQ interaction, supporting all encryption and hashing requirements. For model viewing and export, **WoW Model Viewer** provides a robust reference implementation of the M2 and SKIN formats. Terrain editors such as **Noggit** have successfully reverse-engineered the ADT and WDT structures to allow for world editing.

For developers building new tools, the primary edge case discovered in practice is the handling of **WDT-less maps**. Some small instances and battlegrounds do not use the 64x64 ADT grid but instead rely on a single global WMO placed at the origin. In these cases, the WDT file contains an `MWMO` chunk and a single `MODF` entry for the global structure.

Furthermore, the transition to Cataclysm (build 4.0.1+) introduced the splitting of ADT files into `_root.adt`, `_obj0.adt`, and `_tex0.adt`. Developers working strictly with 3.3.5a must ensure they do not accidentally adopt these later conventions, as the 3.3.5a client is incapable of processing the multi-file ADT structure.

By strictly following these binary specifications and mathematical formulas, implementers can develop a canonical technical knowledge base for build 3.3.5a. This allows for the programmatic generation and modification of the Azeroth world environment without the need for the original Blizzard toolset, enabling modern applications such as AI-assisted world building and advanced asset archival.
