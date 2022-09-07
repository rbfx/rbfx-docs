Reflection Probe Component {#rendering-reflection-probes}
========================================

`ReflectionProbe` defines a reflection cubemap that will be applied to `Drawable`s intersecting with the probe. Reflection from `ReflectionProbe` takes precedence over reflection from `Zone`.

The rule of thumb is to use `Zone` to define default background cubemap for reflection and use `ReflectionProbe`s for all local reflections. `ReflectionProbe`s may provide much better reflection quality than `Zone`s.

`ReflectionProbeManager` component should be present in the `Scene` for `ReflectionProbe`s to work.

When cubemap re-rendering is requested, cubemaps are not updated immediately, updates may be spread across frames. It's possible to control this behavior in some degree.

## Reflection Probe Manager Properties

|Property|Description|
|-|-|
|**Query&nbsp;Padding**|Controls how often reflection probes are re-queried for `Drawable`. Consider not touching this.|
|**Render&nbsp;Budget**|Desired number of cubemap *faces* rendered per frame for dynamic probes.|

## Reflection Probe Blending

When blending is enabled in `Drawable` properties, two reflection cubemaps may be applied to the object.
Only two most relevant `ReflectionProbe`s are blended, or the most relevant `ReflectionProbe` and the `Zone`.

For each `ReflectionProbe`, relative volume of intersection with `Drawable` is determined.
This volume is used to calculate blend factor.

|Case|Formula|
|-|-|
|Two `ReflectionProbe`s with same **Priority** are blended|`mix(P1,P2,V2/(V1+V2))`|
|`ReflectionProbe` with higher **Priority** is blended with another `ReflectionProbe`|`mix(P1,P2,1-V1)`|
|`ReflectionProbe` is blended with `Zone`|`mix(P1,Z,1-V1)`|

## Common Reflection Probe Properties

|Property|Description|
|-|-|
|**Is Enabled**|Whether the probe affects `Scene` in any way.|
|**Is Movable**|Whether the probe can be efficiently moved in realtime. Use as few **Movable** probes as possible.|
|**Probe Type**|Type of probe cubemap.|
|> Baked|Cubemap is baked from the `Scene` and is saved to file.|
|> Mixed|Cubemap can be efficiently rendered on demand.|
|> Dynamic|Cubemap can be efficiently rendered every frame.|
|> Custom Texture|Cubemap is provided by the user.|
|**Is&nbsp;Realtime&nbsp;Update**|Whether to automatically re-render reflection probe every frame.|
|**Is Sliced Update**|Whether to split update across 6 frames (**Dynamic** probe only).|
|**Bounding&nbsp;Box**|Local-space bounding box of the area which is affected by the probe.|
|**Priority**|Relative priority of the probe comparing to other probes.|
|**Texture**|User-provided texture of the probe.|

## Reflection Probe Box Projection

By default, reflection probes are considered infinite, which may result in artifacts when used indoors.

Box Projection may be used to apply finite reflection from cubemap.

Note that projection box is defined in world space and is always axis-aligned for performance reasons.

To avoid projection artifacts, objects affected by the probe should not leave **Projection Box** boundaries. It practically means that either **Projection Box** should be bigger than the **Bounding Box**, or no reflective objects should be placed between smaller **Projection Box** boundaries and bigger **Bounding Box** boundaries.

|Property|Description|
|-|-|
|**Use&nbsp;Box&nbsp;Projection**|Whether to use Box Projection (if supported).|
|**Projection&nbsp;Box**|World-space projection box.|

## Rendering Properties

These properties define how the cubemap is rendered.

**Note:** Add more properties on demand.

|Property|Description|
|-|-|
|**Texture Size**|Size of cubemap texture.|
|**View Mask**|Mask used to cull rendered objects.|
|**Near Clip**|Near camera clip distance.|
|**Far Clip**|Far camera clip distance.|
