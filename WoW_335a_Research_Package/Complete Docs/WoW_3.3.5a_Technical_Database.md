# World of Warcraft 3.3.5a (build 12340) Technical Database

This document provides a comprehensive technical reference for the file formats, data structures, and client systems of World of Warcraft patch 3.3.5a (build 12340). The information compiled herein is intended to assist developers in creating AI-native applications, procedural generation tools, model and map editors, and other utilities that interact with the game's data.

This database expands upon existing knowledge by integrating findings from a wide range of community-driven research and documentation. It covers everything from the fundamental archive and data file formats to the intricate systems that govern character models, spell visuals, and the in-game world itself.

## MPQ (MoPaQ) Archive Format

The MPQ (MoPaQ) format is a proprietary archive format used by Blizzard Entertainment for storing game data. It is used to package the various assets of the game, such as models, textures, sounds, and database files, into a compressed and manageable format.

### Signatures

- **MPQ\x1A**: Standard archive header.
- **MPQ\x1B**: Shunt header, used for extended archive information.

### Header

The MPQ header contains essential information about the archive, including its size, version, and the locations of the hash and block tables.

```c
struct MPQHeader {
    uint32_t magic;           // "MPQ\x1A"
    uint32_t headerSize;
    uint32_t archiveSize;
    uint16_t formatVersion;
    uint16_t blockSize;       // Size of each block in the archive, in bytes
    uint32_t hashTablePos;    // Offset to the hash table
    uint32_t blockTablePos;   // Offset to the block table
    uint32_t hashTableSize;   // Number of entries in the hash table
    uint32_t blockTableSize;  // Number of entries in the block table
};
```

### Hash Table Entry

The hash table is used to quickly locate files within the archive based on their hashed filename.

```c
struct MPQHash {
    uint32_t hashA;           // Primary hash of the filename
    uint32_t hashB;           // Secondary hash of the filename
    uint16_t locale;          // Language locale of the file
    uint16_t platform;        // Platform of the file
    uint32_t blockIndex;      // Index into the block table
};
```

### Block Table Entry

The block table contains information about each file's data block within the archive.

```c
struct MPQBlock {
    uint32_t offset;          // Offset of the block from the beginning of the archive
    uint32_t compressedSize;  // Size of the block when compressed
    uint32_t fileSize;        // Size of the file when decompressed
    uint32_t flags;           // Flags indicating the state of the file (e.g., compressed, encrypted)
};
```

### Common Flags

| Flag       | Description                                      |
|------------|--------------------------------------------------|
| 0x00000200 | File is compressed.                              |
| 0x00010000 | File is encrypted.                               |
| 0x00020000 | Encryption key is adjusted with file offset and size. |
| 0x80000000 | File is a single unit (not compressed or encrypted). |

### Tools

- **Ladik's MPQ Editor**: A widely used tool for creating, opening, and editing MPQ archives.

## DBC (DataBaseClient) File Format

DBC files are simple database files used by the client to store a wide variety of game data, such as item stats, spell effects, character information, and more. They are a critical component of the game's data architecture.

### Header

All DBC files begin with a standard header that defines the structure of the file.

```c
struct DBCHeader {
    char magic[4];          // "WDBC"
    uint32_t recordCount;     // Number of records in the file
    uint32_t fieldCount;      // Number of fields per record
    uint32_t recordSize;      // Size of each record in bytes
    uint32_t stringBlockSize; // Size of the string block at the end of the file
};
```

### Records and String Block

DBC files consist of a block of fixed-size records followed by a string block. The records contain the actual data, and any string values are stored in the string block. The record itself contains an offset to the location of the string in the string block. All strings are null-terminated.

### Key DBC File Structures

While there are over 200 DBC files in patch 3.3.5a, some of the most important ones include:

- **Spell.dbc**: Contains detailed information about every spell in the game.
- **Item.dbc**: Contains basic information about items.
- **CreatureDisplayInfo.dbc**: Defines the appearance of creatures.
- **CharSections.dbc**: Defines the different sections of a character model, such as skin, face, and hair.

### Tools

