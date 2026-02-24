# WDT (World Definition Table) Format for WoW 3.3.5a

This document details the structure of the WDT file format as used in World of Warcraft patch 3.3.5a (build 12340).

## Overview

WDT files are the top-level map files. They define which map tiles (.ADT files) are present in a world and can also reference a global World Map Object (.WMO) for maps that do not have terrain.

## File Structure

WDT files use a chunk-based format. Each chunk has a 4-byte identifier and a 4-byte size field, followed by the chunk data.

### MPHD Chunk

The MPHD chunk is the header of the WDT file. It contains flags that affect the rendering of the map.

**Structure (3.3.5a):**

```c
struct MPHD {
  uint32_t flags;
  uint32_t something; // Used to initialize MAIN chunk data in some cases
  uint32_t unused[6];
};
```

**Flags (mphd_flags enum):**

*   `wdt_uses_global_map_obj (0x1)`: If set, the map uses a global WMO defined in the MWMO and MODF chunks. This is for maps without ADT-based terrain.
*   `adt_has_mccv (0x2)`: ADT files have vertex coloring.
*   `adt_has_big_alpha (0x4)`: Use _env terrain shaders.
*   `adt_has_doodadrefs_sorted_by_size_cat (0x8)`: Doodad references in ADTs are sorted by size category.
*   `FLAG_LIGHTINGVERTICES (0x10)`: ADT files have lighting information in MCLV chunks.
*   `adt_has_upside_down_ground (0x20)`: Flips the ground display upside down to create a ceiling.

### MAIN Chunk

The MAIN chunk is a table of 4096 (64x64) entries that define the map tiles.

**Structure (SMAreaInfo):**

```c
struct SMAreaInfo {
  uint32_t Flag_HasADT : 1;
  uint32_t Flag_Loaded : 1; // Set at runtime
  uint32_t asyncId; // Set at runtime
};
```

If `Flag_HasADT` is set, the corresponding ADT file exists for that map tile.

### MWMO and MODF Chunks

These chunks are only present if the `wdt_uses_global_map_obj` flag is set in the MPHD chunk.

**MWMO Chunk:**

A zero-terminated string containing the filename of the global WMO.

**MODF Chunk:**

Contains placement information for the global WMO.

**Structure (SMMapObjDef):**

```c
struct SMMapObjDef {
  uint32_t nameId; // Not used, filename from MWMO is used
  uint32_t uniqueId; // Dynamically generated
  float position[3];
  float orientation[3];
  float upperExtents[3];
  float lowerExtents[3];
  uint16_t flags;
  uint16_t doodadSet;
  uint16_t nameSet;
  uint16_t pad;
};
```

## Relationships to other formats

*   **ADT (Area Definition Table):** WDT files specify which ADT files are present in a map.
*   **WMO (World Map Object):** WDT files can reference a global WMO for maps without terrain.
*   **WDL (World Definition Low-res):** WDL files contain low-resolution heightmap data for the entire map.

## Tools

*   **WDT Editor:** A tool for editing WDT files.
*   **BLPConverter:** For converting BLP files.

## Code Examples

```c
// Pseudocode for parsing a WDT file

function parseWDT(file) {
  const mphd = readMPHD(file);
  const main = readMAIN(file);

  if (mphd.flags & 0x1) {
    const mwmo = readMWMO(file);
    const modf = readMODF(file);
    // This is a WMO-based map
  } else {
    // This is an ADT-based map
    for (let y = 0; y < 64; y++) {
      for (let x = 0; x < 64; x++) {
        if (main.areaInfo[y][x].Flag_HasADT) {
          // Load ADT file for this tile
        }
      }
    }
  }
}
```
