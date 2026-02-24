# DBC Files Complete Reference for WoW 3.3.5a

This document provides a comprehensive reference for the structure and content of DBC (DataBaseClient) files used in World of Warcraft patch 3.3.5a (build 12340).

## DBC File Structure

DBC files are simple database files used by the client. They have a common header structure followed by data records and a string block.

### Header

```c
struct dbc_header
{
  uint32_t magic; // always 'WDBC'
  uint32_t record_count; // records per file
  uint32_t field_count; // fields per record
  uint32_t record_size; // sum (sizeof (field_type_i)) | 0 <= i < field_count. field_type_i is NOT defined in the files.
  uint32_t string_block_size;
};
```

### String Block

DBC records can contain strings. Strings are not stored in the record itself but in a separate string block at the end of the file. A record contains an offset into that block. Strings are null-terminated (C-style strings).

### Localization

DBC records can contain localized strings. For each localized string, there is a set of columns for each supported language and a bitmask indicating which languages are present.

## Key DBC File Structures

### Spell.dbc

```c
struct SpellEntry {
    uint32_t  ID;                                           // 0
    uint32_t  Category;                                     // 1 SpellCategory.dbc
    uint32_t  DispelType;                                   // 2 SpellDispelType.dbc
    uint32_t  Mechanic;                                     // 3 SpellMechanic.dbc
    uint32_t  Attributes;                                   // 4
    uint32_t  AttributesEx;                                 // 5
    uint32_t  AttributesExB;                                // 6
    uint32_t  AttributesExC;                                // 7
    uint32_t  AttributesExD;                                // 8
    uint32_t  AttributesExE;                                // 9
    uint32_t  AttributesExF;                                // 10
    uint32_t  AttributesExG;                                // 11
    uint32_t  ShapeshiftMask[2];                            // 12-13 SpellShapeshiftForm.dbc
    uint32_t  ShapeshiftExclude[2];                         // 14-15 SpellShapeshiftForm.dbc
    uint32_t  Targets;                                      // 16
    uint32_t  TargetCreatureType;                           // 17 CreatureType.dbc
    uint32_t  RequiresSpellFocus;                           // 18 SpellFocusObject.dbc
    uint32_t  FacingCasterFlags;                            // 19
    uint32_t  CasterAuraState;                              // 20
    uint32_t  TargetAuraState;                              // 21
    uint32_t  ExcludeCasterAuraState;                       // 22
    uint32_t  ExcludeTargetAuraState;                       // 23
    uint32_t  CasterAuraSpell;                              // 24 Spell.dbc
    uint32_t  TargetAuraSpell;                              // 25 Spell.dbc
    uint32_t  ExcludeCasterAuraSpell;                       // 26 Spell.dbc
    uint32_t  ExcludeTargetAuraSpell;                       // 27 Spell.dbc
    uint32_t  CastingTimeIndex;                             // 28 SpellCastTimes.dbc
    uint32_t  RecoveryTime;                                   // 29
    uint32_t  CategoryRecoveryTime;                         // 30
    uint32_t  InterruptFlags;                               // 31
    uint32_t  AuraInterruptFlags;                           // 32
    uint32_t  ChannelInterruptFlags;                        // 33
    uint32_t  ProcTypeMask;                                 // 34
    uint32_t  ProcChance;                                   // 35
    uint32_t  ProcCharges;                                  // 36
    uint32_t  MaxLevel;                                     // 37
    uint32_t  BaseLevel;                                    // 38
    uint32_t  SpellLevel;                                   // 39
    uint32_t  DurationIndex;                                // 40 SpellDuration.dbc
    int32_t   PowerType;                                    // 41
    uint32_t  ManaCost;                                     // 42
    uint32_t  ManaCostPerLevel;                             // 43
    uint32_t  ManaPerSecond;                                // 44
    uint32_t  ManaPerSecondPerLevel;                        // 45
    uint32_t  RangeIndex;                                   // 46 SpellRange.dbc
    float     Speed;                                        // 47
    uint32_t  ModalNextSpell;                               // 48 Spell.dbc
    uint32_t  CumulativeAura;                               // 49
    uint32_t  Totem[2];                                     // 50-51 Item.dbc
    int32_t   Reagent[8];                                   // 52-59 Item.dbc
    uint32_t  ReagentCount[8];                              // 60-67
    int32_t   EquippedItemClass;                            // 68 ItemClass.dbc
    int32_t   EquippedItemSubclass;                         // 69 ItemSubClass.dbc
    int32_t   EquippedItemInvTypes;                         // 70
    uint32_t  Effect[3];                                    // 71-73
    int32_t   EffectDieSides[3];                            // 74-76
    float     EffectRealPointsPerLevel[3];                  // 77-79
    int32_t   EffectBasePoints[3];                          // 80-82
    uint32_t  EffectMechanic[3];                            // 83-85 SpellMechanic.dbc
    uint32_t  EffectImplicitTargetA[3];                     // 86-88
    uint32_t  EffectImplicitTargetB[3];                     // 89-91
    uint32_t  EffectRadiusIndex[3];                         // 92-94 SpellRadius.dbc
    uint32_t  EffectAura[3];                                // 95-97
    uint32_t  EffectAuraPeriod[3];                          // 98-100
    float     EffectAmplitude[3];                           // 101-103
    uint32_t  EffectChainTargets[3];                        // 104-106
    uint32_t  EffectItemType[3];                            // 107-109 Item.dbc
    int32_t   EffectMiscValue[3];                           // 110-112
    int32_t   EffectMiscValueB[3];                          // 113-115
    int32_t   EffectTriggerSpell[3];                        // 116-118 Spell.dbc
    float     EffectPointsPerCombo[3];                      // 119-121
    uint32_t  EffectSpellClassMask_A[3];                    // 122-124
    uint32_t  EffectSpellClassMask_B[3];                    // 125-127
    uint32_t  EffectSpellClassMask_C[3];                    // 128-130
    uint32_t  SpellVisualID[2];                             // 131-132 SpellVisual.dbc
    uint32_t  SpellIconID;                                  // 133 SpellIcon.dbc
    uint32_t  ActiveIconID;                                 // 134 SpellIcon.dbc
    uint32_t  SpellPriority;                                // 135
    char*     Name[16];                                     // 136-151
    uint32_t  Name_lang_mask;                               // 152
    char*     NameSubtext[16];                              // 153-168
    uint32_t  NameSubtext_lang_mask;                        // 169
    char*     Description[16];                              // 170-185
    uint32_t  Description_lang_mask;                        // 186
    char*     AuraDescription[16];                          // 187-202
    uint32_t  AuraDescription_lang_mask;                    // 203
    uint32_t  ManaCostPct;                                  // 204
    uint32_t  StartRecoveryCategory;                        // 205 SpellCategory.dbc
    uint32_t  StartRecoveryTime;                            // 206
    uint32_t  MaxTargetLevel;                               // 207
    uint32_t  SpellClassSet;                                // 208
    uint32_t  SpellClassMask[3];                            // 209-211
    uint32_t  MaxTargets;                                   // 212
    uint32_t  DefenseType;                                  // 213
    uint32_t  PreventionType;                               // 214
    int32_t   StanceBarOrder;                               // 215
    float     EffectChainAmplitude[3];                      // 216-218
    uint32_t  MinFactionID;                                 // 219 Faction.dbc
    uint32_t  MinReputation;                                // 220
    uint32_t  RequiredAuraVision;                           // 221
    uint32_t  RequiredTotemCategoryID[2];                   // 222-223 TotemCategory.dbc
    int32_t   RequiredAreasID;                              // 224 AreaTable.dbc
    uint32_t  SchoolMask;                                   // 225
    uint32_t  RuneCostID;                                   // 226 SpellRuneCost.dbc
    uint32_t  SpellMissileID;                               // 227 SpellMissile.dbc
    int32_t   PowerDisplayID;                               // 228 PowerDisplay.dbc
    float     EffectBonusCoefficient[3];                    // 229-231
    int32_t   DescriptionVariablesID;                       // 232 SpellDescriptionVariables.dbc
    uint32_t  Difficulty;                                   // 233 SpellDifficulty.dbc
};
```

