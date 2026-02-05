# WoW Modding Tools Research

This document contains the findings from research on various WoW 3.3.5a modding tools.

## DBC File Format

### Structure

```c
struct dbc_header
{
  uint32_t magic; // always 'WDBC'
  uint32_t record_count; // records per file
  uint32_t field_count; // fields per record
  uint32_t record_size; // sum (sizeof (field_type_i)) | 0 <= i < field_count. field_type_i is NOT defined in the files.
  uint32_t string_block_size;
};

template<typename record_type>
struct dbc_file
{
  dbc_header header;
  // static_assert (header.record_size == sizeof (record_type));
  record_type records[header.record_count];
  char string_block[header.string_block_size];
};
```

### String Block

DBC records can contain strings. Strings are not stored in record but in an additional string block. A record contains an offset into that block. Strings are zero terminated (c strings) and might be zero length. A zero length string then only contains one byte being zero.

### Localization

DBC records can contain localized strings. Localized strings are a set of one string block offset per locale plus a bitmask up to Cataclysm. Beginning with Cataclysm, there only is one field containing only one string offset, thus disallowing providing multiple locales in one file.


## WoW Model Viewer (WMVx)

A fork of the original WoW Model Viewer, WMVx provides support for multiple client versions, including 3.3.5a. It is a 64-bit application with a simplified build setup.

### Features

*   Image Export (Basic)
*   3D Export (FBX)
*   Client Detection

### Supported Expansions

*   Vanilla (1.12.1)
*   TBC (2.4.3)
*   WOLTK (3.3.5)
*   Cata (4.3.4)
*   BFA (8.3.7)
*   SL (9.x)
*   DF (10.x)
*   TWW (11.x)

### Known Issues (WotLK)

*   Texture animations don't appear to work/show.
*   Texture transparencies don't appear to be correct.

## Ladik's MPQ Editor

An editor for MPQ archives with a Windows Explorer-style interface. It allows for creating, extracting, renaming, and deleting files within MPQ archives, as well as file compression. It supports all known versions of MPQs and works on Windows, ReactOS, and Wine.

### Features

*   Execute files directly from an archive.
*   Supports all known MPQ versions.
*   Available in multiple languages.

## BLP File Format

BLP files store textures with precalculated mipmaps. They can be stored in palettized or RGB formats, with varying alpha bit depths. RGB formats are compressed using DXT1 or DXT3.

### Header

```c
struct BLPHeader {
  uint32_t magic; // 'BLP2'
  uint32_t formatVersion; // must be 1
  BLPColorEncoding colorEncoding;
  uint8_t alphaChannelBitDepth; // 0, 1, 8, 4
  BLPPixelFormat preferredFormat;
  mipmap_level_and_flag_type mipmap_level_and_flags;
  uint32_t width;
  uint32_t height;
#define MIPMAP_COUNT 16
#define PALETTE_SIZE 256
  uint32_t dataOffsets[MIPMAP_COUNT];
  uint32_t dataSizes[MIPMAP_COUNT];
  uint32_t palette[PALETTE_SIZE];
};
```

### Enums

```c
enum BLPColorEncoding : uint8_t {
    COLOR_JPEG = 0, // not supported
    COLOR_PALETTE = 1,
    COLOR_DXT = 2,
    COLOR_ARGB8888 = 3,
    COLOR_ARGB8888_dup = 4,    // same decompression, likely other PIXEL_FORMAT
};

enum BLPPixelFormat : uint8_t {
    PIXEL_DXT1 = 0,
    PIXEL_DXT3 = 1,
    PIXEL_ARGB8888 = 2,
    PIXEL_ARGB1555 = 3,
    PIXEL_ARGB4444 = 4,
    PIXEL_RGB565 = 5,
    PIXEL_A8 = 6,
    PIXEL_DXT5 = 7,
    PIXEL_UNSPECIFIED = 8,
    PIXEL_ARGB2565 = 9,
    PIXEL_BC5 = 11, // DXGI_FORMAT_BC5_UNORM 
    NUM_PIXEL_FORMATS = 12, // (no idea if format=10 exists)
};

enum mipmap_level_and_flag_type : uint8_t // MIPS_TYPE
{
  MIPS_NONE = 0x0,
  MIPS_GENERATED = 0x1,
  MIPS_HANDMADE = 0x2, // not supported
  flags_mipmap_mask = 0xF, // level
  flags_unk_0x10 = 0x10,
};
```