- **WDBX Editor**: A powerful tool for viewing and editing DBC, DB2, and other related client database files.
- **DBC Viewer/Editor**: A general term for various tools that provide basic viewing and editing capabilities for DBC files.
- **TrinityCore DBC Tools**: The TrinityCore project includes various command-line tools and scripts for working with DBC files.

## M2 Model Format

M2 files are the primary format for 3D models in World of Warcraft, used for characters, creatures, doodads, and items. These files contain the model's geometry, bones, animations, and references to textures and other associated data.

### Header

The M2 header, identified by the "MD20" magic number, contains metadata and arrays of offsets to various data blocks within the file.

```c
struct M2Header {
    uint32_t magic;                                     // "MD20"
    uint32_t version;                                   // 264 for 3.3.5a
    M2Array<char> name;                                 // Name of the model
    uint32_t global_flags;                              // Global flags for the model
    M2Array<M2Loop> global_loops;                       // Timestamps for global looping animations
    M2Array<M2Sequence> sequences;                      // Animation sequences
    M2Array<uint16_t> sequenceIdxHashById;              // Lookup table for sequences
    M2Array<M2CompBone> bones;                          // Bones and their animations
    M2Array<uint16_t> boneIndicesById;                  // Lookup table for key bones
    M2Array<M2Vertex> vertices;                         // Model vertices
    uint32_t num_skin_profiles;                         // Number of skin profiles (LODs)
    M2Array<M2Color> colors;                            // Color and alpha animations
    M2Array<M2Texture> textures;                        // Texture definitions
    M2Array<M2TextureWeight> texture_weights;           // Texture transparency animations
    M2Array<M2TextureTransform> texture_transforms;     // UV animations
    M2Array<M2Material> materials;                      // Material properties and blending modes
    M2Array<M2Attachment> attachments;                  // Attachment points for effects and items
    M2Array<M2Event> events;                            // Timeline events (e.g., sound triggers)
    M2Array<M2Light> lights;                            // Model-specific lights
    M2Array<M2Camera> cameras;                          // Model-specific cameras
    M2Array<M2Ribbon> ribbon_emitters;                  // Ribbon particle emitters
    M2Array<M2Particle> particle_emitters;              // Particle emitters
};
```

### Key Data Structures

- **M2Vertex**: Defines a single vertex, including its position, bone weights and indices, normal, and texture coordinates.
- **M2CompBone**: Defines a bone, its parent, and its animation tracks for translation, rotation, and scaling.
- **M2Sequence**: Defines an animation sequence, such as "Walk" or "Attack", including its duration and flags.
- **M2Texture**: Defines a texture used by the model, referencing a BLP file.
- **.skin Files**: Contain the Level of Detail (LOD) information for the model, defining which vertices and triangles are used for each LOD level.

### Tools

- **WoW Model Viewer**: The most popular tool for viewing and exploring M2 models and their animations.
- **010 Editor**: A hex editor with templates for the M2 format, allowing for low-level analysis.
- **Blender M2 import/export scripts**: Addons for Blender that allow for the import, editing, and export of M2 models.

## WMO (World Model Object) Format

WMO files are used for large, static objects in the game world, such as buildings, dungeons, and large environmental props. They are composed of a root file and one or more group files.

### Root and Group Files

The root WMO file contains metadata, material definitions, and references to textures and doodads. The group files contain the actual geometry for different parts of the model.

### Root File Chunks

- **MOHD**: The main header, containing counts for various data elements, the WMO's bounding box, and flags.
- **MOTX**: A list of texture filenames (BLP files) used in the WMO.
- **MOMT**: Defines the materials, referencing textures from MOTX and specifying blending modes and shaders.
- **MOGN**: A list of group names.
- **MOGI**: Information about each group, including its flags and bounding box.
- **MODS**: Doodad sets, which are collections of doodads that can be displayed in the WMO.
- **MODD**: Placement information for each doodad instance.

### Group File Chunks

- **MOGP**: The header for the group, containing flags, bounding box information, and references to portals.
- **MOVT**: The list of vertices for the group's geometry.
- **MONR**: The vertex normals.
- **MOTV**: The texture coordinates (UVs).
- **MOVI**: The vertex indices for the triangles.
- **MOBA**: Render batches, which group triangles that share the same material.
- **MOCV**: Vertex colors, used for pre-calculated lighting.
- **MOBN/MOBR**: The BSP (Binary Space Partitioning) tree, used for collision detection.

