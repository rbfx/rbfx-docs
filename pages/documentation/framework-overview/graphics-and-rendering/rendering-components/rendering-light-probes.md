Light Probe Group Component {#rendering-light-probes}
========================================

`LightProbeGroup` defines a group of light probes which are used to calculate volumetric global illumination.

It is usually filled only during light baking, but it's theoretically possible to change light probe group in runtime as long `GlobalIllumination` cache is properly updated.

Unless `GlobalIllumination` is updated, total number of `LightProbeGroup` instances in the `Scene` is irrelevant.
Only the number of light probes and their arrangement matters.

Component properties only allow probe placement in uniform grid.
It is possible to place light probes in arbitrary positions by `SetLightProbes()` call.
TODO: Implement Editor UI for light probe editing.

*All enabled light probes* in the `Scene` should satisfy certain requirements in order to have correct GI:

- Keep light probes slightly above geometry surfaces.
- Don't submerge light probes into geometry.
- Keep light probes sufficiently dense where lighting changes rapidly and these changes matter.
- Circumscribed convex volume of all enabled light probes shouldn't be too thin, narrow or flat to avoid tetrahedral mesh degradation.

## Basic Properties

|Property|Description|
|-|-|
|**Zone Mask**|Controls whether the `Zone` affects this group.|
|**Light Mask**|Controls whether the light source lit this group|

## Auto Placement Properties

|Property|Description|
|-|-|
|**Auto Placement**|Whether to enabled automatic placement. If enabled, erases all existing light probes.|
|**Auto Placement Step**|Desired distance between probes.|
|**Local&nbsp;Bounding&nbsp;Box**|Bounding box of placed probes.|
