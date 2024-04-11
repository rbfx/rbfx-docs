PBR Texture Channels {#rendering-pbr}
========================================

## Overview
In a PBR pipeline, textures play a crucial role in defining the appearance of materials. Each texture channel encodes different properties that collectively contribute to the final look of the surface in a realistic manner.

## Albedo
The Albedo texture represents the base color of a material and is encoded in the RGB channels of albedo texture. It defines the color of the material under neutral lighting and does not contain any shading or lighting information.

```xml
<texture slot="Albedo" name="Textures/AlbedoMap.png" />
```

- Red (R): Represents the red component of the color.
- Green (G): Represents the green component of the color.
- Blue (B): Represents the blue component of the color.

## Transparency
The alpha channel is the fourth channel in albedo texture. It is used to encode the opacity level of the texture:

- Alpha (A): Represents the transparency of the texture. A value of 1.0 indicates full opacity, while 0.0 represents full transparency.

## Normal Map
The Normal Map is used to simulate fine surface details by altering the surface normals. It is typically stored in an RGB texture where each channel corresponds to a direction in space.

```xml
<texture slot="Normal" name="Textures/NormalMap.png" />
```

- Red (R): Represents the X component of the normal vector.
- Green (G): Represents the Y component of the normal vector.
- Blue (B): Represents the Z component of the normal vector.

This map affects how light interacts with the surface, creating the illusion of depth and complexity without adding additional geometry.

The normal vector is calculated in the shader using the following logic:

```glsl
normal = normalize(normalInput.rgb * 2.0 - 1.0)
```

## Packed Normal Map

In a packed normal map, the X and Y components of the normal vector are stored in the G and A channels of the texture, respectively. Packed mode is enabled with **PACKEDNORMAL** define in fragment shader.

- Green (G): Stores the X component of the normal vector.
- Alpha (A): Stores the Y component of the normal vector.

The third component, Z, is calculated in the shader using the following logic:

```glsl
normal.xy = normalInput.ag * 2.0 - 1.0;
normal.z = sqrt(max(1.0 - dot(normal.xy, normal.xy), 0.0));
```

This calculation is based on the fact that a normal vector is of unit length, so we can derive the Z component from the X and Y components.

## Metallic, Roughness, and Ambient Occlusion

The Metallic, Roughness, and Ambient Occlusion properties are often packed into a single texture to optimize performance. They are encoded into the R, G, and A channels respectively.

```xml
<texture slot="Properties" name="Textures/PropertiesMap.png" />
```

- Red (R): Encodes the Roughness property, indicating how rough or smooth a surface is. A value of 1.0 means fully rough, while 0.0 is completely smooth.
- Green (G): Encodes the Metallic property, defining whether a surface is metallic (1.0) or non-metallic (0.0).
- Alpha (A): Encodes the Ambient Occlusion, which simulates the soft shadowing that occurs in crevices and where objects meet.

By combining these channels, the PBR pipeline can efficiently simulate realistic material properties and interactions with light.

The Roughness and Metallic components from texture get multiplied by material properties in the shader.
```glsl
half3 rmo = texture(propertiesMap, texCoord).rga;
rmo.xy *= vec2(roughnessValue, metalnessValue);
```
