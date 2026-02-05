# WoW 3.3.5a Sound and Music System

## Summary

This document details the technical aspects of the sound and music system in World of Warcraft patch 3.3.5a. It covers the structure of the `SoundEntries.dbc` and `ZoneMusic.dbc` files, which are central to how the game handles audio. The document also outlines the various sound types, flags, and their relationships, providing a comprehensive overview for developers looking to work with WoW's audio on a technical level.

## Struct Definitions

### SoundEntries.dbc

```c
struct SoundEntriesRec {
  uint32_t m_ID;
  uint32_t m_soundType;
  stringref m_name;
  stringref m_File[10];
  uint32_t m_Freq[10];
  stringref m_DirectoryBase;
  float m_volumeFloat;
  uint32_t m_flags;
  float m_minDistance;
  float m_maxDistance;
  float m_distanceCutoff;
  uint32_t m_soundEntriesAdvancedID;
};
```

### ZoneMusic.dbc

```c
enum AMBIENCE {
  AMB_DAY = 0,
  AMB_NIGHT = 1,
  NUM_AMBIENCES = 2,
};

struct ZoneMusicRec {
  uint32_t m_ID;
  stringref m_SetName;
  uint32_t m_SilenceIntervalMin[NUM_AMBIENCES];
  uint32_t m_SilenceIntervalMax[NUM_AMBIENCES];
  foreign_key<uint32_t, &SoundEntriesRec::m_ID> m_Sounds[NUM_AMBIENCES];
};
```

## Constants and Flags

### Sound Types

| Value | Meaning |
| --- | --- |
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
| 18 | Test/Temporary |
| 19 | Foley Sounds (NOT EDITABLE) |
| 20 | Footsteps(Splashes) |
| 21 | CharacterSplashSounds |
| 22 | WaterVolume Sounds |
| 23 | Tradeskill Sounds |
| 24 | Terrain Emitter Sounds |
| 25 | Game Object Sounds |
| 26 | SpellFizzles |
| 27 | CreatureLoops |
| 28 | Zone Music Files |
| 29 | Character Macro Lines |
| 30 | Cinematic Music |
| 31 | Cinematic Voice |
| 50 | Zone Ambience |
| 52 | Sound Emitters |
| 53 | Vehicle States |

### SoundInterfaceFlags

```c
enum SoundInterfaceFlags
{
   UNUSED        = 0x0001,
   NO_DUPLICATES = 0x0020,
   LOOPING       = 0x0200,
   VARY_PITCH    = 0x0400,
   VARY_VOLUME   = 0x0800,
}
```

## Relationships

*   **ZoneMusic.dbc to SoundEntries.dbc**: The `ZoneMusic.dbc` file links to `SoundEntries.dbc` to determine which music to play in a specific zone. The `m_Sounds` field in `ZoneMusicRec` is a foreign key to the `m_ID` field in `SoundEntriesRec`.
*   **SoundEntries.dbc to Audio Files**: The `SoundEntries.dbc` file points to the actual audio files (.wav, .mp3) to be played. The `m_DirectoryBase` and `m_File` fields are combined to create the full path to the sound file.

## File Formats

The primary audio file formats used in WoW 3.3.5a are:
*   `.wav`
*   `.mp3`
*   `.ogg` (less common in this version)

## Tools

*   **DBC Editors**: Various DBC editors can be used to view and edit the contents of `SoundEntries.dbc` and `ZoneMusic.dbc`.
*   **MPQ Editors**: MPQ editors are necessary to extract the DBC files and other game assets from the game's data archives.

## Code Examples

Playing a sound in-game via a script command:

```lua
/script PlaySoundFile("Sound\\Creature\\AmbassadorHellmaw\\Auch_Helmaw_Slay02.wav")
```

## Sources

*   [wowdev.wiki/DB/SoundEntries](https://wowdev.wiki/DB/SoundEntries)
*   [wowdev.wiki/DB/ZoneMusic](https://wowdev.wiki/DB/ZoneMusic)
*   [github.com/fondlez/wow-sounds](https://github.com/fondlez/wow-sounds)
