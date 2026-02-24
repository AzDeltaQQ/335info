# M2 Model Format

## Introduction

M2 files (also called MDX) contain model objects. Each M2 file describes the vertices, faces, materials, texture names, animations and properties of one model. M2 files don't have a chunked format like most other WoW formats (except in Legion). Since it is chunked in Legion, all offsets are relative to beginning of the MD21 chunk's data rather than the beginning of the file.

Models are used for doodads (decoration objects), players, monsters and really everything in the game except for Terrain and WMOs.

M2 files do not store all the data for the model in them. Additional model information is stored in these files: .anim, .skin, .phys, .bone, .skel which may vary depending on the client version.

## Header

The header has mostly the layout of number-offset pairs, containing the number of a particular record in the file, and the offset. These appear at fixed places in the header. Record sizes are not specified in the file.

```c
struct M2Header {
    uint32_t magic;                                       // "MD20". Legion uses a chunked file format starting with MD21.
    uint32_t version;
    M2Array<char> name;                                   // should be globally unique, used to reload by name in internal clients, empty string in files updated in or after 9.2.0.41462+

    struct {
        uint32_t flag_tilt_x: 1;
        uint32_t flag_tilt_y: 1;
        uint32_t: 1;
        uint32_t flag_use_texture_combiner_combos: 1;       // add textureCombinerCombos array to end of data (alt. name: Second_Texture_Material_Override_Combos)
        uint32_t: 1;
        uint32_t flag_load_phys_data: 1;
        uint32_t: 1;
        uint32_t flag_unk_0x80: 1;                         // with this flag unset, demon hunter tattoos stop glowing
        uint32_t flag_camera_related: 1;
        uint32_t flag_new_particle_record: 1;              // In CATA: new version of ParticleEmitters.
        uint32_t flag_unk_0x400: 1;
        uint32_t flag_texture_transforms_use_bone_sequences: 1;
        uint32_t flag_unk_0x1000: 1;
        uint32_t ChunkedAnimFiles_0x2000: 1;
        uint32_t flag_unk_0x4000: 1;
        uint32_t flag_unk_0x8000: 1;
        uint32_t flag_unk_0x10000: 1;
        uint32_t flag_unk_0x20000: 1;
        uint32_t flag_unk_0x40000: 1;
        uint32_t flag_unk_0x80000: 1;
        uint32_t flag_unk_0x100000: 1;
        uint32_t flag_unk_0x200000: 1;
        uint32_t flag_unk_0x40000000: 1;
    } global_flags;

    M2Array<M2Loop> global_loops;                        // Timestamps used in global looping animations.
    M2Array<M2Sequence> sequences;                       // Information about the animations in the model.
    M2Array<uint16_t> sequenceIdxHashById;               // Mapping of sequence IDs to the entries in the Animation sequences block.
    M2Array<M2SequenceFallback> playable_animation_lookup;
    M2Array<M2CompBone> bones;
    M2Array<uint16_t> boneIndicesById;                   //Lookup table for key skeletal bones. (alt. name: key_bone_lookup)
    M2Array<M2Vertex> vertices;
    uint32_t num_skin_profiles;                           // Views (LOD) are now in .skins.
    M2Array<M2Color> colors;                             // Color and alpha animations definitions.
    M2Array<M2Texture> textures;
    M2Array<M2TextureWeight> texture_weights;            // Transparency of textures.
    M2Array<M2TextureTransform> texture_transforms;
    M2Array<uint16_t> textureIndicesById;                // (alt. name: replacable_texture_lookup)
    M2Array<M2Material> materials;                       // Blending modes / render flags.
    M2Array<uint16_t> boneCombos;                        // (alt. name: bone_lookup_table)
    M2Array<uint16_t> textureCombos;                     // (alt. name: texture_lookup_table)
    M2Array<uint16_t> textureCoordCombos;           // (alt. name: tex_unit_lookup_table, texture_mapping_lookup_table)
    M2Array<uint16_t> textureWeightCombos;               // (alt. name: transparency_lookup_table)
    M2Array<uint16_t> textureTransformCombos;            // (alt. name: texture_transforms_lookup_table)

    CAaBox bounding_box;
    float bounding_sphere_radius;
    CAaBox collision_box;
    float collision_sphere_radius;

    M2Array<uint16_t> collisionIndices;                    // (alt. name: collision_triangles)
    M2Array<C3Vector> collisionPositions;                  // (alt. name: collision_vertices)
    M2Array<C3Vector> collisionFaceNormals;                // (alt. name: collision_normals) 
    M2Array<M2Attachment> attachments;                     // position of equipped weapons or effects
    M2Array<uint16_t> attachmentIndicesById;               // (alt. name: attachment_lookup_table)
    M2Array<M2Event> events;                               // Used for playing sounds when dying and a lot else.
    M2Array<M2Light> lights;                               // Lights are mainly used in loginscreens but in wands and some doodads too.
    M2Array<M2Camera> cameras;                             // The cameras are present in most models for having a model in the character tab. 
    M2Array<uint16_t> cameraIndicesById;                   // (alt. name: camera_lookup_table)
    M2Array<M2Ribbon> ribbon_emitters;                     // Things swirling around. See the CoT-entrance for light-trails.
    M2Array<M2Particle> particle_emitters;

    if (flag_use_texture_combiner_combos) {
        M2Array<uint16_t> textureCombinerCombos;
    }
};
```

## Data Structures

