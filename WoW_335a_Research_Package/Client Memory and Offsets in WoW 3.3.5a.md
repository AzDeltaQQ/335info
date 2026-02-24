# Client Memory and Offsets in WoW 3.3.5a

This document details the memory structures, offsets, and related information for the World of Warcraft client version 3.3.5a (build 12340). The information has been gathered from various sources, including community forums and open-source projects.

### Summary

The World of Warcraft client maintains a wealth of information in its memory space, accessible through various offsets from the base address of the game's process. Key data structures include the Object Manager, which tracks all in-game objects such as players, units, and game objects. By reading the client's memory, it is possible to extract detailed information about these objects, including their position, health, and other attributes. This information is invaluable for developing third-party applications that interact with the game world, such as bots, rotation helpers, and informational overlays.

### Struct Definitions

Below are the C-style struct definitions for various game objects, derived from the collected data.

```c
// Object Fields
struct ObjectFields {
    uint64_t guid;              // 0x00
    uint32_t type;              // 0x08
    uint32_t entry;             // 0x0C
    float    scale_x;           // 0x10
    uint32_t padding;           // 0x14
};

// Item Fields
struct ItemFields {
    uint64_t owner;             // 0x18
    uint64_t contained;         // 0x20
    uint64_t creator;           // 0x28
    uint64_t giftcreator;       // 0x30
    uint32_t stack_count;       // 0x38
    uint32_t duration;          // 0x3C
    uint32_t spell_charges[5];  // 0x40
    uint32_t flags;             // 0x54
    uint32_t enchantments[12][2]; // 0x58
    uint32_t property_seed;     // 0xE8
    uint32_t random_properties_id; // 0xEC
    uint32_t durability;        // 0xF0
    uint32_t max_durability;    // 0xF4
    uint32_t create_played_time; // 0xF8
    uint32_t pad;               // 0xFC
};

// Unit Fields
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
    // ... and so on
};
```

### Constants and Flags

**Lua Engine Offsets:**

*   `LUA_GETTOP = 0x0084DBD0`
*   `LUA_SETTOP = 0x0084DBF0`
*   `LUA_PUSHSTRING = 0x0084E350`
*   `LUA_PUSHINTEGER = 0x0084E2D0`
*   `LUA_PCALL = 0x0084EC50`

**Object Manager Constants:**

*   `CURMGR_OFFSET = 0x00B41414`
*   `CURMGR_PTR_OFFSET = 0x24`
*   `NEXT_OBJECT_OFFSET = 0x3C`
*   `OBJECT_TYPE_OFFSET = 0x14`

### Relationships

*   The **Object Manager** is the central hub for all in-game objects. It maintains a linked list of objects, which can be traversed to access individual players, units, and game objects.
*   Each object in the Object Manager has a **GUID** (Globally Unique Identifier) that uniquely identifies it.
*   Player and unit objects have a **UnitFields** structure that contains detailed information about their stats, such as health, mana, and level.
*   The client's **Lua engine** can be manipulated to execute scripts and interact with the game world programmatically.

### Tools

*   **Cheat Engine:** A memory scanner and debugger that can be used to find memory addresses and pointers.
*   **ReClass.NET:** A tool for reverse engineering class structures from memory.
*   **Various debuggers:** Such as OllyDbg, x64dbg, and WinDbg, can be used to analyze the game client's code and memory.

### Code Examples

**Python example for iterating through the Object Manager:**

```python
# Get Player Object: Read pointer at (WoW.exe + CURMGR_OFFSET), then read pointer at (result + CURMGR_PTR_OFFSET)
# Iterate Object Manager: Start with first object, then follow NEXT_OBJECT_OFFSET until it's 0 or loops.
# Get Unit Health: (Unit_Object_Base + UNIT_HEALTH)
```

**C# example for reading player data:**

```csharp
public void Ping()
{
    // ... (code to read player data)
}
```

### Sources

*   [https://www.ownedcore.com/forums/world-of-warcraft/world-of-warcraft-bots-programs/wow-memory-editing/298984-3-3-5a-12340-offsets-3.html](https://www.ownedcore.com/forums/world-of-warcraft/world-of-warcraft-bots-programs/wow-memory-editing/298984-3-3-5a-12340-offsets-3.html)
*   [https://github.com/AzDeltaQQ/WotLKRotations](https://github.com/AzDeltaQQ/WotLKRotations)
*   [https://wowdev.wiki/ObjectManager](https://wowdev.wiki/ObjectManager)
*   [https://www.unknowncheats.me/forum/world-of-warcraft/370439-3-3-5a-adress-offset-dump-question-warmane.html](https://www.unknowncheats.me/forum/world-of-warcraft/370439-3-3-5a-adress-offset-dump-question-warmane.html)
*   [https://github.com/johnmoore/WoW-Object-Manager](https://github.com/johnmoore/WoW-Object-Manager)
