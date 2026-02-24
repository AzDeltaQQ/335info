
# Spell Visual System

## SpellVisual.dbc

Visuals used at each part of a spell cast, the missile of the spell, etc.

### Structure 3.3.2

```c
struct SpellVisualRec {
    uint32_t m_ID;
    uint32_t m_precastKit; // The visual effect used for the casting
    uint32_t m_castKit; // The visual effect used for the cast where the spell occurs
    uint32_t m_impactKit; // The visual effect used for the target
    uint32_t m_stateKit; // The visual effect that can be seen while this buff/debuff remains on the target
    uint32_t m_stateDoneKit;
    uint32_t m_channelKit; // The visual effect used while channeling a spell
    uint32_t m_hasMissile; // Boolean
    uint32_t m_missileModel; // The visual effect used for the spell missile
    uint32_t m_missilePathType;
    uint32_t m_missileDestinationAttachment;
    uint32_t m_missileSound;
    uint32_t m_animEventSoundID;
    uint32_t m_flags; // The visual effect used at the center of an AOE spell probably used for other things as well
    uint32_t m_casterImpactKit;
    uint32_t m_targetImpactKit; // Previous documentation had swapped this with m_flags.
    uint32_t m_missileAttachment;
    uint32_t m_missileFollowGroundHeight;
    uint32_t m_missileFollowGroundDropSpeed;
    uint32_t m_missileFollowGroundApproach;
    uint32_t m_missileFollowGroundFlags;
    uint32_t m_missileMotion;
    uint32_t m_missileTargetingKit;
    uint32_t m_instantAreaKit;
    uint32_t m_impactAreaKit;
    uint32_t m_persistentAreaKit; // The visual effect for AOE spells
    float m_missileCastOffset[3];
    float m_missileImpactOffset[3];
};
```

### 0.5.3.3368

```c
struct SpellVisualRec {
  uint32_t m_ID;
  uint32_t m_precastKit;
  uint32_t m_castKit;
  uint32_t m_impactKit;
  uint32_t m_stateKit;
  uint32_t m_channelKit;
  uint32_t m_hasMissile;
  uint32_t m_missileModel;
  uint32_t m_missilePathType;
  uint32_t m_missileDestinationAttachment;
  uint32_t m_missileSound;
  uint32_t m_hasAreaEffect;
  uint32_t m_areaModel;
  uint32_t m_areaKit;
  uint32_t m_animEventSoundID;
  uint8_t m_weaponTrailRed;
  uint8_t m_weaponTrailGreen;
  uint8_t m_weaponTrailBlue;
  uint8_t m_weaponTrailAlpha;
  uint8_t m_weaponTrailFadeoutRate;
  uint32_t m_weaponTrailDuration;
};
```

## SpellVisualKit.dbc

This file is used to attach different spell effects from `SpellVisualEffectName.dbc` to certain points on the model. Each column corresponds to areas on a model such as hands, chest and base.

Besides the obvious effects for different attachments, there is "character procedures" (`m_characterProcedure`, `m_charProc[4]`) which have additional non-`DB/SpellVisualEffectName` effects, like equipping items or coloring the target.

### 0.5.3.3368

```c
struct SpellVisualKitRec {
  uint32_t m_ID;
  uint32_t m_kitType;
  uint32_t m_anim;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_headEffect;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_chestEffect;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_baseEffect;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_leftHandEffect;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_rightHandEffect;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_breathEffect;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_specialEffect[3];
  int32_t m_characterProcedure;
  float m_characterParam[4];
  foreign_key<uint32_t, &SoundEntriesRec::m_ID> m_soundID;
  foreign_key<uint32_t, &SpellEffectCameraShakesRec::m_ID> m_shakeID;
};
```

**Character Procedures**

| Index | Procedure | Param0 | Param1 | Param2 | Param3 | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| -1 | None | | | | | |
| 0 | Chain (Spell) | (uint32_t) SpellChainEffectsRec::m_ID | targetCount | forever | | floorf(targetCount) must be 1…3. `forever = (value ≥ 1f)`, lightning ignores deathTime |
| 1 | Color | color | period | endTime | fadeOutTime | endTime and fadeOutTime are float secs that are stored as uint32_t ms |
| 2 | Scale | scale | | | | |
| 4 | Emissive | color | | | | |
| 6 | Eclipse | color | fadeInTime | | | |
| 7 | Stand/Walk Anim | standAnim | walkAnim | | | `s_standAnims[standAnim]` or `s_walkAnims[walkAnim]` |
| 8 | Weapon Trail | (uint32_t) color | (int32_t) fadeOutRate | (uint32_t) duration | (uint32_t) alpha | `trailColor = (CImVector)(color | (alpha << 24))` |
| 9 | Blizzard | (uint32_t) modelName | emissionRate | | | `shardModel = MODEL_NAMES[modelName]` |
| 10 | Fishing Line | | | | | |