## WDBX Editor

A communal program to edit all variants of Blizzard's client db files. It has full support for reading and saving all release versions of DBC, DB2, WDB, ADB, and DBCache.

### Features

*   Full support for release versions of DBC, DB2, WDB, ADB, and DBCache.
*   Open files from both MPQ archives and CASC directories.
*   Standard CRUD operations, plus undo/redo.
*   Export to SQL, CSV, and MPQ.
*   Import from SQL and CSV.
*   Find and Replace.
*   Definition editor for maintaining file structure definitions.
## M2 File Format

M2 files contain model objects, including vertices, faces, materials, textures, animations, and other properties.

### Header

```c
struct M2Header
{
  uint32_t magic;              // "MD20"
  uint32_t version;
  M2Array<char> name;
  struct
  {
    uint32_t flag_tilt_x : 1;
    uint32_t flag_tilt_y : 1;
    uint32_t : 1;
    uint32_t flag_use_texture_combiner_combos : 1;
    uint32_t : 1;
    uint32_t flag_load_phys_data : 1;
    uint32_t : 1;
    uint32_t flag_unk_0x80 : 1;
    uint32_t flag_camera_related : 1;
    uint32_t flag_new_particle_record : 1;
    uint32_t flag_unk_0x400 : 1;
    uint32_t flag_texture_transforms_use_bone_sequences : 1;
    uint32_t flag_unk_0x1000 : 1;
    uint32_t ChunkedAnimFiles_0x2000 : 1;
    uint32_t flag_unk_0x4000 : 1;
    uint32_t flag_unk_0x8000 : 1;
    uint32_t flag_unk_0x10000 : 1;
    uint32_t flag_unk_0x20000 : 1;
    uint32_t flag_unk_0x40000 : 1;
    uint32_t flag_unk_0x80000 : 1;
    uint32_t flag_unk_0x100000 : 1;
    uint32_t flag_unk_0x200000 : 1;
    uint32_t flag_unk_0x40000000 : 1;
  } global_flags;
  M2Array<M2Loop> global_loops;
  M2Array<M2Sequence> sequences;
  M2Array<uint16_t> sequenceIdxHashById;
  M2Array<M2CompBone> bones;
  M2Array<uint16_t> boneIndicesById;
  M2Array<M2Vertex> vertices;
  uint32_t num_skin_profiles;
  M2Array<M2Color> colors;
  M2Array<M2Texture> textures;
  M2Array<M2TextureWeight> texture_weights;
  M2Array<M2TextureTransform> texture_transforms;
  M2Array<uint16_t> textureIndicesById;
  M2Array<M2Material> materials;
  M2Array<uint16_t> boneCombos;
  M2Array<uint16_t> textureCombos;
  M2Array<uint16_t> textureCoordCombos;
  M2Array<uint16_t> textureWeightCombos;
  M2Array<uint16_t> textureTransformCombos;
  CAaBox bounding_box;
  float bounding_sphere_radius;
  CAaBox collision_box;
  float collision_sphere_radius;
  M2Array<uint16_t> collisionIndices;
  M2Array<C3Vector> collisionPositions;
  M2Array<C3Vector> collisionFaceNormals;
  M2Array<M2Attachment> attachments;
  M2Array<uint16_t> attachmentIndicesById;
  M2Array<M2Event> events;
  M2Array<M2Light> lights;
  M2Array<M2Camera> cameras;
  M2Array<uint16_t> cameraIndicesById;
  M2Array<M2Ribbon> ribbon_emitters;
  M2Array<M2Particle> particle_emitters;
};
```
