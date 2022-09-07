Material Resource {#rendering-materials}
========================================

`Material` defines properties of geometry surface and how exactly it is rendered by the pipeline.

### Techniques

Each `Material` references at least one `Technique` that defines exact sequence and parameters of scene passes in rendering pipeline.

Used `Technique` may be selected based on the distance to the `Geometry` using this `Material`, and/or based on selected quality level.

Techniques should be ordered (1) by distance from furthest to nearest and (2) from highest to lowest quality:

- Most distant & highest quality
- ...
- Most distant & lowest quality
- Second most distant & highest quality
- ...

First technique with distance greater or equal to current distance and with quality level less or equal to current quality level is selected and used for rendering.

### Textures

Each `Material` may reference zero or more `Texture` resources that will be available later in shaders for sampling.

Some textures can be easily reused for custom purposes despite semantics, some textures should be reused with much caution.

These textures are available on all platforms:

|Name|Description|Reusable|
|-|-|-|
|**diffuse**|Diffuse or albedo map. For refractive geometries, contains background color texture.|Yes|
|**normal**|Normal map.|Yes|
|**specular**|Specular map, or PBR material and occlusion map.|Yes|
|**emissive**|Emission map, or light map, or occlusion map (deprecated).|Yes|
|**environment**|2D or cube texture used for reflection. This texture is usually set automatically during rendering.|
|**lightramp**|Internal. Used to control light source distance attenuation.||
|**lightshape**|Internal. Texture projected by light source.||
|**shadowmap**|Internal. Shadow map for light source.||

These textures are available only on Desktop platforms:

|Name|Description|Reusable|
|-|-|-|
|**depth**|Readable depth buffer from previous pass(es).||
|**...**|There's more, but other textures don't have any special meaning.|Yes|

### Parameters

Each `Material` may declare zero or more shader parameters of the following types:

- **float**
- **Vector2**
- **Vector3**
- **Vector4**

Shader parameters are passed to shader into **Material**, mostly without any additional transformations.

See `Material` section in `_Uniforms.glsl` for exact list of default parameters.

Special notes:

* **FadeOffsetScale** defines depth offset and range in units for soft edges e.g. for particles and water. Shader receives values normalized to camera z range. Depth range is inverted.

### Shader Defines

`Material` may define shader macros that will be defined at shader compilation time.

Be careful with spawning multiple shader permutations.
Each unique combination of shader macros will lead to more shader switches during rendering.

Most of shader defines are passed as is to shaders without additional behaviors.

**Note:** `ALPHAMASK` macro is used for additional optimizations during shadow rendering.

See "Material defines" section in `_Config.glsl` for detailed list of available material defines.

### Configuration

`Material` has several built-in parameters:

|Name|Description|Default Value|
|-|-|-|
|**cull**|Defines whether and how geometry is culled|ccw|
|> cw|Front faces (clockwise) are culled.||
|> ccw|Back faces (counterclockwise) are culled.||
|> none|No faces are culled.||
|**shadowcull**|Same as cull, but for shadow rendering. Use opposite value of **cull** to reduce shadow acne and *increase* peter panning.|ccw|
|**fill**|How to fill geometry. Always **solid** on mobile platforms.|solid|
|> solid|Draw triangles as solid triangles, lines as lines.||
|>&nbsp;wireframe|Draw triangles as wireframe, lines as lines.||
|> point|Draw triangles and lines as points. Why tho.||
|**depthbias**|Constant and scaled depth biases. May be used to reduce z-fighting for decals.||
|**alphatocoverage**|Whether to treat alpha output of pixel shader as MSAA coverage.||
|**lineantialias**|Whether to enable alpha-based line antialiasing. I have no clue. You probably don't need it.||
|**renderorder**|Materials with lower order are always rendered before materials with higher order.||
|**occlusion**|Whether to use geometry with this material for occlusion culling. Since occluders are configured per-object, you probably don't need it. Just uncheck "occluder" flag for occluder `Drawable`.|
