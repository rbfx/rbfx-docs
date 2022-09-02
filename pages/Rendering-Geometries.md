# Drawable Geometry Components

"Geometries" is a general term for all components inherited from `Drawable` with `DRAWABLE_GEOMETRY` flag.
"Geometries" also include components that don't inherit `Drawable` directly and instead use temporary `Drawable`-s internally (like `Terrain` that internally spawns `TerrainPatch`-es)

These components define actual geometry to be rendered.

## Common Properties

For **Light Mask**, **Shadow Mask**, and **Zone Mask** descriptions, see [Zones](Rendering-Zones).

|Property|Description|
|-|-|
|**Is Enabled**|If false, is not rendered in any way.|
|**Is Occluder**|Whether to render this geometry on CPU for additional culling.|
|**Can Be Occluded**|Whether this geometry can be occluded by occluders. Disable this flag for geometries with unusual depth test function (`always`, `greater` or `greaterequal`).|
|**Cast Shadows**|Whether this geometry should casts shadows.|
|**Draw Distance**|Geometry is rendered if distance to it is smaller than **Draw Distance**. If 0, geometry is always rendered.|
|**Shadow Distance**|Geometry casts shadows if the distance to it is smaller than **Shadow Distance**. If 0, geometry always casts shadows.|
|**LOD Bias**|Multiplier that affects level of detail used to render geometry. Increase bias to reduce quality.|
|**View Mask**|Geometry is ignored if its **View Mask** doesn't match **View Mask** of the `Camera`.|
|**Global&nbsp;Illumination**|Method used to apply global illumination to the geometry. Note that ambient and background lighting from zones is applied regardless of this setting.|
|> None|No additional lighting is applied.|
|> Use LightMap|Geometry receives lighting from baked light maps.|
|>&nbsp;Blend&nbsp;Light&nbsp;Probes|Geometry receives lighting from light probes cached in `GlobalIllumination`.|
|**Reflection Mode**|Method used to apply cubemap reflection to the geometry. See [Reflection Probes](Rendering-Reflection-Probes) for more details.|
|> Zone|Geometry receives reflection from best suited `Zone`. `ReflectionProbe`s are ignored.|
|> Nearest Probe|Geometry receives reflection from best suited `ReflectionProbe`, or `Zone` if there is no probes.|
|> Blend Probes|Geometry receives reflection from two best suited `ReflectionProbe`s, or `Zone` if there is no probes.|
|> Blend Probes and Zone|Geometry receives reflection from two best suited `ReflectionProbe`s or `Zone`, whichever is more relevant.|
|**Occlusion&nbsp;LOD&nbsp;Level**|LOD used for rendering occluder on CPU.|
|**Bake Lightmap**|Whether to consider this geometry during light baking.|
|**Scale in Lightmap**|Factor used to adjust texel density of lightmaps. If 0, geometry will not be present in lightmaps and will not contribute to indirect lighting. However, it will contribute to direct lighting (i.e. will cast shadows during baking)|

## Drawable Interface

All geometries are expected to fill the array of `SourceBatch`-es in `Drawable` with batches to be rendered.

All geometries are also expected to implement specific virtual interface.

|Function|Expected behavior|
|-|-|
|**Update**|Update everything that may affect world-space bounding box of geometry. E.g. apply skeletal animations.|
|**UpdateBatches**|Evaluate geometry batches that will be rendered and determine distances to these batches.|
|**UpdateGeometry**|All other updates here, e.g. anything related to updating GPU buffers.|
|**GetUpdateGeometryType**|Whether the **UpdateGeometry** should be called and how.|
|> None|Don't call.|
|> Main Thread|Call in main thread. GPU buffers should be updated from main thread only.|
|> Worker Thread|Call in any thread.|
|**GetNumOccluderTriangles**|Return number of triangles that will be rasterized on **DrawOcclusion**. Ignore if not supported.|
|**DrawOcclusion**|Rasterize geometry to occlusion buffer on CPU. Ignore if not supported.|
|**OnWorldBoundingBoxUpdate**|Recalculate world-space bounding box.|


## Standard Drawables

### Static Model

`StaticModel` renders single `Model` with applied `Material`(s) "as is". May be baked into lightmap.

### Static Model Group

`StaticModelGroup` renders multiple identical `StaticModel`-s processed and culled together as single entity.
Limited in functionality comparing to an array of `StaticModel`-s, use with caution.

### Skybox

`Skybox` is a special type of `StaticModel` used to render skybox.

### Animated Model

`AnimatedModel` renders single `Model` with optionally applied skeletal and morph animations. Avoid using multiple `AnimatedModel`-s on the same node.

### Billboard Set

`BillboardSet` renders arbitrary amount of quads covered with single `Material`. Quads may be automatically aligned to the screen.

### ParticleEmitter

`ParticleEmitter` is a special type of `BillboardSet` which manages quads automatically according to particle emitter configuration.

### Custom Geometry

`CustomGeometry` is used to easily render geometry generated from code. Suboptimal and limited in functionality comparing to `StaticModel`.

### Decal Set

`DecalSet` renders decals attaches to `StaticModel` or `AnimatedModel`.

### Terrain

`Terrain` renders geometry based on height map texture.
May be seamlessly stitched with nearby `Terrain`-s. May be baked into lightmap.

### Ribbon Trail

`RibbonTrail` renders geometry in the shape of tail following owner `Node`.

### Text 3D

`Text3D` renders text in 3D space. Consider using SDF fonts.

### Renderer 2D

`Renderer2D` combines all visible `Drawables` with type `DRAWABLE_GEOMETRY2D` and renders them together in 3D space.