### Item.dbc

```c
struct ItemEntry {
    uint32_t  ID;                                           // 0
    uint32_t  ClassID;                                      // 1 ItemClass.dbc
    uint32_t  SubclassID;                                   // 2 ItemSubClass.dbc
    int32_t   SoundOverrideSubclassID;                      // 3
    int32_t   Material;                                     // 4 Material.dbc
    uint32_t  DisplayInfoID;                                // 5 ItemDisplayInfo.dbc
    uint32_t  InventoryType;                                // 6
    uint32_t  SheatheType;                                  // 7
};
```

### CreatureDisplayInfo.dbc

```c
struct CreatureDisplayInfoEntry {
    uint32_t  ID;                                           // 0
    uint32_t  ModelID;                                      // 1 CreatureModelData.dbc
    uint32_t  SoundID;                                      // 2 CreatureSoundData.dbc
    uint32_t  ExtendedDisplayInfoID;                        // 3 CreatureDisplayInfoExtra.dbc
    float     CreatureModelScale;                           // 4
    uint32_t  CreatureModelAlpha;                           // 5
    char*     TextureVariation[3];                          // 6-8
    char*     PortraitTextureName;                          // 9
    uint32_t  SizeClass;                                    // 10
    uint32_t  BloodID;                                      // 11 UnitBloodLevels.dbc
    uint32_t  NPCSoundID;                                   // 12 NPCSounds.dbc
    uint32_t  ParticleColorID;                              // 13 ParticleColor.dbc
    uint32_t  CreatureGeosetData;                           // 14
    uint32_t  ObjectEffectPackageID;                        // 15 ObjectEffectPackage.dbc
};
```

