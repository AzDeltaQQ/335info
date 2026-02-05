# CharSections.dbc Research

## Structure (3.3.5a)

| Column | Field | Type | Notes |
| --- | --- | --- | --- |
| 1 | ID | Integer |
| 2 | Race | iRefID | -> ChrRaces.dbc |
| 3 | Gender | Boolean | Male = 0, female = 1. |
| 4 | GeneralType | Integer | See below. |
| 5 | Texture1 | String |
| 6 | Texture2 | String |
| 7 | Texture3 | String |
| 8 | Flags | Integer | See Below |
| 9 | Type | Integer |
| 10 | Variation | Integer | These are the variations / colors of the Type. |

## Field Descriptions by GeneralType

| GeneralType | 0 - _Base Skin_ | 1 - _Face_ | 2 - _Facial Hair_ | 3 - _Hair_ | 4 - _Underwear_ |
| --- | --- | --- | --- | --- | --- |
| **Type** | - | FaceType | FacialHairType | HairStyle | - |
| **Color** | SkinColor | SkinColor | HairColor | HairColor | SkinColor |
| **Texture1** | SkinTexture | FaceLowerTexture | FacialLowerTexture | HairTexture | PelvisTexture |
| **Texture2** | ExtraSkinTexture | FaceUpperTexture | FacialUpperTexture | ScalpLowerTexture | TorsoTexture |
| **Texture3** | - | - | - | ScalpUpperTexture | - |

## Flags

| Flag | Description |
| --- | --- |
| 0x1 | Playable/CharacterCreate |
| 0x2 | Barbershop |
| 0x4 | Death Knight texture |
| 0x8 | NPC skin |
| 0x10 | Regular skin |
| 0x100 | Silhouette texture |


## CharacterFacialHairStyles.dbc Research

### Structure (3.3.5a)

| Column | Field | Type | Notes |
| --- | --- | --- | --- |
| 1 | Race | iRefID | -> ChrRaces.dbc |
| 2 | Gender | Integer | 0 = Male, 1 = Female |
| 3 | Variation | Integer | This ID is unique per gender, per race . |
| 4 | Geoset_1 | Integer | group 1 -- This is the small part of Geoset identificator. Determines mixing of different facial elements, on Night Elves this is chin hair. |
| 5 | Geoset_2 | Integer | group 3 -- ... on Night Elves, this controls eyebrows |
| 6 | Geoset_3 | Integer | group 2 -- ... on Night Elves, this controls mustache |
| 7 | Geoset_4 | Integer | group 16 |
| 8 | Geoset_5 | Integer | group 17 -- small\big ears for BloodElves(geo 701,702). |


## CharHairGeosets.dbc Research

### Structure (3.3.5a)

```cpp
struct CharHairGeosetsRec {
  uint32_t m_ID;
  uint32_t m_RaceID;
  uint32_t m_SexID;
  uint32_t m_VariationID;
  uint32_t m_GeosetID;
  uint32_t m_Showscalp;
};
```

### Table Structure (3.3.5a)

| Column | Field | Type | Notes |
| --- | --- | --- | --- |
| 1 | ID | Integer | |
| 2 | Race | iRefID | -> ChrRaces.dbc |
| 3 | Gender | Boolean | 0 = Male, 1 = Female |
| 4 | HairType | Integer | |
| 5 | Geoset | Integer | Defines the connection between HairType and Geoset number in MDX model |
| 6 | Bald | Boolean | If this hairstyle bald or not . |


## CreatureDisplayInfo.dbc Research

### Table Structure (3.3.5a)

| Column | Field | Type | Notes |
| --- | --- | --- | --- |
| 1 | ID | Integer | |
| 2 | Model | iRefID | -> CreatureModelData.dbc |
| 3 | Sound | iRefID | -> CreatureSoundData.dbc |
| 4 | ExtraDisplayInformation | iRefID | -> CreatureDisplayInfoExtra.dbc |
| 5 | Scale | Float | |
| 6 | Opacity | Integer | 0-255 |
| 7 | Texture1 | String | |
| 8 | Texture2 | String | |
| 9 | Texture3 | String | |
| 10 | portraitTextureName | String | |
| 11 | bloodLevel | iRefID | -> UnitBloodLevels.dbc |
| 12 | blood | iRefID | -> UnitBlood.dbc |
| 13 | NPCSounds | iRefID | -> NPCSounds.dbc |
| 14 | Particles | iRefID | -> ParticleColor.dbc |
| 15 | creatureGeosetData | Integer | |
| 16 | objectEffectPackageID | iRefID | -> ObjectEffectPackage.dbc |


## CreatureDisplayInfoExtra.dbc Research

### Table Structure (3.3.5a)

| Column | Field | Type | Notes |
| --- | --- | --- | --- |
| 1 | ID | Integer | |
| 2 | Race | iRefID | -> ChrRaces.dbc |
| 3 | Gender | Boolean | 0 for Male, 1 for Female |
| 4 | SkinColor | Integer | -> CharSections.dbc |
| 5 | FaceType | Integer | -> CharSections.dbc |
| 6 | HairStyle | iRefID | -> CharHairGeosets.dbc |
| 7 | HairColor | iRefID | -> CharSections.dbc, where GeneralType=3 |
| 8 | BeardStyle | Integer | -> CharacterFacialHairStyles.dbc |
| 9 | Helm | iRefID | -> ItemDisplayInfo.dbc |
| 10 | Shoulder | iRefID | -> ItemDisplayInfo.dbc |
| 11 | Shirt | iRefID | -> ItemDisplayInfo.dbc |
| 12 | Cuirass | iRefID | -> ItemDisplayInfo.dbc |
| 13 | Belt | iRefID | -> ItemDisplayInfo.dbc |
| 14 | Legs | iRefID | -> ItemDisplayInfo.dbc |
| 15 | Boots | iRefID | -> ItemDisplayInfo.dbc |
| 16 | Wrist | iRefID | -> ItemDisplayInfo.dbc |
| 17 | Gloves | iRefID | -> ItemDisplayInfo.dbc |
| 18 | Tabard | iRefID | -> ItemDisplayInfo.dbc |
| 19 | Cape | iRefID | -> ItemDisplayInfo.dbc |
| 20 | CanEquip | Boolean | |
| 21 | Texture | String | .blp texture file |
