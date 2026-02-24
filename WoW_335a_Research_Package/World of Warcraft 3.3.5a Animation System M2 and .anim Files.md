# World of Warcraft 3.3.5a Animation System: M2 and .anim Files

## 1. Summary

The World of Warcraft 3.3.5a animation system is primarily centered around the M2 model format. M2 files contain the core model data, including vertices, bones, and animation sequences. Animations can be stored either internally within the M2 file or externally in separate `.anim` files, which are loaded on demand. The system supports various interpolation methods for smooth keyframe animation, including linear, Bezier, and Hermite, allowing for complex and realistic character and creature movements. Global sequences provide a mechanism for continuous, looping animations that are independent of the main animation sequences.

## 2. Struct Definitions

Below are the key C-style struct definitions that describe the animation data within the M2 and `.anim` files. These are based on information from wowdev.wiki and the `warcraft-rs` project.

```c
// Represents a single animation sequence
struct M2Animation {
    uint16_t animation_id;          // Animation ID from AnimationData.dbc
    uint16_t sub_animation_id;      // Sub-animation ID, used for variations
    uint32_t length;                // Duration of the animation in milliseconds
    float moving_speed;             // Speed of the model during this animation
    uint32_t flags;                 // Animation flags
    int16_t probability;            // Likelihood of this animation playing
    uint16_t unused;                // Padding
    uint32_t d1;                    // Unknown
    uint32_t d2;                    // Unknown
    uint32_t playback_speed;        // Speed at which the animation is played
    float box_x, box_y, box_z;      // Bounding box for the animation
    float box_radius;               // Bounding sphere radius
    int16_t next_animation;         // Next animation in the sequence
    uint16_t index;                 // Index into the sequence array
};

// Generic animation track with keyframes
struct M2Track<T> {
    uint16_t interpolation_type;    // 0: none, 1: linear, 2: hermite, 3: bezier
    int16_t global_sequence;        // Global sequence ID, -1 if none
    M2Array<uint32_t> timestamps;   // Timestamps for each keyframe
    M2Array<T> values;              // Keyframe values
};

// Bone definition
struct M2Bone {
    int32_t key_bone_id;            // ID of the bone
    uint32_t flags;                 // Bone flags
    int16_t parent_bone;            // Parent bone index
    uint16_t submesh_id;            // Geometric data for the bone
    uint16_t unknown;               // Unknown
    M2Track<C3Vector> translation;  // Translation keyframes
    M2Track<C4Quaternion> rotation; // Rotation keyframes
    M2Track<C3Vector> scaling;      // Scaling keyframes
    C3Vector pivot;                 // Pivot point for the bone
};

// Quaternion for rotations
struct C4Quaternion {
    float x, y, z, w;
};

// 3D Vector for translations and scaling
struct C3Vector {
    float x, y, z;
};

// Resolved animation track data with keyframes loaded into memory
struct ResolvedTrack<T> {
    uint16_t interpolation_type;    // 0=None, 1=Linear, 2=Bezier, 3=Hermite
    int16_t global_sequence;        // Global sequence index (65535 = no global sequence)
    Vec<Vec<u32>> timestamps;       // Timestamps per animation sequence
    Vec<Vec<T>> values;             // Values per animation sequence
};
```

## 3. Constants and Flags

### Animation Flags

- `0x0001`: Looping animation
- `0x0002`: No blending
- `0x0004`: Stop at end
- `0x0008`: Start at random time
- `0x0010`: Play backwards
- `0x0020`: Play once and hold
- `0x0040`: Play once and stop
- `0x0080`: Primary bone sequence
- `0x0100`: Secondary bone sequence

### Interpolation Types

- `0`: None (no interpolation)
- `1`: Linear
- `2`: Hermite
- `3`: Bezier

## 4. Relationships to Other Systems

- **AnimationData.dbc:** This client database file contains the names and metadata for each animation ID used in the `M2Animation` struct.
- **M2 Model Format:** The animation system is intrinsically linked to the M2 model format, which defines the skeleton, bones, and animation sequences.
- **.skin Files:** These files define the submeshes and vertex data that are animated by the bone transformations.
- **Game Client:** The game client is responsible for loading the M2 and `.anim` files, processing the animation data, and rendering the animated models in the game world.

## 5. Related Tools

- **warcraft-rs:** A Rust library and CLI toolset for parsing and converting various WoW file formats, including M2 and `.anim` files.
- **WoW Model Viewer:** A popular tool for viewing and exploring WoW models and their animations.
- **Blender M2 Import/Export Add-ons:** Various Blender add-ons exist for importing, editing, and exporting M2 models, which often include support for animations.

## 6. Code Examples

### Pseudocode for Keyframe Interpolation

```rust
fn interpolate_track<T: Lerp>(track: &ResolvedTrack<T>, time: f64) -> T {
    let (timestamps, values) = track.get_keyframes_for_current_animation();

    if timestamps.is_empty() {
        return track.default_value;
    }

    let index = find_timestamp_index(timestamps, time);

    if index >= timestamps.len() - 1 {
        return values.last().clone();
    }

    let time1 = timestamps[index];
    let time2 = timestamps[index + 1];
    let value1 = &values[index];
    let value2 = &values[index + 1];

    let t = (time - time1) / (time2 - time1);

    match track.interpolation_type {
        0 => value1.clone(), // None
        1 => value1.lerp(value2, t), // Linear
        2 | 3 => { // Bezier/Hermite (simplified as linear)
            value1.lerp(value2, t)
        }
        _ => value1.clone(),
    }
}
```

## 7. Sources

- [wowdev.wiki M2](https://wowdev.wiki/M2)
- [wowdev.wiki M2/AnimationList](https://wowdev.wiki/M2/AnimationList)
- [warcraft-rs GitHub Repository](https://github.com/wowemulation-dev/warcraft-rs)
