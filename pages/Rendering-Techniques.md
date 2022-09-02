# Technique Resource

`Technique` defines exact sequence of steps used to render geometry to scene.

`Technique` consists of one or multiple passes which are used by the engine in different circumstances.
Each pass may use unique configuration of shaders and render device settings.

`Technique` may also have global shader name and defines which will be used by default within this `Technique`.
There's also **desktop** flag which can be used to disable whole `Technique` on mobile platforms.
Make sure that `Material` has fallback `Technique`(s) to use in this case.

## Pass Properties

|Name|Description|Default Value
|-|-|-|
|**name**|Name of the pass. Used to access pass from code and defines semantics for standard passes.||
|**[v,p]s**|Shaders used for rendering.||
|**[v,p]sdefines**|Shader defines used to compile shaders.|(empty)|
|**[v,p]sexcludes**|Shader defines excluded for this pass from the list of defines. Useful to suppress `Material` or `Technique` defines for specific passes.|(empty)|
|**blend**|Color blend mode used for rendering. See table below.|replace|
|**cull**|Overrides **cull** from `Material` description.|(inherited)|
|**depthtest**|Depth test used for rendering. See table below.|lessequal|
|**depthwrite**|Whether to write surface depth to the depth buffer.|true|
|**alphatocoverage**|If true, overrides **alphatocoverage** from `Material`.|false|

### Blend modes

* `src` - color of the surface rendered with this pass.
* `src_alpha` - alpha value returned from the shader of the surface.
* `dest` - color in the backbuffer.
* `dest_alpha` - alpha value in the backbuffer.
* `output` - final color stored in the backbuffer.

|Name|Formula|
|-|-|
|replace|`output = src`|
|add|`output = dest + src`|
|multiply|`output = dest * src`|
|alpha|`output = dest * (1 - src_alpha) + src * src_alpha`|
|addalpha|`output = dest + src * src_alpha`|
|premulalpha|`output = dest * (1 - src_alpha) + src`|
|invdestalpha|`output = dest * dest_alpha + src * (1 - dest_alpha)`|
|subtract|`output = dest - src`|
|subtractalpha|`output = dest - src * src_alpha`|

### Depth test functions

* `src_depth` - depth of the surface.
* `dest_depth` - depth stored in depth buffer.

|Name|Condition|
|-|-|
|always|`true`|
|equal|`src_depth == dest_depth`|
|less|`src_depth < dest_depth`|
|lessequal|`src_depth <= dest_depth`|
|greater|`src_depth > dest_depth`|
|greaterequal|`src_depth => dest_depth`|

## Standard Passes

Standard passes are listed in sections below.
`Technique` for default render pipeline usually needs *either* Opaque or Transparent passes, and some or all of utility passes.

### Opaque Rendering Passes

For opaque geometries, at least **base** and **light** passes should be present.

|Name|Description|
|-|-|
|**base**|Used to render opaque geometry for the first time. `URHO3D_AMBIENT_PASS` shader define is present during this pass.|
|**light**|Used to apply lighting to already rendered opaque geometry. `URHO3D_LIGHT_PASS` shader define is present during this pass.|
|**litbase**|Used to render opaque geometry for the first time and apply lighting from one light source. Both `URHO3D_AMBIENT_PASS` and `URHO3D_LIGHT_PASS` passes are present during this pass.|
|**deferred**|Used to render opaque geometry if Deferred lighting is enabled. If this pass is missing, Forward lighting will be used as fallback. `URHO3D_GBUFFER_PASS` shader define is present during this pass.|

### Transparent Rendering Passes

For transparent geometries, both **alpha** and **litalpha** passes should be present.

|Name|Description|
|-|-|
|**alpha**|Used to render transparent geometry for the first time. If **blend** is set to **replace**, backbuffer color will be provided as readable texture (if possible). It can be used to render refractive surfaces like water or hot air. `URHO3D_AMBIENT_PASS` shader define is present during this pass. If `URHO3D_LIGHT_PASS` shader define is present as well, shader is expected to apply lighting as well (like in **litalpha**).|
|**litalpha**|Used to apply lighting to already rendered transparent geometry. `URHO3D_LIGHT_PASS` shader define is present during this pass.||

### Utility Passes

Optional.

|Name|Description|
|-|-|
|**shadow**|Used to render shadow cast by the geometry. If absent, no shadow will be rendered. `URHO3D_DEPTH_ONLY_PASS` shader define is present during this pass.|
|**depth**|Used to render geometry to depth buffer during depth pre-pass. If absent, geometry is ignored during depth pre-pass. `URHO3D_DEPTH_ONLY_PASS` shader define is present during this pass.|

### User Passes

All non-standard user passes are ignored by default `RenderPipeline`.
They can be used in arbitrary user code.

## Built-in Techniques

### Lit Techniques

Materials that use lit techniques will receive lighting from direct, indirect and ambient light sources.

|Name|Description|
|-|-|
|**LitOpaque**|Opaque surface. Note that surfaces that use `ALPHAMASK` macro to make holes in the geometry are also considered opaque.|
|**LitTransparent**|Translucent surface with physically correct bright specular highlights (e.g. glass).|
|**LitTransparentFade**|Translucent surface with unrealistic faded specular highlights (e.g. hologram or ghost).|
|**LitWater**|Water technique: distorts and fades background objects.|
|**LitLegacyTerrain**|Legacy terrain shader that supports up to 3 diffuse textures blended by mask.|

There are aliases for techniques with `NORMALMAP` defined.
The only difference is that normal map from `Material` is used when possible.

|Name|Description|
|-|-|
|**LitOpaqueNormalMap**|Same as **LitOpaque** but with normal mapping.|
|**LitTransparentNormalMap**|Same as **LitTransparent** but with normal mapping.|
|**LitTransparentFadeNormalMap**|Same as **LitTransparentFade** but with normal mapping.|

### Unlit Techniques

Unlit techniques ignore all scene lighting and render input textures "as is".

|Name|Description|
|-|-|
|**UnlitOpaque**|Opaque surface. Note that surfaces that use `ALPHAMASK` macro to make holes in the geometry are also considered opaque.|
|**UnlitTransparent**|Translucent surface.|
