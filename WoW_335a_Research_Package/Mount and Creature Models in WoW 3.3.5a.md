# Mount and Creature Models in WoW 3.3.5a

This document details the technical specifications for mount and creature models in World of Warcraft patch 3.3.5a, focusing on the `CreatureModelData.dbc`, `CreatureDisplayInfo.dbc`, and M2 model format.

## CreatureModelData.dbc

This DBC file contains the core model data for creatures.

### 3.3.5.12340 Structure

|Column |Field |Type |Notes |
|---|---|---|---|
|1 |ID |Integer | Unique identifier for the model data.|
|2 |Flags |Integer | Bitmask of flags for model properties.|
|3 |ModelPath |String | Path to the M2 model file.|
|4 |sizeClass |Integer | Affects blood splat size and death thud sound.|
|5 |modelScale |Float | The base scale of the model.|
|6 |BloodLevel |iRefID | Foreign key to `UnitBloodLevels.dbc`.|
|7 |Footprint |iRefID | Foreign key to `FootprintTextures.dbc`.|
|8 |footprintTextureLength |Float | Length of the footprint texture.|
|9 |footprintTextureWidth |Float | Width of the footprint texture.|
|10 |footprintParticleScale |Float | Scale of the footprint particle effect.|
|11 |foleyMaterialID |Integer | ID of the foley material.|
|12 |footstepShakeSize |iRefID | Foreign key to `CameraShakes.dbc` for footstep camera shake.|
|13 |deathThudShakeSize |iRefID | Foreign key to `CameraShakes.dbc` for death thud camera shake.|
|14 |SoundData |iRefID | Foreign key to `CreatureSoundData.dbc`.|
|15 |CollisionWidth |Float | The width of the model's collision box.|
|16 |CollisionHeight |Float | The height of the model's collision box.|
|17 |mountHeight |Float | The height at which a rider is positioned on the mount.|
|18 |geoBoxMin |Vec3F | The minimum coordinates of the model's geometry box.|
|21 |geoBoxMax |Vec3F | The maximum coordinates of the model's geometry box.|
|24 |worldEffectScale |Float | Scale of world effects on the model.|
|25 |attachedEffectScale |Float | Scale of effects attached to the model.|
|26 |missileCollisionRadius |Float | Radius of the missile collision sphere.|
|27 |missileCollisionPush |Float | Push value for missile collision.|
|28 |missileCollisionRaise |Float | Raise value for missile collision.|

### Flags

```
0x00001 = No Footprint Particles
0x00002 = No Breath Particles
0x00004 = Is Player Model
0x00008 = No Attached Weapons
0x00010 = No Footprint Trail Textures
0x00020 = Disable Highlight
0x00040 = Can Mount while Transformed as this
0x00080 = Disable Scale Interpolation
0x00100 = Force Projected Tex. (EXPENSIVE)
0x00200 = Can Jump In Place As Mount
0x00400 = AI cannot use walk backwards anim
0x00800 = Ignore SpineLow for SplitBody
0x01000 = Ignore Head for SplitBody
0x02000 = Ignore SpineLow for SplitBody when Flying
0x04000 = Ignore Head for SplitBody when Flying
0x08000 = Use 'wheel' animation on unit wheel bones
0x10000 = Is HD Model
0x20000 = Suppress Emitters on Low Settings
```

## CreatureDisplayInfo.dbc

This DBC file links a creature to its model and other display-related properties.

### 3.3.5.12340 Structure

|Column |Field |Type |Notes |
|---|---|---|---|
|1 |ID |Integer | Unique identifier for the display info.|
|2 |Model |iRefID | Foreign key to `CreatureModelData.dbc`.|
|3 |Sound |iRefID | Foreign key to `CreatureSoundData.dbc`.|
|4 |ExtraDisplayInformation |iRefID | Foreign key to `CreatureDisplayInfoExtra.dbc`.|
|5 |Scale |Float | The scale of the creature.|
|6 |Opacity |Integer | The opacity of the creature (0-255).|
|7 |Texture1 |String | Path to the first alternate texture.|
|8 |Texture2 |String | Path to the second alternate texture.|
|9 |Texture3 |String | Path to the third alternate texture.|
|10 |portraitTextureName |String | Path to the portrait texture.|
|11 |bloodLevel |iRefID | Foreign key to `UnitBloodLevels.dbc`.|
|12 |blood |iRefID | Foreign key to `UnitBlood.dbc`.|
|13 |NPCSounds |iRefID | Foreign key to `NPCSounds.dbc`.|
|14 |Particles |iRefID | Foreign key to `ParticleColor.dbc`.|
|15 |creatureGeosetData |Integer | Geoset data for the creature.|
|16 |objectEffectPackageID |iRefID | Foreign key to `ObjectEffectPackage.dbc`.|

## M2 Model Format

The M2 format is the primary 3D model format used in World of Warcraft.

### Header

The M2 header contains metadata and offsets to various data blocks within the file.

```c
struct M2Header {
    uint32_t magic; // "MD20"
    uint32_t version;
    M2Array<char> name;
    uint32_t global_flags;
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
    // ... and so on
};
```

### Attachments

Attachments are points within the M2 model used to attach other models or effects, such as a rider on a mount.

```c
struct M2Attachment {
    uint32_t id; // ID of the attachment
    uint16_t bone; // bone to attach to
    uint16_t unknown; // always 0
    C3Vector position; // position of the attachment
    M2Track<uint8_t> data; // animated attachment data
};
```
