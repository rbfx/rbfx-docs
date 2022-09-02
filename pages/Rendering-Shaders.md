# Shaders

If shader name starts with `v2/`, runtime attempts to find the shader in `Shaders/GLSL/(Shader Name)`.
If found, runtime treats it as universal shader.

If not found, or if shader name starts with anything else, runtime attempts to find shader in `Shaders/(Platform)/(Shader Name)`.
If found, it treats it as platform-specific shader.

Default `RenderPipeline` appends `v2/` prefix for all shaders referenced by `Material` or `Technique` definitions.

Yes, it's ugly and complicated, but it's necessary to keep legacy rendering and `RenderPipeline` somewhat compatible, at least on the resource level.

## Universal Shaders

Universal shaders are written in special GLSL dialect and can be compiled for all supported platforms.

Note that universal shaders are compiled "as is" only on OpenGL platforms.
Other platforms may experience freezes due to shader conversion when shaders are compiled first time, especially in Debug mode.

Read shaders for detailed documentation about structures, functions, macros, constants and compile-time defines.

### Platform Defines

|Define|Description|
|-|-|
|`GL_ES`|Defined on all OpenGL ES platforms (Mobiles, Web).|
|`GL3`|Defined on OpenGL 3.3 platforms or better.|
|`D3D11`|Defined when GLSL shader is converted for DX11.|

### File Naming Conventions

|Name&nbsp;Prefix|Description|
|-|-|
|`_*.glsl`|Cannot be used directly and are used as "includes" for other shaders.|
|`M_*.glsl`|Can be used by `Material` and `Technique` resources and therefore can be applied to scene objects.|
|`P_*.glsl`|Can be used by post-processing effects.|
|(other)|Internal or legacy shaders. You probably don't need them.|

## Platform Shaders

It's possible to write platform-specific shaders. See `GLSL/Basic.glsl` and `HLSL/Basic.hlsl` as examples.

There is no reasonable defaults for scene shaders, so you will have to write platform-specific shader from scratch.
It's doable, but it's probably not worth it.
Consider using universal shaders instead.
You can always use some `#ifdef`-s to inject platform-specific logic.