```c
// Generic M2 array
template<typename T>
struct M2Array {
  uint32_t size;
  uint32_t offset; // pointer to T, relative to begin of m2 data block
};

// Generic M2 track
struct M2TrackBase {
  uint16_t trackType;
  uint16_t loopIndex;
  M2Array<M2SequenceTimes> sequenceTimes;
};

template<typename T>
struct M2Track : M2TrackBase {
  M2Array<M2Array<T>> values;
};

// Animation sequences
struct M2Sequence {
    uint16_t id;                   // Animation id in AnimationData.dbc
    uint16_t variationIndex;       // Sub-animation id
    uint32_t duration;             // The length of this animation sequence in milliseconds.
    float movespeed;               // The speed at which the model moves with this animation.
    uint32_t flags;                // See Animation sequence flags
    int16_t frequency;             // How often this animation is played.
    M2Bounds bounds;
    int16_t variationNext;         // id of the next animation of this variation
    uint16_t aliasNext;            // id of the animation that may be played after this animation
};

// Bones
struct M2CompBone {
    int32_t key_bone_id;            // Back-reference to the key bone lookup table.
    uint32_t flags;                 // See Bone flags
    int16_t parent_bone;            // Parent bone ID or -1 for root.
    uint16_t submesh_id;            // Mesh part ID
    union {
        struct {
            uint16_t screen_size_perc_start;
            uint16_t screen_size_perc_end;
        } billboard;
        uint32_t boneNameCRC;
    };
    M2Track<C3Vector> translation;
    M2Track<C4Quaternion> rotation;
    M2Track<C3Vector> scaling;
    C3Vector pivot;                 // Pivot point
};

// Vertices
struct M2Vertex {
    C3Vector pos;
    uint8_t bone_weights[4];
    uint8_t bone_indices[4];
    C3Vector normal;
    C2Vector tex_coords[2];
};

// Materials
struct M2Material {
  uint16_t flags;
  uint16_t blending_mode;
};

// Textures
struct M2Texture {
  uint32_t type;
  uint32_t flags;
  M2Array<char> filename;
};

// Texture Transforms
struct M2TextureTransform {
  M2Track<C3Vector> translation;
  M2Track<C4Quaternion> rotation;
  M2Track<C3Vector> scaling;
};

// Ribbon Emitters
struct M2Ribbon {
  uint32_t ribbonId;
  uint32_t boneIndex;
  C3Vector position;
  M2Array<uint16_t> textureIndices;
  M2Array<uint16_t> materialIndices;
  M2Track<C3Vector> colorTrack;
  M2Track<fixed16> alphaTrack;
  M2Track<float> heightAboveTrack;
  M2Track<float> heightBelowTrack;
  float edgesPerSecond;
  float edgeLifetime;
  float gravity;
  uint16_t textureRows;
  uint16_t textureCols;
  M2Track<uint16_t> texSlotTrack;
  M2Track<uint8_t> visibilityTrack;
  int16_t priorityPlane;
  uint16_t unknown;
};

// Particle Emitters
struct M2Particle {
    //... see wowdev.wiki for full structure
};

// Lights
struct M2Light {
    uint16_t type;
    int16_t bone;
    C3Vector position;
    M2Track<C3Vector> ambient_color;
    M2Track<float> ambient_intensity;
    M2Track<C3Vector> diffuse_color;
    M2Track<float> diffuse_intensity;
    M2Track<float> attenuation_start;
    M2Track<float> attenuation_end;
    M2Track<uint8_t> visibility;
};

// Cameras
struct M2Camera {
  uint32_t type;
  float far_clip;
  float near_clip;
  M2Track<M2SplineKey<C3Vector>> positions;
  C3Vector position_base;
  M2Track<M2SplineKey<C3Vector>> target_position;
  C3Vector target_position_base;
  M2Track<M2SplineKey<float>> roll;
  M2Track<M2SplineKey<float>> FoV;
};

// Attachments
struct M2Attachment {
  uint32_t id;
  uint16_t bone;
  uint16_t unknown;
  C3Vector position;
  M2Track<uchar> animate_attached;
};

```

## Constants and Flags

### Global Flags
- `flag_tilt_x`: 0x1
- `flag_tilt_y`: 0x2
- `flag_use_texture_combiner_combos`: 0x8
- `flag_load_phys_data`: 0x20
- `flag_unk_0x80`: 0x80
- `flag_camera_related`: 0x100
- `flag_new_particle_record`: 0x200

### Bone Flags
- `ignore_parent_translate`: 0x8
- `ignore_parent_scale`: 0x10
- `ignore_parent_rotation`: 0x20
- `screen_align`: 0x40
- `unfogged`: 0x200

### Material Flags
- `Unlit`: 0x01
- `Unfogged`: 0x02
- `Two-sided`: 0x04
- `depthTest`: 0x08
- `depthWrite`: 0x10

### Texture Flags
- `Texture wrap X`: 0x1
- `Texture wrap Y`: 0x2

## Relationships
- M2 models are used for most game objects, including characters, creatures, and doodads.
- They are related to several other file formats like `.skin`, `.anim`, `.phys`, `.bone`, and `.skel` which contain additional model data.
- Textures are stored in `.blp` files.
- Animation data can be found in `AnimationData.dbc`.

## Tools
- **Wow-model-viewer**: A popular tool for viewing WoW models.
- **010 Editor**: A hex editor with templates for many WoW file formats, including M2.
- **Blender M2 import/export scripts**: Various scripts are available for importing and exporting M2 models into Blender.

## Sources
- https://wowdev.wiki/M2