### Tools

- **WoW Model Viewer**: Can render WMO files, allowing for inspection of the model and its components.
- **Blender WMO import/export scripts**: Addons for Blender that enable the creation and modification of WMO files.

## ADT/WDT Terrain Format

The terrain in World of Warcraft is defined by a combination of WDT (World Definition Tile) and ADT (Area Definition Tile) files. The WDT file acts as a master index for a map, while the ADT files contain the detailed geometry and object placement for each individual map tile.

### WDT (World Definition Tile) File

The WDT file for a map contains a 64x64 grid that specifies which ADT tiles are present. It also contains global map information, such as whether the map uses a global WMO instead of terrain.

- **MPHD**: The main header, containing flags that affect the entire map.
- **MAIN**: A 64x64 grid of flags indicating whether an ADT file exists for that tile.
- **MWMO/MODF**: If the map is based on a single large model, these chunks define the WMO to use and its placement.

### ADT (Area Definition Tile) File

Each ADT file represents a single tile of the map and is composed of 256 (16x16) MCNK (Map Chunk) sub-chunks. The ADT file contains the following main chunks:

- **MHDR**: The main header, containing offsets to other chunks within the file.
- **MCIN**: An index of the 256 MCNK chunks, with offsets to each one.
- **MTEX**: A list of texture filenames used in the tile.
- **MMDX/MWMO**: Lists of M2 and WMO model filenames used as doodads and objects.
- **MDDF/MODF**: Placement information for all M2 and WMO instances in the tile.
- **MH2O**: Detailed information about liquids, such as water and lava.

### MCNK (Map Chunk)

Each MCNK chunk represents a 33.33x33.33 yard area of terrain and contains the following key sub-chunks:

- **MCVT**: The height map data, consisting of 145 float values.
- **MCNR**: The vertex normals for the terrain mesh.
- **MCLY**: Defines the texture layers and how they are blended.
- **MCAL**: Alpha maps used for blending the texture layers.
- **MCRF**: References to doodads and objects placed within the chunk.

### Tools

- **Noggit**: A popular map editor for World of Warcraft that allows for the creation and modification of ADT and WDT files.
- **AdtTools**: A C# framework for programmatically manipulating ADT files.

## BLP (Blizzard Picture) Texture Format

BLP files are the standard texture format used in World of Warcraft. They store image data with pre-calculated mipmaps and can be compressed using various methods.

### Header

The BLP header contains information about the texture's format, dimensions, and the location of its mipmap data.

```c
struct BLPHeader {
    char magic[4];          // "BLP2"
    uint32_t version;         // Always 1 for 3.3.5a
    uint8_t compression;    // Compression mode (1: Paletted, 2: DXT, 3: ARGB)
    uint8_t alphaDepth;     // Bit depth of the alpha channel
    uint8_t alphaType;      // Type of alpha compression
    uint8_t hasMipmaps;     // Whether the file has mipmaps
    uint32_t width;           // Width of the texture in pixels
    uint32_t height;          // Height of the texture in pixels
    uint32_t mipOffsets[16];  // Offsets to each mipmap level
    uint32_t mipSizes[16];    // Sizes of each mipmap level
};
```

### Compression Modes

- **Paletted**: The image data is indexed into a 256-color palette.
- **DXT**: The image data is compressed using DXT1, DXT3, or DXT5 compression, depending on the alpha channel.
- **ARGB**: The image data is stored as uncompressed 32-bit ARGB pixels.

### Tools

- **BLPConverter**: A tool for converting between BLP and other image formats, such as PNG or TGA.
- **WoW Model Viewer**: Can display BLP textures and export them to other formats.

## Character and Item Systems

The appearance of characters and how items are displayed on them is managed by a series of interconnected DBC files and the M2 model format.

### Character Customization

Character customization options, such as skin color, face, hair style, and facial hair, are defined in several DBC files:

