# World of Warcraft 3.3.5a (Build 12340) Complete Technical Reference

**Author:** Manus AI  
**Version:** 1.0  
**Date:** January 2026

---

## Table of Contents

1. [Global Binary Conventions](#global-binary-conventions)
2. [MPQ Archive Format](#mpq-mopaq-archive-format)
3. [DBC File Format](#dbc-databaseclient-file-format)
4. [M2 Model Format](#m2-model-format)
5. [Skin and LOD Files](#skin-and-lod-files)
6. [WMO Format](#wmo-world-model-object-format)
7. [ADT Terrain Format](#adt-terrain-format)
8. [WDT World Definition Format](#wdt-world-definition-format)
9. [BLP Texture Format](#blp-texture-format)
10. [World Coordinate System](#world-coordinate-system)
11. [Character Customization System](#character-customization-system)
12. [Spell Visual System](#spell-visual-system)
13. [Sound and Music System](#sound-and-music-system)
14. [Navigation and Pathfinding](#navigation-and-pathfinding)
15. [Client Memory and Offsets](#client-memory-and-offsets)
16. [Network Protocol](#network-protocol)
17. [Lua AddOn API](#lua-addon-api)
18. [Modding Tools Reference](#modding-tools-reference)
19. [Hard Limits](#hard-limits)
20. [References](#references)

---

## Global Binary Conventions

All multi-byte values in World of Warcraft file formats are **little-endian** unless otherwise specified.

All chunked formats use the following structure:

```c
struct ChunkHeader {
    char id[4];      // Four-character identifier (e.g., "MVER", "MCNK")
    uint32_t size;   // Size of the chunk data (excluding this header)
};
```

---

## MPQ (MoPaQ) Archive Format

The MPQ format is a proprietary archive format used by Blizzard Entertainment for storing game assets. It provides compression, encryption, and efficient file lookup through hash tables.

### Signatures

| Signature | Description |
|-----------|-------------|
| `MPQ\x1A` | Standard archive header |
| `MPQ\x1B` | Shunt header (extended archive information) |

### Header Structure

```c
struct MPQHeader {
    uint32_t magic;           // "MPQ\x1A" (0x1A51504D)
    uint32_t headerSize;      // Size of the header
    uint32_t archiveSize;     // Size of the entire archive
    uint16_t formatVersion;   // Format version (0 or 1)
    uint16_t blockSize;       // Size of file blocks as power of 2
    uint32_t hashTablePos;    // Offset to the hash table
    uint32_t blockTablePos;   // Offset to the block table
    uint32_t hashTableSize;   // Number of entries in hash table
    uint32_t blockTableSize;  // Number of entries in block table
};
```

### Hash Table Entry

```c
struct MPQHash {
    uint32_t hashA;       // First hash of filename (HashString with type 0x100)
    uint32_t hashB;       // Second hash of filename (HashString with type 0x200)
    uint16_t locale;      // Language locale
    uint16_t platform;    // Platform (0 for default)
    uint32_t blockIndex;  // Index into block table, or 0xFFFFFFFF if empty
};
```

### Block Table Entry

```c
struct MPQBlock {
    uint32_t offset;          // Offset of file data from archive start
    uint32_t compressedSize;  // Compressed file size
    uint32_t fileSize;        // Uncompressed file size
    uint32_t flags;           // File flags
};
```

### Block Flags

| Flag | Value | Description |
|------|-------|-------------|
| `MPQ_FILE_IMPLODE` | 0x00000100 | File is imploded (PKWARE compression) |
| `MPQ_FILE_COMPRESS` | 0x00000200 | File is compressed |
| `MPQ_FILE_ENCRYPTED` | 0x00010000 | File is encrypted |
| `MPQ_FILE_FIX_KEY` | 0x00020000 | Encryption key adjusted with file offset and size |
| `MPQ_FILE_SINGLE_UNIT` | 0x01000000 | File is a single unit |
| `MPQ_FILE_DELETE_MARKER` | 0x02000000 | File is a deletion marker |
| `MPQ_FILE_SECTOR_CRC` | 0x04000000 | File has sector CRC values |
| `MPQ_FILE_EXISTS` | 0x80000000 | File exists |

### Filename Normalization

Before hashing, filenames are normalized:
1. Convert to uppercase
2. Replace forward slashes (`/`) with backslashes (`\`)

### Hashing Algorithm

Three hashes are computed per filename using different hash types:
- **HASH_OFFSET** (type 0x000): Used to find initial position in hash table
- **HASH_A** (type 0x100): First verification hash
- **HASH_B** (type 0x200): Second verification hash

Collision resolution uses linear probing.

---

## DBC (DataBaseClient) File Format

DBC files store tabular game data used by the client. They follow a simple fixed-record format with an optional string block.

### Header Structure

```c
struct DBCHeader {
    char magic[4];            // "WDBC" (0x43424457)
    uint32_t recordCount;     // Number of records
    uint32_t fieldCount;      // Number of fields per record
    uint32_t recordSize;      // Size of each record in bytes
    uint32_t stringBlockSize; // Size of the string block
};
```

### File Layout

```
[DBCHeader]
[Record 0]
[Record 1]
...
[Record N-1]
[String Block]
```

### String References

String fields contain 32-bit offsets into the string block. Strings are null-terminated C strings. A zero offset typically indicates an empty string.

### Localization

Localized strings use 17 consecutive fields: 16 string offsets (one per locale) plus a bitmask indicating which locales are present.

### Complete DBC File List (3.3.5a)

The following table lists all DBC files present in patch 3.3.5a:

| Category | Files |
|----------|-------|
| **Achievement** | Achievement, Achievement_Category, Achievement_Criteria |
| **Animation** | AnimationData, AttackAnimKits, AttackAnimTypes |
| **Area** | AreaGroup, AreaPOI, AreaTable, AreaTrigger, WMOAreaTable |
| **Character** | CharBaseInfo, CharHairGeosets, CharHairTextures, CharSections, CharStartOutfit, CharTitles, CharVariations, CharacterFacialHairStyles |
| **Class/Race** | ChrClasses, ChrRaces |
| **Creature** | CreatureDisplayInfo, CreatureDisplayInfoExtra, CreatureFamily, CreatureModelData, CreatureMovementInfo, CreatureSoundData, CreatureSpellData, CreatureType |
| **Item** | Item, ItemBagFamily, ItemClass, ItemCondExtCosts, ItemDisplayInfo, ItemExtendedCost, ItemGroupSounds, ItemLimitCategory, ItemPetFood, ItemPurchaseGroup, ItemRandomProperties, ItemRandomSuffix, ItemSet, ItemSubClass, ItemSubClassMask, ItemVisualEffects, ItemVisuals |
| **Map** | Map, MapDifficulty, DungeonMap, DungeonMapChunk, WorldMapArea, WorldMapContinent, WorldMapOverlay, WorldMapTransforms |
| **Spell** | Spell, SpellCastTimes, SpellCategory, SpellChainEffects, SpellDescriptionVariables, SpellDifficulty, SpellDispelType, SpellDuration, SpellEffectCameraShakes, SpellFocusObject, SpellIcon, SpellItemEnchantment, SpellItemEnchantmentCondition, SpellMechanic, SpellMissile, SpellMissileMotion, SpellRadius, SpellRange, SpellRuneCost, SpellShapeshiftForm, SpellVisual, SpellVisualEffectName, SpellVisualKit, SpellVisualKitAreaModel, SpellVisualKitModelAttach, SpellVisualPrecastTransitions |
| **Sound** | SoundAmbience, SoundEmitters, SoundEntries, SoundEntriesAdvanced, SoundFilter, SoundFilterElem, SoundProviderPreferences, SoundSamplePreferences, SoundWaterType, ZoneMusic, ZoneIntroMusicTable |
| **Talent** | Talent, TalentTab |
| **Taxi** | TaxiNodes, TaxiPath, TaxiPathNode |
| **Vehicle** | Vehicle, VehicleSeat, VehicleUIIndSeat, VehicleUIIndicator |
| **Other** | AuctionHouse, BankBagSlotPrices, BannedAddOns, BarberShopStyle, BattlemasterList, CameraShakes, Cfg_Categories, Cfg_Configs, ChatChannels, ChatProfanity, CinematicCamera, CinematicSequences, CurrencyCategory, CurrencyTypes, DanceMoves, DeathThudLookups, DeclinedWord, DeclinedWordCases, DestructibleModelData, DungeonEncounter, DurabilityCosts, DurabilityQuality, Emotes, EmotesText, EmotesTextData, EmotesTextSound, EnvironmentalDamage, Exhaustion, Faction, FactionGroup, FactionTemplate, FileData, FootprintTextures, FootstepTerrainLookup, GameObjectArtKit, GameObjectDisplayInfo, GameTables, GameTips, GemProperties, GlyphProperties, GlyphSlot, GroundEffectDoodad, GroundEffectTexture, GtBarberShopCostBase, GtChanceToMeleeCrit, GtChanceToMeleeCritBase, GtChanceToSpellCrit, GtChanceToSpellCritBase, GtCombatRatings, GtNPCManaCostScaler, GtOCTClassCombatRatingScalar, GtOCTRegenHP, GtOCTRegenMP, GtRegenHPPerSpt, GtRegenMPPerSpt, HelmetGeosetVisData, HolidayDescriptions, HolidayNames, Holidays, LanguageWords, Languages, LfgDungeonGroup, LfgDungeons, LFGDungeonExpansion, Light, LightFloatBand, LightIntBand, LightParams, LightSkybox, LiquidMaterial, LiquidType, LoadingScreenTaxiSplines, LoadingScreens, Lock, LockType, MailTemplate, Material, Movie, MovieFileData, MovieVariation, NameGen, NamesProfanity, NamesReserved, NPCSounds, ObjectEffect, ObjectEffectGroup, ObjectEffectModifier, ObjectEffectPackage, ObjectEffectPackageElem, OverrideSpellData, Package, PageTextMaterial, PaperDollItemFrame, ParticleColor, PetPersonality, PetitionType, PowerDisplay, PvpDifficulty, QuestFactionReward, QuestInfo, QuestSort, QuestXP, RandPropPoints, Resistances, ScalingStatDistribution, ScalingStatValues, ScreenEffect, ServerMessages, SheatheSoundLookups, SkillCostsData, SkillLine, SkillLineAbility, SkillLineCategory, SkillRaceClassInfo, SkillTiers, SpamMessages, StableSlotPrices, Startup_Strings, Stationery, StringLookups, SummonProperties, TeamContributionPoints, TerrainType, TerrainTypeSounds, TotemCategory, TransportAnimation, TransportPhysics, TransportRotation, UISoundLookups, UnitBlood, UnitBloodLevels, VideoHardware, VocalUISounds, WeaponImpactSounds, WeaponSwingSounds2, Weather, WorldChunkSounds, WorldSafeLocs, WorldStateUI, WorldStateZoneSounds, WowError_Strings |

---

## M2 Model Format

M2 files contain 3D models used for characters, creatures, doodads, and items. The format stores geometry, bones, animations, textures, and particle effects.

### Header Structure

```c
struct M2Header {
    uint32_t magic;                                       // "MD20" (0x3032444D)
    uint32_t version;                                     // 264 for 3.3.5a
    M2Array<char> name;                                   // Model name
    
    struct GlobalFlags {
        uint32_t flag_tilt_x : 1;                         // 0x001
        uint32_t flag_tilt_y : 1;                         // 0x002
        uint32_t : 1;
        uint32_t flag_use_texture_combiner_combos : 1;    // 0x008
        uint32_t : 1;
        uint32_t flag_load_phys_data : 1;                 // 0x020
        uint32_t : 1;
        uint32_t flag_unk_0x80 : 1;                       // 0x080
        uint32_t flag_camera_related : 1;                 // 0x100
        uint32_t flag_new_particle_record : 1;            // 0x200
        uint32_t flag_unk_0x400 : 1;                      // 0x400
        uint32_t flag_texture_transforms_use_bone_sequences : 1; // 0x800
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

### M2Array Structure

```c
template<typename T>
struct M2Array {
    uint32_t count;   // Number of elements
    uint32_t offset;  // Offset from start of file to data
};
```

### Vertex Structure

```c
struct M2Vertex {
    C3Vector pos;           // Position (x, y, z)
    uint8_t bone_weights[4]; // Bone weights (sum must equal 255)
    uint8_t bone_indices[4]; // Bone indices
    C3Vector normal;        // Normal vector
    C2Vector tex_coords[2]; // Texture coordinates (u, v)
};
```

### Bone Structure

```c
struct M2CompBone {
    int32_t key_bone_id;            // Key bone lookup index, or -1
    uint32_t flags;                 // Bone flags
    int16_t parent_bone;            // Parent bone index, or -1 for root
    uint16_t submesh_id;            // Mesh part ID
    union {
        struct {
            uint16_t screen_size_perc_start;
            uint16_t screen_size_perc_end;
        } billboard;
        uint32_t boneNameCRC;
    };
    M2Track<C3Vector> translation;  // Translation animation track
    M2Track<C4Quaternion> rotation; // Rotation animation track
    M2Track<C3Vector> scaling;      // Scale animation track
    C3Vector pivot;                 // Pivot point
};
```

### Bone Flags

| Flag | Value | Description |
|------|-------|-------------|
| `IGNORE_PARENT_TRANSLATE` | 0x008 | Ignore parent bone's translation |
| `IGNORE_PARENT_SCALE` | 0x010 | Ignore parent bone's scale |
| `IGNORE_PARENT_ROTATION` | 0x020 | Ignore parent bone's rotation |
| `SPHERICAL_BILLBOARD` | 0x040 | Spherical billboard |
| `CYLINDRICAL_BILLBOARD_X` | 0x080 | Cylindrical billboard (lock X) |
| `CYLINDRICAL_BILLBOARD_Y` | 0x100 | Cylindrical billboard (lock Y) |
| `TRANSFORMED` | 0x200 | Transformed |
| `KINEMATIC_BONE` | 0x400 | Kinematic bone |
| `HELMET_ANIM_SCALED` | 0x1000 | Helmet animation scaled |

### Animation Sequence Structure

```c
struct M2Sequence {
    uint16_t id;              // Animation ID (references AnimationData.dbc)
    uint16_t variationIndex;  // Sub-animation index
    uint32_t duration;        // Duration in milliseconds
    float movespeed;          // Movement speed
    uint32_t flags;           // Animation flags
    int16_t frequency;        // Playback frequency
    uint16_t padding;
    M2Range replay;           // Replay range
    uint32_t blendTime;       // Blend time in milliseconds
    M2Bounds bounds;          // Bounding box
    int16_t variationNext;    // Next variation index
    uint16_t aliasNext;       // Alias next animation
};
```

### Animation Track Structure

```c
struct M2TrackBase {
    uint16_t interpolationType;  // 0=none, 1=linear, 2=hermite, 3=bezier
    uint16_t globalSequence;     // Global sequence index, or -1
    M2Array<M2Array<uint32_t>> timestamps;
};

template<typename T>
struct M2Track : M2TrackBase {
    M2Array<M2Array<T>> values;
};
```

### Interpolation Types

| Value | Type | Description |
|-------|------|-------------|
| 0 | None | No interpolation (step) |
| 1 | Linear | Linear interpolation |
| 2 | Hermite | Hermite spline interpolation |
| 3 | Bezier | Bezier spline interpolation |

### Material Structure

```c
struct M2Material {
    uint16_t flags;         // Render flags
    uint16_t blending_mode; // Blending mode
};
```

### Material Flags

| Flag | Value | Description |
|------|-------|-------------|
| `UNLIT` | 0x01 | Disable lighting |
| `UNFOGGED` | 0x02 | Disable fog |
| `TWO_SIDED` | 0x04 | Two-sided rendering |
| `DEPTH_TEST` | 0x08 | Enable depth testing |
| `DEPTH_WRITE` | 0x10 | Enable depth writing |

### Texture Structure

```c
struct M2Texture {
    uint32_t type;              // Texture type
    uint32_t flags;             // Texture flags
    M2Array<char> filename;     // Texture filename
};
```

### Texture Types

| Value | Type | Description |
|-------|------|-------------|
| 0 | HARDCODED | Filename in M2 file |
| 1 | SKIN | Character skin |
| 2 | OBJECT_SKIN | Object skin |
| 3 | WEAPON_BLADE | Weapon blade |
| 4 | WEAPON_HANDLE | Weapon handle |
| 5 | ENVIRONMENT | Environment map |
| 6 | HAIR | Character hair |
| 7 | FACIAL_HAIR | Character facial hair |
| 8 | SKIN_EXTRA | Extra skin texture |
| 9 | UI_SKIN | UI skin |
| 10 | TAUREN_MANE | Tauren mane |
| 11 | MONSTER_SKIN_1 | Monster skin 1 |
| 12 | MONSTER_SKIN_2 | Monster skin 2 |
| 13 | MONSTER_SKIN_3 | Monster skin 3 |

### Attachment Structure

```c
struct M2Attachment {
    uint32_t id;                    // Attachment ID
    uint16_t bone;                  // Bone index
    uint16_t unknown;
    C3Vector position;              // Position relative to bone
    M2Track<uint8_t> animate_attached; // Animation track
};
```

### Attachment IDs

| ID | Name | Description |
|----|------|-------------|
| 0 | SHIELD | Shield mount point |
| 1 | HAND_RIGHT | Right hand |
| 2 | HAND_LEFT | Left hand |
| 3 | ELBOW_RIGHT | Right elbow |
| 4 | ELBOW_LEFT | Left elbow |
| 5 | SHOULDER_RIGHT | Right shoulder |
| 6 | SHOULDER_LEFT | Left shoulder |
| 7 | KNEE_RIGHT | Right knee |
| 8 | KNEE_LEFT | Left knee |
| 9 | HIP_RIGHT | Right hip |
| 10 | HIP_LEFT | Left hip |
| 11 | HELM | Helmet |
| 12 | BACK | Back |
| 13 | SHOULDER_FLAP_RIGHT | Right shoulder flap |
| 14 | SHOULDER_FLAP_LEFT | Left shoulder flap |
| 15 | CHEST_BLOOD_FRONT | Chest blood (front) |
| 16 | CHEST_BLOOD_BACK | Chest blood (back) |
| 17 | BREATH | Breath |
| 18 | PLAYER_NAME | Player name |
| 19 | BASE | Base |
| 20 | HEAD | Head |
| 21 | SPELL_LEFT_HAND | Spell left hand |
| 22 | SPELL_RIGHT_HAND | Spell right hand |
| 23 | SPECIAL_1 | Special 1 |
| 24 | SPECIAL_2 | Special 2 |
| 25 | SPECIAL_3 | Special 3 |
| 26 | SHEATH_MAIN_HAND | Sheath main hand |
| 27 | SHEATH_OFF_HAND | Sheath off hand |
| 28 | SHEATH_SHIELD | Sheath shield |
| 29 | PLAYER_NAME_MOUNTED | Player name (mounted) |
| 30 | LARGE_WEAPON_LEFT | Large weapon left |
| 31 | LARGE_WEAPON_RIGHT | Large weapon right |
| 32 | HIP_WEAPON_LEFT | Hip weapon left |
| 33 | HIP_WEAPON_RIGHT | Hip weapon right |
| 34 | CHEST | Chest |
| 35 | HAND_ARROW | Hand arrow |

### Light Structure

```c
struct M2Light {
    uint16_t type;                      // Light type (0=directional, 1=point)
    int16_t bone;                       // Bone index, or -1
    C3Vector position;                  // Position
    M2Track<C3Vector> ambient_color;    // Ambient color track
    M2Track<float> ambient_intensity;   // Ambient intensity track
    M2Track<C3Vector> diffuse_color;    // Diffuse color track
    M2Track<float> diffuse_intensity;   // Diffuse intensity track
    M2Track<float> attenuation_start;   // Attenuation start track
    M2Track<float> attenuation_end;     // Attenuation end track
    M2Track<uint8_t> visibility;        // Visibility track
};
```

### Camera Structure

```c
struct M2Camera {
    uint32_t type;                              // Camera type
    float far_clip;                             // Far clipping plane
    float near_clip;                            // Near clipping plane
    M2Track<M2SplineKey<C3Vector>> positions;   // Position track
    C3Vector position_base;                     // Base position
    M2Track<M2SplineKey<C3Vector>> target_position; // Target position track
    C3Vector target_position_base;              // Base target position
    M2Track<M2SplineKey<float>> roll;           // Roll track
    M2Track<M2SplineKey<float>> FoV;            // Field of view track
};
```

### Ribbon Emitter Structure

```c
struct M2Ribbon {
    uint32_t ribbonId;                  // Ribbon ID
    uint32_t boneIndex;                 // Bone index
    C3Vector position;                  // Position
    M2Array<uint16_t> textureIndices;   // Texture indices
    M2Array<uint16_t> materialIndices;  // Material indices
    M2Track<C3Vector> colorTrack;       // Color track
    M2Track<fixed16> alphaTrack;        // Alpha track
    M2Track<float> heightAboveTrack;    // Height above track
    M2Track<float> heightBelowTrack;    // Height below track
    float edgesPerSecond;               // Edges per second
    float edgeLifetime;                 // Edge lifetime
    float gravity;                      // Gravity
    uint16_t textureRows;               // Texture rows
    uint16_t textureCols;               // Texture columns
    M2Track<uint16_t> texSlotTrack;     // Texture slot track
    M2Track<uint8_t> visibilityTrack;   // Visibility track
    int16_t priorityPlane;              // Priority plane
    uint16_t padding;
};
```

---

## Skin and LOD Files

Skin files (`.skin`) contain Level of Detail (LOD) information for M2 models. Each M2 model can have up to 4 skin files: `Model00.skin`, `Model01.skin`, `Model02.skin`, and `Model03.skin`.

### Skin Header

```c
struct SkinHeader {
    char magic[4];                  // "SKIN"
    M2Array<uint16_t> vertices;     // Vertex indices
    M2Array<uint16_t> indices;      // Triangle indices
    M2Array<uint32_t> bones;        // Bone indices
    M2Array<M2SkinSection> submeshes; // Submesh definitions
    M2Array<M2Batch> batches;       // Render batches
    uint32_t boneCountMax;          // Maximum bones per draw call
};
```

### Skin Section Structure

```c
struct M2SkinSection {
    uint16_t skinSectionId;     // Mesh part ID
    uint16_t level;             // LOD level
    uint16_t vertexStart;       // Starting vertex
    uint16_t vertexCount;       // Number of vertices
    uint16_t indexStart;        // Starting index
    uint16_t indexCount;        // Number of indices
    uint16_t boneCount;         // Number of bones
    uint16_t boneComboIndex;    // Starting bone combo index
    uint16_t boneInfluences;    // Number of bone influences
    uint16_t centerBoneIndex;   // Center bone index
    C3Vector centerPosition;    // Center position
    C3Vector sortCenterPosition; // Sort center position
    float sortRadius;           // Sort radius
};
```

### Batch Structure

```c
struct M2Batch {
    uint8_t flags;              // Batch flags
    int8_t priorityPlane;       // Priority plane
    uint16_t shader_id;         // Shader ID
    uint16_t skinSectionIndex;  // Skin section index
    uint16_t geosetIndex;       // Geoset index
    uint16_t colorIndex;        // Color index
    uint16_t materialIndex;     // Material index
    uint16_t materialLayer;     // Material layer
    uint16_t textureCount;      // Number of textures
    uint16_t textureComboIndex; // Texture combo index
    uint16_t textureCoordComboIndex; // Texture coord combo index
    uint16_t textureWeightComboIndex; // Texture weight combo index
    uint16_t textureTransformComboIndex; // Texture transform combo index
};
```

---

## WMO (World Model Object) Format

WMO files are used for large static structures like buildings, dungeons, and caves. They consist of a root file and multiple group files.

### Root File Chunks

| Chunk | Description |
|-------|-------------|
| MVER | Version (17 for 3.3.5a) |
| MOHD | Header |
| MOTX | Texture filenames |
| MOMT | Materials |
| MOGN | Group names |
| MOGI | Group information |
| MOSB | Skybox filename |
| MOPV | Portal vertices |
| MOPT | Portal information |
| MOPR | Portal references |
| MOVV | Visible block vertices |
| MOVB | Visible block list |
| MOLT | Lights |
| MODS | Doodad sets |
| MODN | Doodad names |
| MODD | Doodad definitions |
| MFOG | Fog settings |
| MCVP | Convex volume planes |

### MOHD Header Structure

```c
struct SMOHeader {
    uint32_t nTextures;       // Number of textures
    uint32_t nGroups;         // Number of groups
    uint32_t nPortals;        // Number of portals
    uint32_t nLights;         // Number of lights
    uint32_t nDoodadNames;    // Number of doodad names
    uint32_t nDoodadDefs;     // Number of doodad definitions
    uint32_t nDoodadSets;     // Number of doodad sets
    uint32_t ambColor;        // Ambient color (BGRA)
    uint32_t wmoID;           // WMO ID (WMOAreaTable.dbc)
    CAaBox boundingBox;       // Bounding box
    uint16_t flags;           // WMO flags
    uint16_t numLod;          // Number of LOD levels
};
```

### MOHD Flags

| Flag | Value | Description |
|------|-------|-------------|
| `DO_NOT_ATTENUATE_VERTICES` | 0x01 | Don't attenuate vertices based on portal distance |
| `USE_UNIFIED_RENDER_PATH` | 0x02 | Use unified render path |
| `USE_LIQUID_TYPE_DBC_ID` | 0x04 | Use liquid type from LiquidType.dbc |
| `DO_NOT_FIX_VERTEX_COLOR_ALPHA` | 0x08 | Don't fix vertex color alpha |

### MOMT Material Structure

```c
struct SMOMaterial {
    uint32_t flags;           // Material flags
    uint32_t shader;          // Shader index
    uint32_t blendMode;       // Blending mode
    uint32_t texture_1;       // First texture offset
    uint32_t color_1;         // First color (BGRA)
    uint32_t flags_1;         // First texture flags
    uint32_t texture_2;       // Second texture offset
    uint32_t color_2;         // Second color (BGRA)
    uint32_t flags_2;         // Second texture flags
    uint32_t color_3;         // Third color (BGRA)
    float f_unk1;
    float f_unk2;
    uint32_t runTimeData[8];  // Runtime data
};
```

### MOMT Flags

| Flag | Value | Description |
|------|-------|-------------|
| `F_UNLIT` | 0x01 | Unlit |
| `F_UNFOGGED` | 0x02 | Unfogged |
| `F_UNCULLED` | 0x04 | Two-sided |
| `F_EXTLIGHT` | 0x08 | External light |
| `F_SIDN` | 0x10 | Bright at night |
| `F_WINDOW` | 0x20 | Window |
| `F_CLAMP_S` | 0x40 | Clamp S |
| `F_CLAMP_T` | 0x80 | Clamp T |

### Group File Chunks

| Chunk | Description |
|-------|-------------|
| MVER | Version |
| MOGP | Group header |
| MOPY | Material info for triangles |
| MOVI | Vertex indices |
| MOVT | Vertices |
| MONR | Normals |
| MOTV | Texture coordinates |
| MOBA | Render batches |
| MOLR | Light references |
| MODR | Doodad references |
| MOBN | BSP tree nodes |
| MOBR | BSP face indices |
| MOCV | Vertex colors |
| MLIQ | Liquids |

### MOGP Group Header Structure

```c
struct SMOGroup {
    uint32_t groupName;           // Group name offset
    uint32_t descriptiveGroupName; // Descriptive name offset
    uint32_t flags;               // Group flags
    CAaBox boundingBox;           // Bounding box
    uint16_t portalStart;         // Portal start index
    uint16_t portalCount;         // Number of portals
    uint16_t transBatchCount;     // Transparent batch count
    uint16_t intBatchCount;       // Interior batch count
    uint16_t extBatchCount;       // Exterior batch count
    uint16_t padding;
    uint8_t fogIds[4];            // Fog indices
    uint32_t groupLiquid;         // Liquid type
    uint32_t uniqueID;            // Unique ID
    uint32_t flags2;              // Additional flags
    uint32_t unused;
};
```

### MOGP Flags

| Flag | Value | Description |
|------|-------|-------------|
| `HAS_BSP_TREE` | 0x00000001 | Has BSP tree |
| `HAS_LIGHT_MAP` | 0x00000002 | Has light map |
| `HAS_VERTEX_COLORS` | 0x00000004 | Has vertex colors |
| `EXTERIOR` | 0x00000008 | Exterior |
| `EXTERIOR_LIT` | 0x00000040 | Exterior lit |
| `UNREACHABLE` | 0x00000080 | Unreachable |
| `HAS_LIGHTS` | 0x00000200 | Has lights |
| `HAS_DOODADS` | 0x00000800 | Has doodads |
| `HAS_WATER` | 0x00001000 | Has water |
| `INTERIOR` | 0x00002000 | Interior |
| `SHOW_SKYBOX` | 0x00040000 | Show skybox |
| `HAS_MOCV2` | 0x01000000 | Has second MOCV |
| `HAS_MOTV2` | 0x02000000 | Has second MOTV |

### BSP Node Structure

```c
struct CAaBspNode {
    uint16_t flags;       // Node flags
    int16_t negChild;     // Negative child index
    int16_t posChild;     // Positive child index
    uint16_t nFaces;      // Number of faces
    uint32_t faceStart;   // Face start index
    float planeDist;      // Plane distance
};
```

---

## ADT Terrain Format

ADT files contain the terrain data for a single map tile. Each map is divided into a 64x64 grid of ADT tiles, and each tile contains 256 (16x16) MCNK chunks.

### Main Chunks

| Chunk | Description |
|-------|-------------|
| MVER | Version (18 for 3.3.5a) |
| MHDR | Header with offsets |
| MCIN | MCNK index |
| MTEX | Texture filenames |
| MMDX | M2 model filenames |
| MMID | M2 filename offsets |
| MWMO | WMO filenames |
| MWID | WMO filename offsets |
| MDDF | M2 placement |
| MODF | WMO placement |
| MFBO | Flight bounds |
| MH2O | Liquid data |
| MCNK | Map chunks (256) |

### MHDR Header Structure

```c
struct MHDR {
    uint32_t flags;       // Header flags
    uint32_t mcin;        // MCIN offset
    uint32_t mtex;        // MTEX offset
    uint32_t mmdx;        // MMDX offset
    uint32_t mmid;        // MMID offset
    uint32_t mwmo;        // MWMO offset
    uint32_t mwid;        // MWID offset
    uint32_t mddf;        // MDDF offset
    uint32_t modf;        // MODF offset
    uint32_t mfbo;        // MFBO offset
    uint32_t mh2o;        // MH2O offset
    uint32_t mtxf;        // MTXF offset
    uint8_t mamp_value;   // MAMP value
    uint8_t padding[3];
    uint32_t unused[3];
};
```

### MHDR Flags

| Flag | Value | Description |
|------|-------|-------------|
| `MHDR_MFBO` | 0x01 | Has MFBO chunk |
| `MHDR_NORTHREND` | 0x02 | Northrend map |

### MCIN Entry Structure

```c
struct MCINEntry {
    uint32_t offset;      // Absolute offset to MCNK
    uint32_t size;        // Size of MCNK chunk
    uint32_t flags;       // Flags (always 0 in file)
    uint32_t asyncId;     // Async ID (client use)
};
```

### MDDF M2 Placement Structure

```c
struct MDDF {
    uint32_t nameId;      // Index into MMID
    uint32_t uniqueId;    // Unique instance ID
    float position[3];    // X, Y, Z position
    float rotation[3];    // Rotation in degrees
    uint16_t scale;       // Scale * 1024
    uint16_t flags;       // Placement flags
};
```

### MODF WMO Placement Structure

```c
struct MODF {
    uint32_t nameId;      // Index into MWID
    uint32_t uniqueId;    // Unique instance ID
    float position[3];    // X, Y, Z position
    float rotation[3];    // Rotation in degrees
    float boundsMin[3];   // Bounding box min
    float boundsMax[3];   // Bounding box max
    uint16_t flags;       // Placement flags
    uint16_t doodadSet;   // Doodad set index
    uint16_t nameSet;     // Name set index
    uint16_t scale;       // Scale
};
```

### MCNK Chunk Header Structure

```c
struct MCNK {
    uint32_t flags;           // Chunk flags
    uint32_t indexX;          // X index (0-15)
    uint32_t indexY;          // Y index (0-15)
    uint32_t nLayers;         // Number of texture layers
    uint32_t nDoodadRefs;     // Number of doodad references
    uint32_t ofsHeight;       // MCVT offset
    uint32_t ofsNormal;       // MCNR offset
    uint32_t ofsLayer;        // MCLY offset
    uint32_t ofsRefs;         // MCRF offset
    uint32_t ofsAlpha;        // MCAL offset
    uint32_t sizeAlpha;       // MCAL size
    uint32_t ofsShadow;       // MCSH offset
    uint32_t sizeShadow;      // MCSH size
    uint32_t areaId;          // Area ID (AreaTable.dbc)
    uint32_t nMapObjRefs;     // Number of WMO references
    uint16_t holes;           // Hole bitmap
    uint16_t pad;
    uint16_t predTex[8];      // Low-res texture map
    uint8_t noEffectDoodad[8]; // No effect doodad
    uint32_t ofsSndEmitters;  // MCSE offset
    uint32_t nSndEmitters;    // Number of sound emitters
    uint32_t ofsLiquid;       // MCLQ offset
    uint32_t sizeLiquid;      // MCLQ size
    float position[3];        // Position (Z, X, Y)
    uint32_t ofsMCCV;         // MCCV offset
    uint32_t ofsMCLV;         // MCLV offset
    uint32_t unused;
};
```

### MCNK Flags

| Flag | Value | Description |
|------|-------|-------------|
| `HAS_MCSH` | 0x0001 | Has shadow map |
| `IMPASS` | 0x0002 | Impassable |
| `LQ_RIVER` | 0x0004 | River liquid |
| `LQ_OCEAN` | 0x0008 | Ocean liquid |
| `LQ_MAGMA` | 0x0010 | Magma liquid |
| `LQ_SLIME` | 0x0020 | Slime liquid |
| `HAS_MCCV` | 0x0040 | Has vertex colors |
| `DO_NOT_FIX_ALPHA_MAP` | 0x8000 | Don't fix alpha map |

### MCVT Height Map

The MCVT sub-chunk contains 145 float values representing the height map:
- 9x9 outer vertices (81 values)
- 8x8 inner vertices (64 values)

```c
struct MCVT {
    float heights[145];   // Height values relative to MCNK position
};
```

Absolute height calculation:
```
H = MCNK.position[2] + MCVT.heights[v]
```

### MCNR Normals

```c
struct MCNR {
    int8_t normals[145][3];   // Packed normals (x, z, y)
};
```

### MCLY Texture Layer Structure

```c
struct MCLY {
    uint32_t textureId;       // Index into MTEX
    uint32_t flags;           // Layer flags
    uint32_t offsetInMCAL;    // Offset in MCAL
    int16_t effectId;         // Ground effect ID
    int16_t padding;
};
```

### MCLY Flags

| Flag | Value | Description |
|------|-------|-------------|
| `ANIMATION_ROTATION` | 0x01 | Animation rotation |
| `ANIMATION_SPEED` | 0x02 | Animation speed |
| `ANIMATION_ENABLED` | 0x04 | Animation enabled |
| `OVERBRIGHT` | 0x08 | Overbright |
| `USE_ALPHA_MAP` | 0x10 | Use alpha map |
| `ALPHA_MAP_COMPRESSED` | 0x20 | Alpha map compressed |
| `USE_CUBE_MAP_REFLECTION` | 0x40 | Use cube map reflection |

### MH2O Liquid Structure

```c
struct MH2OHeader {
    uint32_t offsetInstances;  // Offset to instances
    uint32_t layerCount;       // Number of layers
    uint32_t offsetAttributes; // Offset to attributes
};

struct MH2OInstance {
    uint16_t liquidType;       // Liquid type (LiquidType.dbc)
    uint16_t flags;            // Liquid flags
    float minHeight;           // Minimum height
    float maxHeight;           // Maximum height
    uint8_t xOffset;           // X offset
    uint8_t yOffset;           // Y offset
    uint8_t width;             // Width
    uint8_t height;            // Height
    uint32_t offsetBitmap;     // Offset to existence bitmap
    uint32_t offsetVertexData; // Offset to vertex data
};
```

---

## WDT World Definition Format

WDT files define the structure of a world map, specifying which ADT tiles exist and providing global map information.

### Chunks

| Chunk | Description |
|-------|-------------|
| MVER | Version (18 for 3.3.5a) |
| MPHD | Map header |
| MAIN | ADT tile flags |
| MWMO | Global WMO filename (optional) |
| MODF | Global WMO placement (optional) |

### MPHD Header Structure

```c
struct SMMapHeader {
    uint32_t flags;       // Map flags
    uint32_t something;   // Unknown
    uint32_t unused[6];
};
```

### MPHD Flags

| Flag | Value | Description |
|------|-------|-------------|
| `WDT_USES_GLOBAL_MAP_OBJ` | 0x01 | Uses global WMO |
| `ADT_HAS_MCCV` | 0x02 | ADTs have vertex colors |
| `ADT_HAS_BIG_ALPHA` | 0x04 | ADTs have big alpha maps |
| `ADT_HAS_DOODADREFS_SORTED` | 0x08 | Doodad refs sorted by size |
| `FLAG_LIGHTING_VERTICES` | 0x10 | Has lighting vertices |
| `ADT_HAS_UPSIDE_DOWN_GROUND` | 0x20 | Upside down ground |

### MAIN Entry Structure

```c
struct SMAreaInfo {
    uint32_t flags;       // Tile flags
    uint32_t asyncId;     // Async ID (client use)
};
```

### MAIN Flags

| Flag | Value | Description |
|------|-------|-------------|
| `FLAG_HAS_ADT` | 0x01 | ADT file exists |
| `FLAG_ALL_WATER` | 0x02 | All water |
| `FLAG_LOADED` | 0x04 | Loaded (runtime) |

---

## BLP Texture Format

BLP files store textures with pre-calculated mipmaps. They support various compression methods and alpha channel configurations.

### Header Structure

```c
struct BLPHeader {
    char magic[4];            // "BLP2"
    uint32_t version;         // Always 1
    uint8_t compression;      // Compression type
    uint8_t alphaDepth;       // Alpha bit depth (0, 1, 4, 8)
    uint8_t alphaType;        // Alpha type
    uint8_t hasMipmaps;       // Has mipmaps
    uint32_t width;           // Width in pixels
    uint32_t height;          // Height in pixels
    uint32_t mipOffsets[16];  // Mipmap offsets
    uint32_t mipSizes[16];    // Mipmap sizes
    uint32_t palette[256];    // Color palette (paletted only)
};
```

### Compression Types

| Value | Type | Description |
|-------|------|-------------|
| 0 | JPEG | JPEG compression (not used in 3.3.5a) |
| 1 | Paletted | 256-color palette |
| 2 | DXT | DXT compression |
| 3 | ARGB8888 | Uncompressed ARGB |

### DXT Variants

| Alpha Depth | Alpha Type | Format |
|-------------|------------|--------|
| 0 | 0 | DXT1 |
| 1 | 0 | DXT1 |
| 4 | 1 | DXT3 |
| 8 | 1 | DXT3 |
| 8 | 7 | DXT5 |

### DXT Mipmap Size Calculation

```c
blocksX = (width + 3) / 4;
blocksY = (height + 3) / 4;
DXT1_size = blocksX * blocksY * 8;
DXT3_size = blocksX * blocksY * 16;
DXT5_size = blocksX * blocksY * 16;
```

---

## World Coordinate System

The World of Warcraft coordinate system uses yards as the internal unit and follows a specific axis orientation.

### Axes

| Axis | Direction |
|------|-----------|
| +X | North |
| +Y | West |
| +Z | Up |

### Grid Dimensions

| Element | Size |
|---------|------|
| World grid | 64 × 64 ADT tiles |
| ADT tile | 533.33333 yards |
| MCNK cell | 33.33333 yards |
| World width | 34,133.33312 yards |

### Coordinate Conversions

**Tile index to world coordinates:**
```
Wx = (32 - Ix) * 533.33333
Wy = (32 - Iy) * 533.33333
```

**Chunk local offset:**
```
Ox = Cx * 33.33333
Oy = Cy * 33.33333
```

---

## Character Customization System

Character appearance is controlled by several interconnected DBC files.

### CharSections.dbc Structure

```c
struct CharSectionsEntry {
    uint32_t ID;
    uint32_t RaceID;          // ChrRaces.dbc
    uint32_t SexID;           // 0=Male, 1=Female
    uint32_t BaseSection;     // Section type (see below)
    char* TextureName[3];     // Texture filenames
    uint32_t Flags;           // Section flags
    uint32_t VariationIndex;  // Variation index
    uint32_t ColorIndex;      // Color index
};
```

### BaseSection Types

| Value | Type | Texture1 | Texture2 | Texture3 |
|-------|------|----------|----------|----------|
| 0 | Base Skin | SkinTexture | ExtraSkinTexture | - |
| 1 | Face | FaceLowerTexture | FaceUpperTexture | - |
| 2 | Facial Hair | FacialLowerTexture | FacialUpperTexture | - |
| 3 | Hair | HairTexture | ScalpLowerTexture | ScalpUpperTexture |
| 4 | Underwear | PelvisTexture | TorsoTexture | - |

### CharSections Flags

| Flag | Value | Description |
|------|-------|-------------|
| `PLAYABLE` | 0x01 | Available in character creation |
| `BARBERSHOP` | 0x02 | Available in barbershop |
| `DEATH_KNIGHT` | 0x04 | Death Knight texture |
| `NPC_SKIN` | 0x08 | NPC skin |
| `REGULAR_SKIN` | 0x10 | Regular skin |
| `SILHOUETTE` | 0x100 | Silhouette texture |

### CharacterFacialHairStyles.dbc Structure

```c
struct CharacterFacialHairStylesEntry {
    uint32_t RaceID;          // ChrRaces.dbc
    uint32_t SexID;           // 0=Male, 1=Female
    uint32_t VariationID;     // Style variation
    uint32_t Geoset1;         // Geoset group 1
    uint32_t Geoset2;         // Geoset group 3
    uint32_t Geoset3;         // Geoset group 2
    uint32_t Geoset4;         // Geoset group 16
    uint32_t Geoset5;         // Geoset group 17
};
```

### CharHairGeosets.dbc Structure

```c
struct CharHairGeosetsEntry {
    uint32_t ID;
    uint32_t RaceID;          // ChrRaces.dbc
    uint32_t SexID;           // 0=Male, 1=Female
    uint32_t VariationID;     // Hair style variation
    uint32_t GeosetID;        // Geoset in model
    uint32_t Showscalp;       // Show scalp (bald)
};
```

### CreatureDisplayInfo.dbc Structure

```c
struct CreatureDisplayInfoEntry {
    uint32_t ID;
    uint32_t ModelID;                 // CreatureModelData.dbc
    uint32_t SoundID;                 // CreatureSoundData.dbc
    uint32_t ExtendedDisplayInfoID;   // CreatureDisplayInfoExtra.dbc
    float CreatureModelScale;
    uint32_t CreatureModelAlpha;      // 0-255
    char* TextureVariation[3];
    char* PortraitTextureName;
    uint32_t SizeClass;
    uint32_t BloodID;                 // UnitBloodLevels.dbc
    uint32_t NPCSoundID;              // NPCSounds.dbc
    uint32_t ParticleColorID;         // ParticleColor.dbc
    uint32_t CreatureGeosetData;
    uint32_t ObjectEffectPackageID;   // ObjectEffectPackage.dbc
};
```

### CreatureDisplayInfoExtra.dbc Structure

```c
struct CreatureDisplayInfoExtraEntry {
    uint32_t ID;
    uint32_t RaceID;          // ChrRaces.dbc
    uint32_t SexID;           // 0=Male, 1=Female
    uint32_t SkinColor;       // CharSections.dbc
    uint32_t FaceType;        // CharSections.dbc
    uint32_t HairStyle;       // CharHairGeosets.dbc
    uint32_t HairColor;       // CharSections.dbc
    uint32_t BeardStyle;      // CharacterFacialHairStyles.dbc
    uint32_t Helm;            // ItemDisplayInfo.dbc
    uint32_t Shoulder;        // ItemDisplayInfo.dbc
    uint32_t Shirt;           // ItemDisplayInfo.dbc
    uint32_t Cuirass;         // ItemDisplayInfo.dbc
    uint32_t Belt;            // ItemDisplayInfo.dbc
    uint32_t Legs;            // ItemDisplayInfo.dbc
    uint32_t Boots;           // ItemDisplayInfo.dbc
    uint32_t Wrist;           // ItemDisplayInfo.dbc
    uint32_t Gloves;          // ItemDisplayInfo.dbc
    uint32_t Tabard;          // ItemDisplayInfo.dbc
    uint32_t Cape;            // ItemDisplayInfo.dbc
    uint32_t CanEquip;
    char* Texture;
};
```

---

## Spell Visual System

Spell visuals are controlled by a hierarchy of DBC files that define the effects for each stage of a spell cast.

### SpellVisual.dbc Structure

```c
struct SpellVisualEntry {
    uint32_t ID;
    uint32_t precastKit;              // SpellVisualKit.dbc
    uint32_t castKit;                 // SpellVisualKit.dbc
    uint32_t impactKit;               // SpellVisualKit.dbc
    uint32_t stateKit;                // SpellVisualKit.dbc
    uint32_t stateDoneKit;            // SpellVisualKit.dbc
    uint32_t channelKit;              // SpellVisualKit.dbc
    uint32_t hasMissile;              // Boolean
    uint32_t missileModel;            // Model ID
    uint32_t missilePathType;
    uint32_t missileDestinationAttachment;
    uint32_t missileSound;            // SoundEntries.dbc
    uint32_t animEventSoundID;        // SoundEntries.dbc
    uint32_t flags;
    uint32_t casterImpactKit;         // SpellVisualKit.dbc
    uint32_t targetImpactKit;         // SpellVisualKit.dbc
    uint32_t missileAttachment;
    uint32_t missileFollowGroundHeight;
    uint32_t missileFollowGroundDropSpeed;
    uint32_t missileFollowGroundApproach;
    uint32_t missileFollowGroundFlags;
    uint32_t missileMotion;           // SpellMissileMotion.dbc
    uint32_t missileTargetingKit;     // SpellVisualKit.dbc
    uint32_t instantAreaKit;          // SpellVisualKit.dbc
    uint32_t impactAreaKit;           // SpellVisualKit.dbc
    uint32_t persistentAreaKit;       // SpellVisualKit.dbc
    float missileCastOffset[3];
    float missileImpactOffset[3];
};
```

### SpellVisualKit.dbc Structure

```c
struct SpellVisualKitEntry {
    uint32_t ID;
    uint32_t startAnimID;             // AnimationData.dbc
    uint32_t animID;                  // AnimationData.dbc
    uint32_t headEffect;              // SpellVisualEffectName.dbc
    uint32_t chestEffect;             // SpellVisualEffectName.dbc
    uint32_t baseEffect;              // SpellVisualEffectName.dbc
    uint32_t leftHandEffect;          // SpellVisualEffectName.dbc
    uint32_t rightHandEffect;         // SpellVisualEffectName.dbc
    uint32_t breathEffect;            // SpellVisualEffectName.dbc
    uint32_t leftWeaponEffect;        // SpellVisualEffectName.dbc
    uint32_t rightWeaponEffect;       // SpellVisualEffectName.dbc
    uint32_t specialEffect[3];        // SpellVisualEffectName.dbc
    uint32_t worldEffect;             // SpellVisualEffectName.dbc
    uint32_t soundID;                 // SoundEntries.dbc
    uint32_t shakeID;                 // SpellEffectCameraShakes.dbc
    uint32_t charProc[4];             // Character procedures
    float charParamZero[4];
    float charParamOne[4];
    float charParamTwo[4];
    float charParamThree[4];
};
```

### Character Procedures

| Index | Procedure | Description |
|-------|-----------|-------------|
| -1 | None | No procedure |
| 0 | Chain | Lightning chain effect |
| 1 | Color | Color tint |
| 2 | Scale | Scale model |
| 4 | Emissive | Emissive color |
| 6 | Eclipse | Eclipse effect |
| 7 | Stand/Walk Anim | Override animations |
| 8 | Weapon Trail | Weapon trail effect |
| 9 | Blizzard | Blizzard particles |
| 10 | Fishing Line | Fishing line effect |

### SpellVisualEffectName.dbc Structure

```c
struct SpellVisualEffectNameEntry {
    uint32_t ID;
    char* name;
    char* fileName;           // M2 model path
    float areaEffectSize;
    float scale;
    float minAllowedScale;
    float maxAllowedScale;
};
```

---

## Sound and Music System

Sound and music in World of Warcraft are managed through several DBC files.

### SoundEntries.dbc Structure

```c
struct SoundEntriesEntry {
    uint32_t ID;
    uint32_t soundType;               // Sound type
    char* name;
    char* File[10];                   // Sound files
    uint32_t Freq[10];                // Playback frequencies
    char* DirectoryBase;
    float volumeFloat;
    uint32_t flags;
    float minDistance;
    float maxDistance;
    float distanceCutoff;
    uint32_t soundEntriesAdvancedID;  // SoundEntriesAdvanced.dbc
};
```

### Sound Types

| Value | Type |
|-------|------|
| 1 | Spells |
| 2 | UI |
| 3 | Footsteps |
| 4 | Combat Impacts |
| 6 | Combat Swings |
| 7 | Greetings |
| 8 | Casting |
| 9 | Pick Up/Put Down |
| 10 | NPC Combat |
| 12 | Errors |
| 13 | Birds |
| 14 | Doodad Sounds |
| 16 | Death Thud Sounds |
| 17 | NPC Sounds |
| 28 | Zone Music Files |
| 50 | Zone Ambience |
| 52 | Sound Emitters |
| 53 | Vehicle States |

### ZoneMusic.dbc Structure

```c
struct ZoneMusicEntry {
    uint32_t ID;
    char* SetName;
    uint32_t SilenceIntervalMin[2];   // Day/Night
    uint32_t SilenceIntervalMax[2];   // Day/Night
    uint32_t Sounds[2];               // SoundEntries.dbc (Day/Night)
};
```

### Sound Flags

| Flag | Value | Description |
|------|-------|-------------|
| `NO_DUPLICATES` | 0x0020 | No duplicate sounds |
| `LOOPING` | 0x0200 | Looping sound |

---

## Navigation and Pathfinding

Server-side navigation uses VMAPs (Visibility Maps) and MMAPs (Movement Maps) for collision detection and pathfinding.

### VMAP (Visibility Map)

VMAPs are used for line-of-sight calculations and contain simplified collision geometry extracted from WMO and M2 files.

### MMAP (Movement Map)

MMAPs are navigation meshes generated using the Recast/Detour library. They enable server-side pathfinding for NPCs.

### Generation Process

1. **Map Extraction**: Extract map data from MPQ files using `mapextractor`
2. **VMAP Extraction**: Extract collision data using `vmap4extractor`
3. **VMAP Assembly**: Assemble VMAP data using `vmap4assembler`
4. **MMAP Generation**: Generate navigation meshes using `mmaps_generator`

### Recast/Detour Integration

The server uses the Recast library to build navigation meshes and the Detour library to perform pathfinding queries.

```cpp
// Pseudocode for pathfinding
dtNavMesh* navMesh = loadNavMesh("path/to/navmesh.mmap");
dtNavMeshQuery* navQuery = new dtNavMeshQuery();
navQuery->init(navMesh, 2048);

float startPos[3] = {x1, y1, z1};
float endPos[3] = {x2, y2, z2};

dtQueryFilter filter;
dtPolyRef startRef, endRef;
navQuery->findNearestPoly(startPos, extents, &filter, &startRef, nearestPt);
navQuery->findNearestPoly(endPos, extents, &filter, &endRef, nearestPt);

dtPolyRef path[MAX_PATH_POLYS];
int pathCount;
navQuery->findPath(startRef, endRef, startPos, endPos, &filter, path, &pathCount, MAX_PATH_POLYS);
```

---

## Client Memory and Offsets

The World of Warcraft client stores game state in memory structures that can be accessed through known offsets.

### Object Manager

The Object Manager maintains a linked list of all game objects (players, units, game objects, etc.).

```c
// Key offsets for 3.3.5a (build 12340)
#define CURMGR_OFFSET       0x00B41414
#define CURMGR_PTR_OFFSET   0x24
#define NEXT_OBJECT_OFFSET  0x3C
#define OBJECT_TYPE_OFFSET  0x14
```

### Object Fields Structure

```c
struct ObjectFields {
    uint64_t guid;              // 0x00
    uint32_t type;              // 0x08
    uint32_t entry;             // 0x0C
    float scale_x;              // 0x10
    uint32_t padding;           // 0x14
};
```

### Unit Fields Structure

```c
struct UnitFields {
    uint64_t charm;             // 0x18
    uint64_t summon;            // 0x20
    uint64_t critter;           // 0x28
    uint64_t charmed_by;        // 0x30
    uint64_t summoned_by;       // 0x38
    uint64_t created_by;        // 0x40
    uint64_t target;            // 0x48
    uint64_t channel_object;    // 0x50
    uint32_t channel_spell;     // 0x58
    uint32_t bytes_0;           // 0x5C
    uint32_t health;            // 0x60
    uint32_t power[7];          // 0x64
    uint32_t max_health;        // 0x80
    uint32_t max_power[7];      // 0x84
    uint32_t level;             // 0xD8
    // ... additional fields
};
```

### Lua Engine Offsets

```c
#define LUA_GETTOP      0x0084DBD0
#define LUA_SETTOP      0x0084DBF0
#define LUA_PUSHSTRING  0x0084E350
#define LUA_PUSHINTEGER 0x0084E2D0
#define LUA_PCALL       0x0084EC50
```

---

## Network Protocol

Communication between the client and server uses TCP with encrypted and compressed packets.

### World Packet Structure

**Server Message (SMSG) Header:**

| Offset | Size | Endian | Type | Description |
|--------|------|--------|------|-------------|
| 0x0 | 2 | Big | uint16 | Packet size (including opcode) |
| 0x2 | 2 | Little | uint16 | Opcode |

**Client Message (CMSG) Header:**

| Offset | Size | Endian | Type | Description |
|--------|------|--------|------|-------------|
| 0x0 | 2 | Big | uint16 | Packet size (including opcode) |
| 0x2 | 4 | Little | uint32 | Opcode |

### Login Opcodes

| Name | Value | Description |
|------|-------|-------------|
| CMD_AUTH_LOGON_CHALLENGE | 0x00 | Login challenge |
| CMD_AUTH_LOGON_PROOF | 0x01 | Login proof |
| CMD_AUTH_RECONNECT_CHALLENGE | 0x02 | Reconnect challenge |
| CMD_AUTH_RECONNECT_PROOF | 0x03 | Reconnect proof |
| CMD_SURVEY_RESULT | 0x04 | Hardware survey |
| CMD_REALM_LIST | 0x10 | Realm list request |
| CMD_XFER_INITIATE | 0x30 | Transfer initiate |
| CMD_XFER_DATA | 0x31 | Transfer data |
| CMD_XFER_ACCEPT | 0x32 | Transfer accept |
| CMD_XFER_RESUME | 0x33 | Transfer resume |
| CMD_XFER_CANCEL | 0x34 | Transfer cancel |

### Authentication Flow

1. Client sends `CMD_AUTH_LOGON_CHALLENGE_Client` with username and version
2. Server responds with `CMD_AUTH_LOGON_CHALLENGE_Server` containing SRP values
3. Client sends `CMD_AUTH_LOGON_PROOF_Client` with proof
4. Server responds with `CMD_AUTH_LOGON_PROOF_Server` with verification
5. Client requests realm list with `CMD_REALM_LIST_Client`
6. Server responds with `CMD_REALM_LIST_Server`
7. Client connects to realm server
8. Realm server sends `SMSG_AUTH_CHALLENGE`
9. Client sends `CMSG_AUTH_SESSION`
10. Server sends `SMSG_AUTH_RESPONSE`

---

## Lua AddOn API

The World of Warcraft 3.3.5a client exposes a comprehensive Lua API for addon development.

### API Categories

| Category | Description |
|----------|-------------|
| Account | Account information |
| Achievement | Achievement system |
| Action | Action bar functions |
| Auction | Auction house |
| Bag | Inventory management |
| Bank | Bank functions |
| Battlefield | PvP battlegrounds |
| Calendar | Calendar system |
| Camera | Camera control |
| Channel | Chat channels |
| Character | Character information |
| Combat | Combat system |
| Companion | Pets and mounts |
| Container | Container management |
| Cursor | Cursor functions |
| Equipment | Equipment management |
| Event | Event handling |
| Frame | UI frame functions |
| Friends | Friends list |
| Glyph | Glyph system |
| Group | Party/raid functions |
| Guild | Guild functions |
| Item | Item information |
| Loot | Loot functions |
| Macro | Macro system |
| Mail | Mail system |
| Map | World map |
| Pet | Pet functions |
| Quest | Quest system |
| Skill | Skill information |
| Sound | Sound control |
| Spell | Spell functions |
| Talent | Talent system |
| Taxi | Flight paths |
| Trade | Trading |
| Unit | Unit functions |
| Vehicle | Vehicle system |

### Common Functions

```lua
-- Unit functions
UnitName("unit")
UnitHealth("unit")
UnitHealthMax("unit")
UnitPower("unit", powerType)
UnitPowerMax("unit", powerType)
UnitLevel("unit")
UnitClass("unit")
UnitRace("unit")

-- Spell functions
GetSpellInfo(spellId)
CastSpellByName("spellName")
IsSpellKnown(spellId)
GetSpellCooldown(spellId)

-- Item functions
GetItemInfo(itemId)
GetItemCount(itemId)
UseItemByName("itemName")

-- Frame functions
CreateFrame("frameType", "frameName", parent, "template")
frame:SetScript("event", handler)
frame:Show()
frame:Hide()
```

### Events

Events are used to notify addons of game state changes:

```lua
local frame = CreateFrame("Frame")
frame:RegisterEvent("PLAYER_LOGIN")
frame:RegisterEvent("UNIT_HEALTH")
frame:SetScript("OnEvent", function(self, event, ...)
    if event == "PLAYER_LOGIN" then
        -- Player logged in
    elseif event == "UNIT_HEALTH" then
        local unit = ...
        -- Unit health changed
    end
end)
```

---

## Modding Tools Reference

### Map and Terrain Tools

| Tool | Description |
|------|-------------|
| **Noggit** | Comprehensive map editor for ADT/WDT files |
| **AdtTools** | C# framework for ADT manipulation |
| **wow-adt** | Rust crate for ADT parsing |
| **wow-wdt** | Rust crate for WDT parsing |

### Model Tools

| Tool | Description |
|------|-------------|
| **WoW Model Viewer** | 3D model viewer for M2 and WMO files |
| **WMVx** | Fork of WoW Model Viewer with multi-version support |
| **Blender M2 Scripts** | Import/export M2 models in Blender |
| **Blender WMO Scripts** | Import/export WMO models in Blender |

### Database Tools

| Tool | Description |
|------|-------------|
| **WDBX Editor** | Editor for DBC, DB2, WDB, ADB files |
| **MyDBCEditor** | Simple DBC editor |
| **TrinityCore DBC Tools** | Command-line DBC utilities |

### Archive Tools

| Tool | Description |
|------|-------------|
| **Ladik's MPQ Editor** | Comprehensive MPQ archive editor |
| **StormLib** | Library for MPQ manipulation |

### Texture Tools

| Tool | Description |
|------|-------------|
| **BLPConverter** | Convert BLP to/from PNG, TGA |
| **BLP Lab** | BLP texture editor |

### Analysis Tools

| Tool | Description |
|------|-------------|
| **010 Editor** | Hex editor with WoW file templates |
| **Cheat Engine** | Memory scanner and debugger |
| **ReClass.NET** | Class structure reverse engineering |

---

## Hard Limits

The following are hard limits imposed by the 3.3.5a client:

| Limit | Value |
|-------|-------|
| ADT tiles per map | 64 × 64 (4096) |
| MCNK chunks per ADT | 256 (16 × 16) |
| Terrain layers per MCNK | 4 |
| Bones per M2 model | 256 |
| Bone weights per vertex | 4 |
| Weight sum per vertex | 255 |
| MPQ archive size | 4 GB |
| Heightmap vertices per MCNK | 145 (9×9 + 8×8) |
| Alpha map resolution | 64 × 64 pixels |
| Skin files per M2 | 4 (LOD levels) |
| Mipmap levels per BLP | 16 |
| Palette colors per BLP | 256 |

---

## References

This document was compiled using information from the following sources:

1. **wowdev.wiki** - The primary community wiki for World of Warcraft file formats and technical documentation. [https://wowdev.wiki](https://wowdev.wiki)

2. **TrinityCore** - Open-source MMORPG framework with extensive documentation on server-side systems. [https://trinitycore.info](https://trinitycore.info)

3. **OwnedCore** - Community forum with discussions on memory editing and client internals. [https://www.ownedcore.com](https://www.ownedcore.com)

4. **WoWWiki (Archive)** - Archived version of the original WoWWiki with API documentation. [https://wowwiki-archive.fandom.com](https://wowwiki-archive.fandom.com)

5. **GitHub Repositories** - Various open-source projects for WoW modding and file format parsing.

6. **UnknownCheats** - Forum with information on client offsets and structures. [https://www.unknowncheats.me](https://www.unknowncheats.me)

---

*This document is provided for educational and research purposes. World of Warcraft is a trademark of Blizzard Entertainment.*
