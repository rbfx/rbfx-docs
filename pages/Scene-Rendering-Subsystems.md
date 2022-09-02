# Scene Subsystems

Scene subsystems are root level `Scene` components, i.e. assigned to the `Scene` itself.
Subsystems should have at most one instance per `Scene`.

Scene subsystems are used to enable some of global features affecting scene as whole.

## Render Pipeline

`RenderPipeline` manages scene rendering and keeps current rendering settings.

`RenderPipeline` is required if `Scene` is rendered with new renderer.
For compatibility reasons it is created automatically unless legacy renderer is used.

Any class derived from `RenderPipeline` can be used instead of `RenderPipeline` itself.

See [Scene rendering pipeline](Scene-Rendering-Pipeline.md) for details.

## Octree

`Octree` allows accelerated lookup through enabled `Drawable` components in the scene.
`Octree` also manages updates of object transformations.

`Octree` is required for each rendered `Scene` and it cannot be added afterwards.

|Property|Description|
|-|-|
|**Bounding&nbsp;Box**|Bounding box of the area of optimized lookups. Lookups outside of this area are performed via linear search.|
|**Number&nbsp;of&nbsp;Levels**|Number of `Octree` levels defines size of the smallest possible cube chunk used for grouping.|

## Debug Renderer

`DebugRenderer` enables rendering of simple debug primitives.
It's used by the Editor and debug in-game HUD.

|Property|Description|
|-|-|
|**Line&nbsp;Antialias**|Whether to enable line antialiasing even if global antialiasing is disabled.|

## Global Illumination

`GlobalIllumination` manages baked data used for Global Illumination:

* Substantial parameters of light baking.

* Cached lookup structure for all enabled `LightProbeGroup`-s.

`GlobalIllumination` is required if light probes are used.

`CompileLightProbes()` shall be executed whenever light probes are added, removed or moved.
It's also shall be called whenever light probe data is changed.
TODO: Try to elevate this requirement?

|Property|Description|
|-|-|
|**Emission&nbsp;Brightness**|Emission brightness multiplied for GI baking.|

## Light Baker

`LightBaker` provides light baking functionality and controls light baking quality.

`LightBaker` is required if the light baking itself is used.
This component is usually unneeded in runtime.

`Bake()`/`BakeAsync()` can be used to re-bake all the lighting in the `Scene`.

### Basic settings

|Property|Description|
|-|-|
|**Output&nbsp;Directory**|Output directory to store baking results. If empty, results are stored nearby `Scene` file in separate directory.|
|**Lightmap Size**|Size of lightmap texture.|
|**Texel Density**|Number of lightmap texels allocated per scene unit.|

### Quality settings

|Property|Description|
|-|-|
|**Quality**|Quality presets.|
|> Custom|Custom settings.|
|> Low|Fast baking, low quality.|
|> Medium|Slower baking, medium quality.|
|> High|Slow baking, high quality.|
|**Direct Samples (Lightmap)**|Number of rays traced per lightmap texels per light source.|
|**Direct Samples (Light&nbsp;Probes)**|Number of rays traced per light probe per light source.|
|**Indirect Bounces**|Number of indirect light bounces.|
|**Indirect Samples (Texture)**|Number of rays traced per lightmap texels for indirect light baking.|
|**Indirect&nbsp;Samples (Light&nbsp;Probes)**|Number of rays traced per light probe for indirect light baking.|
|**Filter Radius (Direct)**|Gauss blur radius for direct light in lightmap.|
|**Filter Radius (Indirect)**|Gauss blur radius for indirect light in lightmap.|

### Chunk settings

Scene is split into multiple chunks baked separately to limit complexity and hardware requirements for large scenes.

|Property|Description|
|-|-|
|**Chunk&nbsp;Size**|Size of chunk in units.|
|**Chunk Indirect Padding**|Size of extra space with nearby geometry used for indirect light baking.|
|**Chunk Shadow Distance**|Max distance of cast shadows from light sources.|

### Miscellaneous settings

|Property|Description|
|-|-|
|**Stitch&nbsp;Iterations**|Number of iterations in lightmap seam stitching. Higher is slower, but lightmap seams are less visible.|
