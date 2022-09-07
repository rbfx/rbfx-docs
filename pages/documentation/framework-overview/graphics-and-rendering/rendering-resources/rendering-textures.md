Texture Resource {#rendering-textures}
========================================

There are several types of texture resources:

- `Texture2D`
- `TextureCube`
- `Texture3D`
- `TextureArray`

Only `Texture2D` and `TextureCube` are supported on mobile platforms.

All these resources are optimized for rendering.
For easy CPU access use `Image` and `ImageCube` classes and `GetDecompressedImage()` utility function.

## Common Properties

Each texture may have auxiliary `*.xml` file that contains texture metadata:

|Name|Description|Default|
|-|-|-|
|**address**|Defines how texture is sampled when texture coordinates are outside of valid range. Can be defined for u, v, w coordinates separately.|wrap|
|> wrap|Sample infinitely tiled texture.||
|> mirror|Sample infinitely tiled texture mirrored at every other tile.||
|> clamp|Clamp texture coordinates to valid range.||
|> border|Clamp texture coordinates to valid range. Set texture border to fixed color. Not supported on mobiles. Avoid using it.||
|**border**|Border color for **border** address mode.| `(0,0,0,0)`|
|**filter**|Texture filtering mode. Controls interpolation within mip level, interpolation between mip levels, and anisotropy level.|default|
|> nearest|Pick best mip level, pick nearest texel.||
|> bilinear|Pick best mip level, blend 4 nearest texels.||
|> trilinear|Blend two best mip levels, blend 4 nearest texels.||
|> anisotropic|Blend two best mip levels, blend multiple texels with anisotropic filtering.||
|>&nbsp;nearestanisotropic|Pick best mip level, blend multiple texels with anisotropic filtering.||
|> default|Use global settings from renderer. **trilinear** by default||
|**mipmap**|Whether to enable texture mip levels.|false|
|**quality**|Number of mip levels skipped for different texture quality levels.|0|
|**srgb**|Whether to use hardware acceleration to convert from Gamma to Linear color space on sampling. Emulated in shader if not supported. Set if you use texture in Gamma space and render lighting in Linear space.|false|
|**linear**|Whether the texture colors are in Linear color space. Shouldn't be set simultaneously with **srgb** flag.|false|

## Cube Textures

Cube textures require `*.xml` file with which defines cube faces.

Cube faces may be stored in separate textures of in single texture with pre-defined layout:

- horizontal
- horizontalnvidia
- horizontalcross
- verticalcross
- blender

TODO: Insert examples?

## 3D Textures

3D textures may be stored in single texture file as is (if it is supported by the format, like `*.dds`).

3D textures also may be stored in 2D texture, where 3D slices are stored next to each other along X axis.

3D textures are not supported on mobiles.

## 2D Texture Arrays

Texture array is the array of 2D textures of the same size bound to the single shader unit.
All sampling is performed within single array slice, there's no automatic blending between different array elements.

Texture arrays are not supported on mobiles or low-end desktops.