```c
const ANIMENUMERATION s_standAnims[3] { ANIM_STAND, ANIM_STEALTHSTAND, ANIM_DEATH }; // 0x0, 0x78, 0x1
const ANIMENUMERATION s_walkAnims[2]  { ANIM_WALK, ANIM_STEALTHWALK }; // 0x4, 0x77

const char *MODEL_NAMES[] =
{
  "Spells\\FlamestrikeSmall_Impact_Base.mdx",
  "Spells\\CallLightning_Impact.mdx",
  "Spells\\RainOfFire_Impact_Base.mdx",
  "Spells\\Blizzard_Impact_Base.mdx"
};

struct SPELLEFFECTDESC
{
  SpellVisualKitRec *kitPtr;
  CImVector color;
  float scale;
  uint32_t startTime;
  uint32_t fadeInTime;
  uint32_t fadeOutTime;
  uint32_t endTime;
  uint32_t curTime;
  float period;
  int32_t standAnim;
  int32_t walkAnim;
  char isOneShot;
  LightningObject *lightningObjs[3]; // used by Chain
};
```

### unknown version

```c
struct SpellVisualKitRec {
  uint32_t m_ID;
  foreign_key<uint32_t, &AnimationDataRec::m_ID> m_startAnimID;
  foreign_key<uint32_t, &AnimationDataRec::m_ID> m_animID;
#if version > ?
  foreign_key<uint32_t, &AnimKitRec::m_ID> m_animKitID;
#endif
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_headEffect;        // attached to Head
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_chestEffect;       // attached to Chest
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_baseEffect;        // attached to Base
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_leftHandEffect;    // attached to SpellLeftHand
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_rightHandEffect;   // attached to SpellRightHand
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_breathEffect;      // attached to Breath
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_leftWeaponEffect;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_rightWeaponEffect;
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_specialEffect[3];  // attached to Special1…3
  foreign_key<uint32_t, &SpellVisualEffectNameRec::m_ID> m_worldEffect;
  foreign_key<uint32_t, &SoundEntriesRec::m_ID> m_soundID;
  foreign_key<uint32_t, &SpellEffectCameraShakesRec::m_ID> m_shakeID;
  uint32_t m_charProc[4];
  float m_charParamZero[4];
  float m_charParamOne[4];
  float m_charParamTwo[4];
  float m_charParamThree[4];
#if version > ?
  uint32_t m_flags;
#endif
};
```

**Flags**

| Value | Description |
| --- | --- |
| 0x40 | LOOP_ANIMATION |

## SpellVisualEffectName.dbc

List of IDs and names for various models to be used.

### 3.3.5

```c
struct SpellVisualEffectNameRec {
  uint32_t m_ID;
  stringref name;
  stringref m_fileName;
  float m_areaEffectSize;
  float scale;
  float minAllowedScale;
  float minAllowedScale;
};
```

### 0.5.3.3368