### CharSections.dbc

```c
struct CharSectionsEntry {
    uint32_t  ID;                                           // 0
    uint32_t  RaceID;                                       // 1 ChrRaces.dbc
    uint32_t  SexID;                                        // 2
    uint32_t  BaseSection;                                  // 3
    char*     TextureName[3];                               // 4-6
    uint32_t  Flags;                                        // 7
    uint32_t  VariationIndex;                               // 8
    uint32_t  ColorIndex;                                   // 9
};
```


## Complete List of DBC Files (3.3.5a)

This list is compiled from multiple sources and represents a comprehensive collection of DBC files present in World of Warcraft patch 3.3.5a.

*   Achievement
*   Achievement_Category
*   Achievement_Criteria
*   AnimationData
*   AreaGroup
*   AreaPOI
*   AreaTable
*   AreaTrigger
*   AttackAnimKits
*   AttackAnimTypes
*   AuctionHouse
*   BankBagSlotPrices
*   BannedAddOns
*   BarberShopStyle
*   BattlemasterList
*   CameraShakes
*   Cfg_Categories
*   Cfg_Configs
*   CharBaseInfo
*   CharHairGeosets
*   CharHairTextures
*   CharSections
*   CharStartOutfit
*   CharTitles
*   CharVariations
*   CharacterFacialHairStyles
*   ChatChannels
*   ChatProfanity
*   ChrClasses
*   ChrRaces
*   CinematicCamera
*   CinematicSequences
*   CreatureDisplayInfo
*   CreatureDisplayInfoExtra
*   CreatureFamily
*   CreatureModelData
*   CreatureMovementInfo
*   CreatureSoundData
*   CreatureSpellData
*   CreatureType
*   CurrencyCategory
*   CurrencyTypes
*   DanceMoves
*   DeathThudLookups
*   DeclinedWord
*   DeclinedWordCases
*   DestructibleModelData
*   DungeonEncounter
*   DungeonMap
*   DungeonMapChunk
*   DurabilityCosts
*   DurabilityQuality
*   Emotes
*   EmotesText
*   EmotesTextData
*   EmotesTextSound
*   EnvironmentalDamage
*   Exhaustion
*   Faction
*   FactionGroup
*   FactionTemplate
*   FileData
*   FootprintTextures
*   FootstepTerrainLookup
*   GMSurveyAnswers
*   GMSurveyCurrentSurvey
*   GMSurveyQuestions
*   GMSurveySurveys
*   GMTicketCategory
*   GameObjectArtKit
*   GameObjectDisplayInfo
*   GameTables
*   GameTips
*   GemProperties
*   GlyphProperties
*   GlyphSlot
*   GroundEffectDoodad
*   GroundEffectTexture
*   GtBarberShopCostBase
*   GtChanceToMeleeCrit
*   GtChanceToMeleeCritBase
*   GtChanceToSpellCrit
*   GtChanceToSpellCritBase
*   GtCombatRatings
*   GtNPCManaCostScaler
*   GtOCTClassCombatRatingScalar
*   GtOCTRegenHP
*   GtOCTRegenMP
*   GtRegenHPPerSpt
*   GtRegenMPPerSpt
*   HelmetGeosetVisData
*   HolidayDescriptions
*   HolidayNames
*   Holidays
*   Item
*   ItemBagFamily
*   ItemClass
*   ItemCondExtCosts
*   ItemDisplayInfo
*   ItemExtendedCost
*   ItemGroupSounds
*   ItemLimitCategory
*   ItemPetFood
*   ItemPurchaseGroup
*   ItemRandomProperties
*   ItemRandomSuffix
*   ItemSet
*   ItemSubClass
*   ItemSubClassMask
*   ItemVisualEffects
*   ItemVisuals
*   LFGDungeonExpansion
*   LanguageWords
*   Languages
*   LfgDungeonGroup
*   LfgDungeons
*   Light
*   LightFloatBand
*   LightIntBand
*   LightParams
*   LightSkybox
*   LiquidMaterial
*   LiquidType
*   LoadingScreenTaxiSplines
*   LoadingScreens
*   Lock
*   LockType
*   MailTemplate
*   Map
*   MapDifficulty
*   Material
*   Movie
*   MovieFileData
*   MovieVariation
*   NPCSounds
*   NameGen
*   NamesProfanity
*   NamesReserved
*   ObjectEffect
*   ObjectEffectGroup
*   ObjectEffectModifier
*   ObjectEffectPackage
*   ObjectEffectPackageElem
*   OverrideSpellData
*   Package
*   PageTextMaterial
*   PaperDollItemFrame
*   ParticleColor
*   PetPersonality
*   PetitionType
*   PowerDisplay
*   PvpDifficulty
*   QuestFactionReward
*   QuestInfo
*   QuestSort
*   QuestXP
*   RandPropPoints
*   Resistances
*   ScalingStatDistribution
*   ScalingStatValues
*   ScreenEffect
*   ServerMessages
*   SheatheSoundLookups
*   SkillCostsData
*   SkillLine
*   SkillLineAbility
*   SkillLineCategory
*   SkillRaceClassInfo
*   SkillTiers
*   SoundAmbience
*   SoundEmitters
*   SoundEntries
*   SoundEntriesAdvanced
*   SoundFilter
*   SoundFilterElem
*   SoundProviderPreferences
*   SoundSamplePreferences
*   SoundWaterType
*   SpamMessages
*   Spell
*   SpellCastTimes
*   SpellCategory
*   SpellChainEffects
*   SpellDescriptionVariables
*   SpellDifficulty
*   SpellDispelType
*   SpellDuration
*   SpellEffectCameraShakes
*   SpellFocusObject
*   SpellIcon
*   SpellItemEnchantment
*   SpellItemEnchantmentCondition
*   SpellMechanic
*   SpellMissile
*   SpellMissileMotion
*   SpellRadius
*   SpellRange
*   SpellRuneCost
*   SpellShapeshiftForm
*   SpellVisual
*   SpellVisualEffectName
*   SpellVisualKit
*   SpellVisualKitAreaModel
*   SpellVisualKitModelAttach
*   SpellVisualPrecastTransitions
*   StableSlotPrices
*   Startup_Strings
*   Stationery
*   StringLookups
*   SummonProperties
*   Talent
*   TalentTab
*   TaxiNodes
*   TaxiPath
*   TaxiPathNode
*   TeamContributionPoints
*   TerrainType
*   TerrainTypeSounds
*   TotemCategory
*   TransportAnimation
*   TransportPhysics
*   TransportRotation
*   UISoundLookups
*   UnitBlood
*   UnitBloodLevels
*   Vehicle
*   VehicleSeat
*   VehicleUIIndSeat
*   VehicleUIIndicator
*   VideoHardware
*   VocalUISounds
*   WMOAreaTable
*   WeaponImpactSounds
*   WeaponSwingSounds2
*   Weather
*   WorldChunkSounds
*   WorldMapArea
*   WorldMapContinent
*   WorldMapOverlay
*   WorldMapTransforms
*   WorldSafeLocs
*   WorldStateUI
*   WorldStateZoneSounds
*   WowError_Strings
*   ZoneIntroMusicTable
*   ZoneMusic
_Note: This list is not exhaustive and is based on available documentation._

