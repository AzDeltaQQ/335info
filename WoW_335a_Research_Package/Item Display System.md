# Item Display System

summary: The Item Display System in World of Warcraft 3.3.5a is a complex and data-driven system responsible for rendering items on characters and in the game world. It relies on a combination of DBC files, primarily ItemDisplayInfo.dbc, and model data in M2 and .skin formats to control everything from an item's appearance and icon to its visual effects and how it modifies a character's geometry. This system uses geosets to dynamically alter character models based on equipped armor, with a hardcoded priority system to resolve conflicts between different items. Weapon effects and enchantments are handled through a system of attachment points on the M2 models, linked to visual effect definitions in other DBC files.

struct_definitions: ```c
struct ItemDisplayInfoRec {
  uint32_t  m_ID;
  char*     m_leftModelPath;
  char*     m_rightModelPath;
  char*     m_leftModelTexturePath;
  char*     m_rightModelTexturePath;
  char*     m_icon1;
  char*     m_icon2;
  uint32_t  m_geosetGroup[3];
  uint32_t  m_flags; // &1: EmblazonedTabard, &2: hides underwear, &4: event specific
  uint32_t  m_spellVisualID;
  uint32_t  m_groupSoundIndex; // foreign key to ItemGroupSounds.dbc
  uint32_t  m_helmetGeosetVisMale; // foreign key to HelmetGeosetVisData.dbc
  uint32_t  m_helmetGeosetVisFemale; // foreign key to HelmetGeosetVisData.dbc
  char*     m_upperArmTexture;
  char*     m_lowerArmTexture;
  char*     m_handsTexture;
  char*     m_upperTorsoTexture;
  char*     m_lowerTorsoTexture;
  char*     m_upperLegTexture;
  char*     m_lowerLegTexture;
  char*     m_footTexture;
  uint32_t  m_itemVisual; // foreign key to ItemVisuals.dbc
  uint32_t  m_particleColorID; // foreign key to ParticleColor.dbc
};

struct M2SkinProfile {
  uint32_t                magic;         // 'SKIN'
  M2Array<unsigned short> vertices;
  M2Array<unsigned short> indices;
  M2Array<ubyte4>         bones;
  M2Array<M2SkinSection>  submeshes;
  M2Array<M2Batch>        batches;
  uint32_t                boneCountMax;
  M2Array<M2ShadowBatch>  shadow_batches;
};
```

constants_and_flags: 
ItemDisplayInfo.dbc Flags:
EmblazonedTabard = 0x1
hides_underwear = 0x2
event_specific = 0x4

Geoset Groups:
Head_geosetGroup_0 = 2700
Head_geosetGroup_1 = 2101
Shoulder_geosetGroup_0 = 2601
Shirt_geosetGroup_0 = 801
Shirt_geosetGroup_1 = 1001
Chest_geosetGroup_0 = 801
Chest_geosetGroup_1 = 1001
Chest_geosetGroup_2 = 1301
Chest_geosetGroup_3 = 2201
Chest_geosetGroup_4 = 2801
Waist_geosetGroup_0 = 1801
Pants_geosetGroup_0 = 1101
Pants_geosetGroup_1 = 901
Pants_geosetGroup_2 = 1301
Boots_geosetGroup_0 = 501
Boots_geosetGroup_1 = 2000
Gloves_geosetGroup_0 = 401
Gloves_geosetGroup_1 = 2301
Cape_geosetGroup_0 = 1501
Tabard_geosetGroup_0 = 1201

relationships: 
- ItemDisplayInfo.dbc is the central file, linking to models, textures, and other DBC files.
- M2 files define the 3D models for items, including their geometry, animations, and attachment points.
- .skin files define the different levels of detail (LODs) for a model and how textures are applied.
- ItemVisuals.dbc links visual effects to the attachment points on a weapon model.
- ItemVisualEffects.dbc defines the models used for visual effects.
- SpellVisual.dbc defines the visual appearance of spells and some enchantment effects.
- HelmetGeosetVisData.dbc controls the visibility of helmet geosets.
- ItemGroupSounds.dbc defines the sounds that items make.
- ParticleColor.dbc defines the colors of particles used in visual effects.

tools: 
DBC Editor: For viewing and editing DBC files like ItemDisplayInfo.dbc.
WoW Model Viewer: For viewing M2 models and their animations.
M2/Skin file editors: For modifying the 3D models and their textures.

code_examples: ```c
// Pseudocode for geoset priority
function resolve_geosets(gloves, chest, shirt, belt, tabard, pants, boots) {
  // Sleeve geoset
  if (gloves.geosetGroup[0] != 0) {
    apply_geoset(gloves.geosetGroup[0]);
  } else if (chest.geosetGroup[0] != 0) {
    apply_geoset(chest.geosetGroup[0]);
  } else {
    apply_geoset(shirt.geosetGroup[0]);
  }

  // Belt/Tabard geoset
  if (belt.geosetGroup[0] != 0) {
    apply_geoset(belt.geosetGroup[0]);
  } else {
    apply_geoset(tabard.geosetGroup[0]);
  }

  // Robe/Pants/Boots geoset
  if (chest.geosetGroup[2] != 0) {
    apply_geoset(chest.geosetGroup[2]);
  } else if (pants.geosetGroup[2] != 0) {
    apply_geoset(pants.geosetGroup[2]);
  } else if (boots.geosetGroup[0] != 0) {
    apply_geoset(boots.geosetGroup[0]);
  } else {
    apply_geoset(pants.geosetGroup[1]);
  }
}
```

sources: 
https://wowdev.wiki/DB/ItemDisplayInfo
https://wowdev.wiki/M2
https://wowdev.wiki/M2/.skin
