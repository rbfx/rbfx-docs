Model Resource {#rendering-models}
========================================

`Model` contains vertex and index buffers used to render static geometries.
`Model` also contain `Skeleton` for skeletal animation and some extra metadata.

**Note:** `Model` contains prepared data ready for rendering.
Use `ModelView` to access model data in easy-to-use format.

`Model` is stored in the file system as `*.mdl` file which contains main data and optional `*.xml` auxiliary file which contains arbitrary metadata.

## Data Layout

### Vertex and Index Buffers

`Model` contains arbitrary amount of `VertexBuffer`-s and `IndexBuffer`-s of arbitrary format.

`Model` contains `BoundingBox` in model space which will be used for spatial queries.

### Vertex Morphs

`Model` may contain arbitrary amount of morphs.

Morph is an update to a subset of vertices in one or more `VertexBuffer`-s.
Morph may additively modify position, normal and/or tangent of vertices.
Morphs are applied to geometry in `AnimatedModel`.

### Skeleton

`Model` contains exactly one `Skeleton`.
For static models `Skeleton` is empty and is usually ignored.

`Skeleton` is an array of `Bone`-s.

Every `Bone` has its name, initial transform and parent bone.

Also, `Bone` should contain either bounding box or bounding sphere in order to contribute to the bounding box of `AnimatedModel`.

### Geometries

Geometries are what actually defines how `Model` will be rendered.

`Model` is split into one or more "submodels", where each "submodel":

- has its own independent set of LODs;
- has geometry center in model space used to calculate LOD distance;
- has optional mapping of bone indices (from bone index in vertex to bone index in skeleton);
- may be assigned with unique `Material` during rendering.

`Geometry` is the specific LOD of specific "submodel".
Example layout of geometries in the `Model`:

| |LOD #0|LOD #1|LOD #2|...|
|-|-|-|-|-|
|**Batch #0**|Geometry #0|Geometry #1||
|**Batch #1**|null|Geometry #2|Geometry #3|...|
|**Batch #2**|Geometry #4|Geometry #5|Geometry #5|
|**...**|...|...|...|...|

Every `Geometry` *references* one or more `VertexBuffer` of the `Model` and at most one `IndexBuffer`. `Geometry` never stores actual vertices or indices.

`Geometry` also defines the range of vertices/indices to be rendered and primitive type used.

### Metadata

Usually metadata is up to user, but there's a couple of values with special meaning:

|Name|Type|Description|
|-|-|-|
|**LightmapSize**|**IntVector2**|Size of the region in lightmap texture required to fit all object lightmaps with specified **LightmapDensity**.|
|**LightmapDensity**|**float**|Number of lightmap texels per unit, assuming that the object takes **LightmapSize** texels of lightmap texture.|
|**LightmapSharedUV**|**bool**|Whether the object uses the same lightmap UVs for all its LODs.|