- **ChrRaces.dbc**: Defines the playable races.
- **CharSections.dbc**: A key file that links race, gender, and appearance options to specific textures. It defines the base skin, face, facial hair, and hair textures for each character model.
- **CharacterFacialHairStyles.dbc**: Defines the different facial hair styles for each race and gender.
- **CharHairGeosets.dbc**: Defines the different hair styles and their corresponding geosets in the character model.

### Creature and Character Display

- **CreatureDisplayInfo.dbc**: This file is central to the appearance of all creatures and characters in the game. It references a `CreatureModelData.dbc` entry, which in turn points to an M2 model file. It also specifies textures, scale, and other visual properties.
- **CreatureDisplayInfoExtra.dbc**: For player characters and NPCs that use the player model, this file provides additional customization options, such as skin color, face, hair, and the display of equipped items.

### Item Display

- **ItemDisplayInfo.dbc**: This file defines how an item appears when equipped. It references M2 models for weapons, shields, and helmets, and it specifies the textures and geosets to be used for other armor pieces. It also links to visual effects for enchantments.
- **HelmetGeosetVisData.dbc**: This file contains information about which character geosets (like hair or beards) should be hidden when a helmet is equipped.

## Spell Visual System

The visual effects of spells are orchestrated by a combination of DBC files that define the models, animations, and attachments used for each part of a spell cast.

- **Spell.dbc**: The entry for each spell contains references to a `SpellVisual.dbc` record.
- **SpellVisual.dbc**: This file is the core of the spell visual system. It defines the visual kits to be used for the precast, cast, impact, and state effects of a spell. It also defines the model and path of any missile associated with the spell.
- **SpellVisualKit.dbc**: This file links a visual kit to specific models and attachment points. It defines which `SpellVisualEffectName.dbc` entries are used for different parts of a model (e.g., head, hands, chest) and can also trigger character procedures, such as scaling or coloring the model.
- **SpellVisualEffectName.dbc**: This file contains a list of M2 model files that are used as the actual visual effects.
## Client and Network

### Client Memory and Offsets

The World of Warcraft client maintains a wealth of information in its memory, which can be accessed through various offsets from the process's base address. Key data structures include the Object Manager, which tracks all in-game objects.

- **Object Manager**: A linked list of all objects currently active in the game world, including players, units, and game objects.
- **Unit and Player Structures**: These structures contain detailed information about a unit's health, mana, level, and other stats.

### Network Protocol

The communication between the client and the server is handled through a TCP-based protocol using encrypted and compressed packets.

- **Login and Realm Server**: The initial connection is made to the login server for authentication, followed by a connection to the realm server for character selection and entry into the game world.
- **World Packets**: Communication with the world server is done through world packets, which have a header containing the packet size and an opcode that identifies the message type. Server-to-client (SMSG) and client-to-server (CMSG) packets have slightly different header structures.

## Procedural Generation

While the concept of procedurally generating content like maps and dungeons is a key interest for modern game development, research into the WoW 3.3.5a client and server architecture indicates that procedural generation was not a technique used by Blizzard for creating the game's world. Dungeons, raids, and the open world were all manually constructed by developers using the tools and file formats described in this document.

However, the file formats themselves, particularly the ADT/WDT terrain formats, are structured in a way that could allow for the development of third-party procedural generation tools. By programmatically creating valid ADT and WDT files, it is possible to generate new custom terrain for use in private server environments.

## References

This document was compiled using information from a wide range of community-driven resources. The following sources were instrumental in providing the technical details and specifications presented here.

[1] wowdev.wiki - A comprehensive wiki for World of Warcraft file formats and data structures.
[2] OwnedCore - A large community forum for MMOs, with extensive discussions on memory editing and bot development.
[3] UnknownCheats - A forum dedicated to game hacking and reverse engineering, with valuable information on client offsets and structures.
[4] TrinityCore - An open-source MMORPG framework that provides a wealth of information on server-side systems and database structures.
[5] WoWWiki (Archive) - An archived version of the original WoWWiki, containing a vast amount of API documentation and game information.
[6] GitHub Repositories - Various open-source projects related to WoW modding, model viewing, and file format parsing.