## Constants, Enums, and Flags

DBC files use various constants, enums, and flags to represent game data. Here are some examples from `Spell.dbc`:

### Spell Attributes (Attributes, AttributesEx, etc.)

These fields are bitmasks that define a spell's behavior.

*   `SPELL_ATTR0_UNK1`: Unknown attribute 1
*   `SPELL_ATTR0_ON_NEXT_SWING`: On next swing
*   `SPELL_ATTR0_IS_REPLENISHMENT`: Is replenishment
*   `SPELL_ATTR0_ABILITY`: Ability
*   `SPELL_ATTR0_TRADESPELL`: Trade spell
*   `SPELL_ATTR0_PASSIVE`: Passive

### DispelType

| ID | Name                |
|----|---------------------|
| 0  | DISPEL_NONE         |
| 1  | DISPEL_MAGIC        |
| 2  | DISPEL_CURSE        |
| 3  | DISPEL_DISEASE      |
| 4  | DISPEL_POISON       |
| 5  | DISPEL_STEALTH      |
| 6  | DISPEL_INVISIBILITY |
| 7  | DISPEL_ALL          |
| 8  | DISPEL_SPE_NPC_ONLY |
| 9  | DISPEL_ENRAGE       |
| 10 | DISPEL_ZG_TICKET    |
| 11 | DESPEL_OLD_UNUSED   |

