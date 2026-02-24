# Skin and LOD Files

## Summary

The .skin file format, introduced in World of Warcraft: Wrath of the Lich King, is a critical component for rendering M2 models with varying levels of detail (LODs). It separates the submesh and LOD data from the main M2 file, allowing the game to load simplified versions of models at a distance to improve performance. The format specifies vertex and index lookup tables, bone mappings, texture units, and render batches for each LOD, enabling a flexible and efficient rendering pipeline.

## Struct Definitions

```c
struct M2SkinProfile
{
  uint32_t                magic;                         // 'SKIN'
  M2Array<unsigned short> vertices;
  M2Array<unsigned short> indices;
  M2Array<ubyte4>         bones;
  M2Array<M2SkinSection>  submeshes;
  M2Array<M2Batch>        batches;
  uint32_t                boneCountMax;
  M2Array<M2ShadowBatch>  shadow_batches;
};

struct M2SkinSection
{
  uint16_t skinSectionId;
  uint16_t Level;
  uint16_t vertexStart;
  uint16_t vertexCount;
  uint16_t indexStart;
  uint16_t indexCount;
  uint16_t boneCount;
  uint16_t boneComboIndex;
  uint16_t boneInfluences;
  uint16_t centerBoneIndex;
  C3Vector centerPosition;
  C3Vector sortCenterPosition;
  float sortRadius;
};

struct M2Batch
{
  uint8_t flags;
  int8_t priorityPlane;
  uint16_t shader_id;
  uint16_t skinSectionIndex;
  uint16_t geosetIndex;
  uint16_t colorIndex;
  uint16_t materialIndex;
  uint16_t materialLayer;
  uint16_t textureCount;
  uint16_t textureComboIndex;
  uint16_t textureCoordComboIndex;
  uint16_t textureWeightComboIndex;
  uint16_t textureTransformComboIndex;
};

struct M2ShadowBatch
{
  uint8_t  flags;          // 0x1: Casters don't have shadows. Appears on some doodads.
  uint8_t  flags2;
  uint16_t ground_type;
  int16_t  bone_index;
  uint16_t submesh_id;
  uint16_t vertex_count;
  uint16_t index_count;
  uint32_t vertex_offset;
  uint32_t index_offset;
};
```

## Constants and Flags

### Magic Number
- `SKIN`: The first 4 bytes of a .skin file, identifying it as such.

### M2Batch Flags
- `0x1`: Invert texture mapping
- `0x2`: Transform
- `0x4`: Project texture
- `0x10`: Unlit
- `0x40`: Unfogged

### M2ShadowBatch Flags
- `0x1`: Casters don't have shadows. Appears on some doodads.

## Relationships

- **M2 Files:** .skin files are intrinsically linked to .m2 model files. The .skin file references vertex and bone data within the .m2 file, and the .m2 file relies on the .skin file for LOD information.
- **CreatureDisplayInfo.dbc and ItemDisplayInfo.dbc:** These database files use geoset data to customize the appearance of creatures and items, which directly corresponds to the submesh IDs defined in the .skin file.

## Related Tools

- **jM2converter:** A universal converter for WoW M2 formats.
- **LKBC_Converter:** A tool to convert M2 models from WotLK to The Burning Crusade format.

## Code Examples

### C++ Example for Applying Monster Geosets

```cpp
void ApplyMonsterGeosets(CM2Model *pModel, CreatureDisplayInfoRec *pDisplayInfo, CharacterComponent *pCharacterComponent)
{
    if (pModel && pDisplayInfo)
    {
        CreatureModelData *modelDataRec = ClientDB::CreatureModelDataDB::GetRow(pCharacterComponent->ModelId);
        
        if (!modelDataRec)
            __debugbreak();

        if (modelDataRec->CreatureGeosetDataID)
        {
            CM2Model::SetGeometryVisible(pModel, 1, 899, false);

            int displayInfoID = pDisplayInfo->ID;

            auto geosetDatas = ClientDB::CreatureDisplayInfoGeosetDataDB::GetRows([](auto cdigd) { return cdigd->CreatureDisplayInfoID == displayInfoID; });

            for (auto geosetData : geosetDatas)
            {
                int meshId1 = 100 * (geosetData->GeosetIndex + 1);
                CM2Model::SetGeometryVisible(pModel, meshId1, meshId1 + 99, false);
                int meshId2 = meshId1 + geosetData->GeosetValue;
                CM2Model::SetGeometryVisible(pModel, meshId2, meshId2, true);
            }
        }
    }
}
```

### JavaScript Example for WotLK Runtime Shader Selection

```javascript
function CM2Shared.sub837A40() 
{
  /* Some code is skipped */
  
  if ( !((_BYTE)field[4]->field_4 & 8) )
  {
    M2Vertex* override_vertices = SMemNew(sizeof (M2Vertex) * skinFile->indices.count);

    // 2. zero-initialize (but will be overridden with real vertices in 3.)
    // 3. Copy data from initial vertex of m2 and override boneIndexes
    
    for (int meshIndex = 0; meshIndex < skinFile->submeshes.count; ++meshIndex)
    {
      M2SkinSection* subMesh = skinFile->submeshes.data[meshIndex];

      for (int vertIndex = subMesh->StartVertex; vertIndex < (subMesh->StartVertex + subMesh->vertices.count); ++vertIndex)
      {
        override_vertices[vertIndex] = m_data->vertices.data[skinFile->indices.data[vertIndex]];

        for (int boneInd = 0; boneInd < subMesh->boneInfluences; ++boneInd)
        {
          override_vertices[vertIndex].bone_indices[boneInd] =
            m_data->bone_lookup_table.data[subMesh->StartBones + skinFile->properties.data[4*vertIndex + boneInd]];
        }
      }
    }
  
    // 4. Override bone lookup table and in m2 file
    for (int i = 0; i < m_data->nBoneLookupTable; ++i)
      m_data->bone_lookup_table.data[i] = i;
 
    // 5. Override indicies in skin file
    for (int j = 0; j < skinFile->indices.count; ++j)
      skinFile->indices.data[j] = j;
 
    // 6. Override vertex array from m2 with new data
    if ( skinFile->indices.count <= m_data->vertices.count )
    {
      memcpy(m_data->vertices.data, override_vertices, sizeof (M2Vertex) * skinFile->indices.count);
      SMemFree (override_vertices);
    }
    else
    {
      field_8 |= 8u;
      m_data->vertices.data = override_vertices;
    }
  
    m_data->vertices.count = skinFile->indices.count;
  }
  
  // 7. Override batch flags
  if ( !((_BYTE)field[4]->field_4 & 8) )
  {
    for ( int i = 0; i < skinFile->batches.count; i++)
    {
      if ( skinFile->batches.data[i].op_count > 1u )
        skinFile->batches.data[i - skinFile->batches.data[i].layer].flags |= 0x40u;
    }
  
    for ( int i = 0; i < skinFile->batches.count; i++)
    {
      if ( skinFile->batches.data[i].layer )
      {
        if ( skinFile->batches.data[i - skinFile->batches.data[i].layer].flags & 0x40 )
          skinFile->batches.data[i].flags |= 0x40u;
      }
    }
  }
}
```

## Sources

- https://wowdev.wiki/M2/.skin
