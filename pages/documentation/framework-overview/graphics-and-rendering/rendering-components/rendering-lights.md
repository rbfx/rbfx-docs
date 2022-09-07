Light Component {#rendering-lights}
========================================

`Light` component applies direct lighting to geometries in the `Scene`.
There are three types of light sources:

- Directional Lights;
- Point Lights;
- Spot Lights.

## Light Type

### > Directional

Light is emitted from infinitely distant point. Position of the owner `Node` is irrelevant, only orientation matters. Examples: Sun or Moon.

Try to have at most one enabled **Directional** light in the `Scene` for sake of performance.

### > Spot

Light is emitted from light source position to one dominant direction with configured spread. Example: flashlight.

Spot lights support relatively cheap shadows.
Prefer spot lights to point lights whenever possible.

### > Point

Light is emitted from light source position to all directions equally. Examples: campfire, torch.

Shadows are relatively expensive.

## Basic Properties

|Property|Description|
|-|-|
|**Is Enabled**|If false, does not affect the `Scene` in any way.|
|**Light Type**|Type of light source.|
|> Directional|(see above)|
|> Spot|(see above)|
|> Point|(see above)|
|**Light&nbsp;Importance**|A hint for desired light quality during Forward lighting. If Deferred lighting is used, this hint is ignored.|
|> Auto|Lighting is applied either per-pixel, per-vertex or per-geometry, depending on quotas of per-pixel and per-vertex lights.|
|> Important|Lighting is always applied per-pixel. If light casts shadows, it is always considered **Important**.|
|>&nbsp;Not&nbsp;Important|Lighting is never applied per-pixel. It is applied either per-vertex or per-geometry depending on the quota of per-vertex lights.|
|**Light Mode**|Determines how this `Light` will be treated by light baking system.|
|> Realtime|`Light` is ignored by light baking system and is completely rendered in runtime.|
|> Mixed|`Light` contributes to indirect lighting during light baking. Direct lighting and shadows are still calculated in runtime.|
|> Baked|`Light` is ignored during rendering. All lighting is baked into light maps and light probes.|
|**View Mask**|Light is ignored if its **View Mask** doesn't match **View Mask** of the `Camera`.|
|**Light Mask**|See \ref rendering-zones|
|**Can&nbsp;Be&nbsp;Occluded**|Whether to disable this light if it's invisible. You may want to disable that only if you have lit non-occluded geometries. Which is probably bad idea.|

## Lighting Properties

|Property|Description|
|-|-|
|**Color**|Color of light source, in gamma color space.|
|**Specular Intensity**|Intensity of specular highlights. Ignored by PBR materials.|
|**Brightness&nbsp;Multiplier**|Multiplier applied to **Color** value.|
|**Indirect Brightness**|Brightness of baked indirect lighting.|
|**Range**|Lighting is applied to the geometries within **Range** units around light `Node` position.|
|>|During baking of **Directional** lights, **Range** is treated as light source size in degrees.|
|**Draw Distance**|Light source is disabled at **Draw Distance**. If 0, light source is never disabled.|
|**Fade Distance**|Light source intensity starts to fall at **Fade Distance** and reaches zero at **Draw Distance**. If 0, there's no fade.|
|**Spot FOV**|**Spot lights only.** Maximum angle of light rays spread.|
|**Spot&nbsp;Aspect&nbsp;Ratio**|**Spot lights only.** Ratio of light spread along x axis to light spread along y axis.|

## Shadow Properties

Shadows are rendered differently depending on the **Light Type**:

- Spot lights use one shadow map that covers whole light frustum.
- Point lights use 6 shadow maps that cover each face of axis-aligned cube centered at the light source. Each face is rendered as 90 degree square Spot light.
- Directional lights use from 1 to 4 shadow maps that cover different splits of main `Camera` frustum.

|Property|Description|
|-|-|
|**Cast Shadows**|Whether to cast shadows.|
|**Shadow Distance**|Distance to *light source* where it stops casting any shadows. Note that shadow casting is not controlled per-geometry, only per-light. If zero, shadows are rendered as long as lit geometry is visible.|
|**Shadow&nbsp;Fade&nbsp;Distance**|Distance to *light source* where shadows start to disappear. If zero, there is no smooth fade out for shadows.|
|**Shadow Intensity**|Amount of light *from light source* that leaks into shadow.|
|**Shadow Resolution**|Multiplier of shadow map resolution. Note that final shadow map size is still clamped to valid range.|
|**Auto-Reduce Size**|Whether to adjust shadow map density automatically. If disabled, shadow map will always have the exact specified size.|
|**Near/Farclip Ratio**|**Spot and Point lights only.** Determines how close to light source the geometry should be to cast shadow. Decreasing this value decreases shadow quality.|

### Shadow Properties for Directional Lights

|Property|Description|
|-|-|
|**Focus To Scene**|Whether to adjust shadow maps to rendered scene instead of `Camera` frustum. Increases shadow quality and shadow instability on camera movement.|
|**Non-uniform View**|Whether to allow non-square shadow maps. Increases shadow quality and shadow instability on camera movement.|
|**CSM Splits**|Max distances for each cascade.|
|**CSM Fade Start**|Distance to *geometries* where shadow starts to disappear.|
|**CSM&nbsp;Bias&nbsp;Auto&nbsp;Adjust**|Whether to magically adjust shadow bias on per-cascade basis. Affects **Depth Constant Bias** and **Depth Slope Bias**.|
|**View Size Quantize**|Magic factor to reduce shadow instability.|
|**View Size Minimum**|Minimum size of shadow map of single cascade.|
|**Max Extrusion**|Maximum distance from `Camera` frustum to the outside geometry where it can still cast shadow.|

### Shadow Bias Properties

These are magic values used to reduce shadow artifacts.

|Property|Description|
|-|-|
|**Depth Constant Bias**|Controls shifting geometry along light rays unconditionally.|
|**Depth Slope Bias**|Controls shifting geometry along light rays depending on surface orientation.|
|**Normal Offset**|Controls shifting geometry along its vertex normals.|

Depth bias used to shift shadow caster geometry along light rays.
It is calculated by the following formula:

`final_bias = constant_bias + slope_bias * tg(angle)`

Where `angle` is the angle between geometry surface normal and light rays.

## Other Properties

### Attenuation Texture

Point and Spot lights may have custom texture that defines a function used to calculate light intensity falloff with distance.

If not set, default falloff is `x^-2`.

### Light Shape Texture

TODO: Support light shapes for Directional light.

Point and Spot lights may have custom shape texture that modulated emitted light intensity.
It can be used to project arbitrary textures via light sources.

Point light expects `TextureCube`.
Spot light expects `Texture2D`.

Orientation of shape texture for Point light is controlled by orientation of the owner `Node`.

When shape texture is set for Spot light, default angular falloff of light intensity is disabled.

It can be used for effects like projectors, custom flashlights, flares from disco ball, etc.