```c
struct SpellVisualEffectNameRec {
  uint32_t m_ID;
  stringref m_fileName;
  uint32_t m_specialID;             // UNITEFFECTSPECIALS
  uint32_t m_specialAttachPoint;    // if < 0xC UNITEFFECTATTACHPPOINT else ??? `UnitEffectOneShot`
  uint32_t m_areaEffectSize;           // if the model's bounding sphere radius <= 0.001, scales the model `CGDynamicObject_C::UpdateModelLoadStatus`
  uint32_t m_VisualEffectNameFlags; // &1: `ONESHOTSTANDALONEEFFECTNODE::ReleaseDeathHolds`, &4: after anim seq ends `OneShotEndHandler`, &8: spawns a world object `UnitEffectIsAuraWorldObject`
};

enum UNITEFFECTSPECIALS
{
  SPECIALEFFECT_LOOTART = 0x0,
  SPECIALEFFECT_LEVELUP = 0x1,
  SPECIALEFFECT_FOOTSTEPSPRAYSNOW = 0x2,
  SPECIALEFFECT_FOOTSTEPSPRAYSNOWWALK = 0x3,
  SPECIALEFFECT_FOOTSTEPDIRT = 0x4,
  SPECIALEFFECT_FOOTSTEPDIRTWALK = 0x5,
  SPECIALEFFECT_COLDBREATH = 0x6,
  SPECIALEFFECT_UNDERWATERBUBBLES = 0x7,
  SPECIALEFFECT_COMBATBLOODSPURTFRONT = 0x8,
  SPECIALEFFECT_UNUSED = 0x9,
  SPECIALEFFECT_COMBATBLOODSPURTBACK = 0xA,
  SPECIALEFFECT_HITSPLATPHYSICALSMALL = 0xB,
  SPECIALEFFECT_HITSPLATPHYSICALBIG = 0xC,
  SPECIALEFFECT_HITSPLATHOLYSMALL = 0xD,
  SPECIALEFFECT_HITSPLATHOLYBIG = 0xE,
  SPECIALEFFECT_HITSPLATFIRESMALL = 0xF,
  SPECIALEFFECT_HITSPLATFIREBIG = 0x10,
  SPECIALEFFECT_HITSPLATNATURESMALL = 0x11,
  SPECIALEFFECT_HITSPLATNATUREBIG = 0x12,
  SPECIALEFFECT_HITSPLATFROSTSMALL = 0x13,
  SPECIALEFFECT_HITSPLATFROSTBIG = 0x14,
  SPECIALEFFECT_HITSPLATSHADOWSMALL = 0x15,
  SPECIALEFFECT_HITSPLATSHADOWBIG = 0x16,
  SPECIALEFFECT_COMBATBLOODSPURTFRONTLARGE = 0x17,
  SPECIALEFFECT_COMBATBLOODSPURTBACKLARGE = 0x18,
  SPECIALEFFECT_FIZZLEPHYSICAL = 0x19,
  SPECIALEFFECT_FIZZLEHOLY = 0x1A,
  SPECIALEFFECT_FIZZLEFIRE = 0x1B,
  SPECIALEFFECT_FIZZLENATURE = 0x1C,
  SPECIALEFFECT_FIZZLEFROST = 0x1D,
  SPECIALEFFECT_FIZZLESHADOW = 0x1E,
  SPECIALEFFECT_COMBATBLOODSPURTGREENFRONT = 0x1F,
  SPECIALEFFECT_COMBATBLOODSPURTGREENFRONTLARGE = 0x20,
  SPECIALEFFECT_COMBATBLOODSPURTGREENBACK = 0x21,
  SPECIALEFFECT_COMBATBLOODSPURTGREENBACKLARGE = 0x22,
  SPECIALEFFECT_FOOTSTEPSPRAYWATER = 0x23,
  SPECIALEFFECT_FOOTSTEPSPRAYWATERWALK = 0x24,
  SPECIALEFFECT_CHARACTERSHAPESHIFT = 0x25,
  SPECIALEFFECT_COMBATBLOODSPURTBLACKFRONT = 0x26,
  SPECIALEFFECT_COMBATBLOODSPURTBLACKFRONTLARGE = 0x27,
  SPECIALEFFECT_COMBATBLOODSPURTBLACKBACK = 0x28,
  SPECIALEFFECT_COMBATBLOODSPURTBLACKBACKLARGE = 0x29,
  SPECIALEFFECT_RES_EFFECT = 0x2A,
  NUM_UNITEFFECTSPECIALS = 0x2B,
  SPECIALEFFECT_NONE = 0xFFFFFFFF,
};

enum UNITEFFECTATTACHPPOINT
{
  UNITEFFECT_ATTACHBASE = 0x0,
  UNITEFFECT_ATTACHHEAD = 0x1,
  UNITEFFECT_ATTACHLEFTHAND = 0x2,
  UNITEFFECT_ATTACHRIGHTHAND = 0x3,
  UNITEFFECT_ATTACHNONE = 0x4,
  UNITEFFECT_ATTACHBREATH = 0x5,
  UNITEFFECT_ATTACHCHEST = 0x6,
  UNITEFFECT_ATTACHSPECIAL1 = 0x7,
  UNITEFFECT_ATTACHSPECIAL2 = 0x8,
  UNITEFFECT_ATTACHSPECIAL3 = 0x9,
  UNITEFFECT_ATTACHCHESTBLOODBACK = 0xA,
  UNITEFFECT_ATTACHCHESTBLOODFRONT = 0xB,
  NUM_UNITEFFECTATTACHPOINTS = 0xC,
  UNITEFFECT_INVALID = 0xFFFFFFFF,
};
```
