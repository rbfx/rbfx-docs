# Zone Component

`Zone` is special kind of `Drawable` that defines global rendering properties for OBB (oriented bounding box) region of the `Scene`.

Some properties affect individual objects within the zone, other properties affect `Camera` itself when it enters the zone.

Try to use as few `Zone`s as possible, preferably just one global `Zone`. Keep in mind that there's no property blending between different `Zone`s. Consider using more fine-tuned tools like [Light Probes](Rendering-Light-Probes.md) or [Reflection Probes](Rendering-Reflection-Probes.md) whenever possible.

## Basic Properties

|Property|Description|
|-|-|
|**Bounding&nbsp;Box**|Bounding box of affected area.|
|**Priority**|`Zone` with highest priority is chosen when multiple zones intersect in some point.|

## Mask Properties

Masks are used to split objects into different layers which don't affect each other.

**Zone Mask** controls whether the geometry or `LightProbeGroup` is affected by the `Zone`. Condition:

`zone.zone_mask & geometry.zone_mask != 0`

**Light Mask** controls whether the geometry or `LightProbeGroup` *affected by the zone* is lit by light source. Condition:

`zone.light_mask & geometry.light_mask & light.light_mask != 0`

**Shadow Mask** controls whether the geometry *affected by the zone* cast shadows for given light source. Condition:

`zone.shadow_mask & geometry.shadow_mask & light.shadow_mask != 0`

## Ambient Properties

Zone ambient is the most simple form of lighting that fills `Scene` with constant color.

Ambient is always dynamic and is applied immediately on change.

|Property|Description|
|-|-|
|**Ambient Color**|Color of ambient light.|
|**Ambient Brightness**|Arbitrary multiplier applied to **Ambient Color**.|

## Background Properties

Background lighting the lighting emitted by background fog or background texture.

If dynamic, it is applies in the same way as ambient.
If baked, it is emitted by the background.

Disabled by default.

|Property|Description|
|-|-|
|**Zone Texture**|Cube texture used for background lighting and reflections.|
|**Background&nbsp;Brightness**|Arbitrary multiplier applied to the color of fog or **Zone Texture**.|
|**Is&nbsp;Background&nbsp;Static**|Whether to bake background lighting into light maps and light probes. If enabled, no background lighting is applied in runtime!|

## Camera Properties

Camera properties don't affect scene geometries individually, they affect the rendering `Camera` itself.

|Property|Description|
|-|-|
|**Fog&nbsp;Color**|Color of background fog. Fills background if there's no `Skybox`. Defines background lighting if there's no **Zone Texture** assigned.|
|**Fog Start**|Distance where fog begins.|
|**Fog End**|Distance where everything fades into fog.|