### Mechanic

| ID | Name                   |
|----|------------------------|
| 0  | MECHANIC_NONE          |
| 1  | MECHANIC_CHARM         |
| 2  | MECHANIC_DISORIENTED   |
| 3  | MECHANIC_DISARM        |
| 4  | MECHANIC_DISTRACT      |
| 5  | MECHANIC_FEAR          |

## Relationships to Other Systems/Formats

DBC files are central to the game client's data representation and have numerous relationships with other systems:

*   **Server-side Databases:** While DBC files are client-side, they often mirror or are supplemented by server-side database tables (e.g., `creature_template`, `item_template`). The server is the ultimate authority on game state.
*   **M2 and WMO Models:** DBC files like `CreatureDisplayInfo.dbc` and `GameObjectDisplayInfo.dbc` reference M2 (model) files that define the visual appearance of creatures and game objects.
*   **Game Logic:** The game client's executable contains the logic that reads and interprets the data in the DBC files to display the world, handle spell effects, and manage other game systems.
*   **UI (Interface):** The user interface, defined in XML and Lua, often queries DBC data to display information to the player (e.g., item stats, spell descriptions).

## Tools for Working with DBC Files

Several community-developed tools are available for viewing and editing DBC files:

*   **WDBX Editor:** A popular and powerful DBC editor that supports various WoW versions, including 3.3.5a. It allows for easy viewing, editing, and exporting of DBC data.
*   **DBC Viewer/Editor:** A generic term for several tools that provide basic viewing and editing capabilities for DBC files.
*   **TrinityCore DBC Tools:** The TrinityCore project includes various tools and scripts for working with DBC files, often used in the context of server development.
*   **wow_dbc (Rust library):** A Rust library for reading and writing DBC files for various WoW versions.


## Sources

*   https://wowdev.wiki/DBC
*   https://wowdev.wiki/Category:DBC_WotLK
*   https://rgtc.fandom.com/wiki/Files:DBC
*   https://trinitycore.info/files/DBC/335/spell
*   https://trinitycore.info/files/DBC/335/item
*   https://trinitycore.info/files/DBC/335/creaturedisplayinfo
*   https://trinitycore.info/files/DBC/335/charsections
*   https://github.com/dawidcxx/wow-file-tools
*   https://trinitycore.info/en/files/DBC/335/DBC
