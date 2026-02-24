# BLP2 Texture Format Research

## Sources

* https://wowdev.wiki/BLP
* https://github.com/Pinta365/blp
* http://justsolve.archiveteam.org/wiki/BLP

## Summary

BLP2 is a texture format used by Blizzard Entertainment in games like World of Warcraft. It supports various compression methods, including DXT1, DXT3, and DXT5, as well as uncompressed and palettized formats. The file structure consists of a header, mipmap offsets and sizes, and the texture data for each mipmap level.

## Struct Definitions

```c
struct BLPHeader {
  uint32_t magic; // 'BLP2'
  uint32_t formatVersion; // always 1
  uint8_t colorEncoding; // 1: uncompressed, 2: DXTC, 3: plain A8R8G8B8
  uint8_t alphaSize; // 0, 1, or 8
  uint8_t preferredFormat;
  uint8_t hasMips; // 0: no mipmaps, 1: mipmaps
  uint32_t width;
  uint32_t height;
  uint32_t mipOffsets[16];
  uint32_t mipSizes[16];
  union {
    struct BlpPalPixel {
      uint8_t b, g, r, pad;
    } palette[256];
    struct {
      uint32_t headerSize;
      char headerData[1020];
    } jpeg;
  } extended;
};
```

## Constants and Flags

**colorEncoding**
* 1: Uncompressed
* 2: DXTC (DXT1, DXT3, DXT5)
* 3: Plain A8R8G8B8 (Cataclysm)

**alphaSize**
* 0: No alpha
* 1: 1-bit alpha
* 8: 8-bit alpha

## Relationships

* BLP files are used for textures in various WoW file formats, such as M2 (models) and WMO (World Map Objects).
* The DXT compression formats (DXT1, DXT3, DXT5) are standard S3 Texture Compression formats.

## Tools

* **BLPConverter**: A tool for converting BLP files to other formats.
* **Pillow**: A Python imaging library with support for reading BLP files.
* **wow_blp**: A Rust crate for parsing BLP files.

## Code Examples

```javascript
// Calculating valid mipmap size
if ((textureFormat == "DXT5") || (textureFormat == "DXT3")) {
    validSize = Math.floor((width + 3) / 4) * Math.floor((height + 3) / 4) * 16;
}
if ((textureFormat == "RGB_DXT1") || (textureFormat == "RGBA_DXT1")) {
    validSize = Math.floor((width + 3) / 4) * Math.floor((height + 3) / 4) * 8;
}
```


## Additional Information from GitHub

The GitHub repository for the `blp` TypeScript library provides further insights into the BLP2 format and its implementation.

### DXT Compression

The library supports DXT1, DXT3, and DXT5 compression formats. The specific DXT format used is determined by the `preferredFormat` and `alphaSize` fields in the BLP header.

### Mipmaps

The library can extract all mipmap levels from a BLP file. The `mipOffsets` and `mipSizes` arrays in the header provide the location and size of each mipmap level's data.

### Uncompressed Formats

In addition to compressed formats, the library also supports uncompressed (RAW3) and palettized (RAW1) images.
