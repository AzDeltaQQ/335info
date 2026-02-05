# DB2 and WDB Cache Formats

## DB2 File Format

DB2 files are the successor to the DBC (DataBaseClient) file format, introduced in later versions of World of Warcraft to store client-side database information. While the research is for 3.3.5a, the wowdev wiki page for DB2 mentions that this format was introduced in Cataclysm (4.x). For WotLK (3.x), DBC files are more common.

### DB2 Header Structure (WDB2 version - Cataclysm and later)

```c
struct db2_header
{
  uint32_t magic;                                               // 'WDB2'
  uint32_t record_count;
  uint32_t field_count;                                         // array fields count as the size of array for WDB2
  uint32_t record_size;
  uint32_t string_table_size;                                   // string block almost always contains at least one zero-byte
  uint32_t table_hash;
  uint32_t build;
  uint32_t timestamp_last_written;                              // set to time(0); when writing in WowClientDB2_Base::Save()
  uint32_t min_id;
  uint32_t max_id;
  uint32_t locale;                                              // as seen in TextWowEnum
  uint32_t copy_table_size;                                     // always zero in WDB2 (?) - see WDB3 for information on how to parse this
};
```

## WDB File Format

WDB files are used by the client to cache data received from the server, reducing network traffic. These files are located in the `WDB` folder of the WoW client.

### WDB Header Structure (for version 3.3.5a)

For WoW version 3.3.5a (build 12340), which is >= 3.0.8, the WDB header is 24 bytes long.

```c
struct wdb_header_335a
{
  char      signature[4];      // Identifier for the WDB file type (e.g., 'WMOB', 'WGOB')
  uint32_t  client_version;    // Client build version
  char      client_locale[4];  // Client locale (e.g., 'enUS')
  uint32_t  record_size;       // Size of each record in the file
  uint32_t  record_version;    // A manually updated versioning field
  uint32_t  cache_version;     // A packet based versioning field set via SMSG_CLIENTCACHE_VERSION
};
```

### WDB File Signatures

| File                  | Signature |
| --------------------- | --------- |
| CreatureCache.wdb     | WMOB      |
| GameObjectCache.wdb   | WGOB      |
| ItemCache.wdb         | WIDB      |
| ItemNameCache.wdb     | WNDB      |
| ItemTextCache.wdb     | WITX      |
| NPCCache.wdb          | WNPC      |
| PageTextCache.wdb     | WPTX      |
| QuestCache.wdb        | WQST      |

