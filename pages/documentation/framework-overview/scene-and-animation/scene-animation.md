Scene Animation {#scene-animation}
========================================

## Animation

`Animation` resource describes abstract animation with specified length and a set of tracks describing how values change over time.

Animation time range always goes from **0** to **Length**, inclusive.

Each animation track consists of key frames, containing time, value, and optional tangents for cubic spline interpolation.

There are two types of animation tracks:

- Transformation track, aka `AnimationTrack`: mapping from time to position, rotation and/or scale.

- Variant track, aka `VariantAnimationTrack`: mapping from time to `Variant`. `Variant` type must be the same for all key frames within the track.

Track names must be unique within a type.

`Animation` doesn't have any logic except sampling tracks by time.
Track names don't have any semantics within `Animation` itself.

## Animation Parameters

`AnimationController` doesn't play `Animation` as is, it needs additional configuration describing how exactly animation is played.
`AnimationParameters` specifies this configuration.

`Animation` resource, animation name hash and animation `instanceIndex` are filled automatically and should stay the same during playback.

`Layer` controls the order in which animations are applied.
When applied, animations are sorted by layer.
Order of animations within the same layer is preserved.

If animation is not `Looped` and `RemoveOnCompletion` is set, animation will be removed automatically when time reaches `Time.Max` (when `Speed` is positive) or `Time.Min` (when `Speed` is negative).

`Time` of animation has current `Time.Value` and is always constrained between `Time.Min` and `Time.Max`.

`Speed` controls the rate of change in `Time` over real time.

If `Time.Value` of `Looped` animation reaches `Time.Max` (when `Speed` is positive) or `Time.Min` (when `Speed` is negative), `Time.Value` is snapped to the opposite extreme.

If `StartBone` is set, animation will be filtered to the specified sub-hierarchy of bones.

If `AutoFadeOutTime` is set and the animation is not `Looped`, animation will be smoothly faded out after it is finished.

`BlendMode` controls how the animation is applied:

* `Lerp`: value from the previous applied animation is blended with sampled animation value with specified `Weight`.
If `Weight` is `1.0`, applied animation completely overrides prior value.
If it's the first animation to be applied, it is always applied with full weight.

* `Additive`: sampled animation value is added to the previous applied animation with specified `Weight`.
If `Weight` is `1.0`, applied animation value is added as is.
If it's the first animation to be applied, it is not applied.

`Weight` indicates the current weight of animations (see above).
`TargetWeight` is the future weight value which will be fully applied in `TargetWeightDelay` seconds.

`Weight` automatically linearly transitions into `TargetWeight` over time, which is useful for fade-ins and fade-outs.

If `RemoveOnZeroWeight` is set (and it is set by default), animations that reach zero `Weight` are removed automatically.

## Animation Controller

### Overview

`AnimationController` is the main tool of controlling animations in `Scene`.

`AnimationController` allows user to play arbitrary set of animations with different settings.
It can be used as a base for more high-level animation systems like animation state machines.

Animations are defined by the instances of the `Animation` class.

When `AnimationController` has no animations playing, it has no effect on the `Scene`.

If the same `Animation` is being played by `AnimationController` simultaneously more than once, each instance is assigned with unique `instanceIndex`.

### Tracks

Animation tracks from `Animation` are applied based on track name and track type.

- Transformation tracks can be applied only to the `Node` positions, rotations and scales.

    Track name is used to search `Node` by name among all direct or indirect children of `AnimationController` or `StartBone` (if specified).
    `Node` that represents a `Bone` of a sibling `AnimatedModel` is preferred.

    Transformation tracks that reference a `Bone` are evaluated in worker thread during `AnimatedModel` update.

    Transformation tracks that reference any other `Node`, are applied on `AnimationController::Update`.

- Variant tracks are applied to the attributes of `Node`s and `Component`s.

    Track name is used as path relative to the `AnimationController` regardless of `StartBone`:

    `[path/to/node/]@[ComponentName[#Index]]/Attribute[/etc]`

    Examples:

    ```
    @/Position
    Parent Node/Child Node/@UserComponent/Attribute
    Some Node/@CollisionShape#3/Size
    Some Node/@/Variables/Variable Name
    Some Node/@AnimatedModel/Morphs/3
    ```

    Track name until `@` represents relative path to animated `Node`.

    Track name between `@` and the next `/` references the component name with optional index (if there are multiple instances of the same component).
    If empty, `Node` attribute is referenced.

    Next part of the path references the attribute name.
    In some specific cases, it may also reference sub-attribute.

Special cases of sub-attribute animations:

- Individual variables in "Variables" attribute of `Node` can be referenced by name.

- Individual morph weights in "Morphs" attribute of `AnimatedModel` can be referenced by index.

**Note**: be careful when transformation tracks are applied in a hierarchy. Example:

```
1. Parent
2.     Child A
3.         Parent
4.             Child A
5.             Child B
6.     Child B
```

In this hierarchy, transformation track with name "Child B" will be applied to the Node at line 5, while the user may have wanted to apply it to the closest child.

### Low-level API

There's low-level API that provides basic access to played animations described as array of `AnimationParameters`.

|Function|Description|
|-|-|
|`GetNumAnimations`|Return number of playing animations.|
|`GetAnimationParameters`|Return currently playing animation at given index.|
|`AddAnimation`|Append new animation to the end of the animation array.|
|`UpdateAnimation`|Update parameters of one playing animation. It's better to use `GetAnimationParameters` as base value for more predictable results. `Animation` and `instanceIndex` must remain unchanged.|
|`RemoveAnimation`|Remove playing animation at given index.|

Note that no smoothing is performed by low-level API, all changes are effective immediately.

### Medium-level API

Medium level allows easier and almost user-friendly manipulation of played animations.

Functions that reference playing animation by `Animation` will work with the latest added animation if the choice is ambiguous.

|Function|Description|
|-|-|
|`FindLastAnimation`|Get the latest played instance of `Animation`.|
|`GetLastAnimationParameters`|Get the parameters of the latest played instance of `Animation`.|
|`IsPlaying`|Check whether there is at least one playing `Animation`.|
|`PlayNew`|Play new instance of animation with specified parameters with smooth fade-in.|
|`PlayNewExclusive`|Same as `PlayNew`, but also fade out all other animations with the same layer.|
|`PlayExisting`|Play new instance of `Animation` with specified parameters and smooth fade-in if this `Animation` is not yet played. Otherwise, adjust `Speed` and smoothly adjust `Weight` of currently played animation, keeping other parameters intact.|
|`PlayExistingExclusive`|Same as `PlayExisting`, but also fade out all other animations with the same layer.|
|`Fade`|Fade animation to the specified weight. If `RemoveOnZeroWeight` is true, fading to zero weight will remove animation.|
|`Stop`|Fade out and remove *one* animation.|
|`StopLayer`|Stop all animations with specified layer.|
|`StopLayerExcept`|Stop all animations with specified layer except animation at specified index.|
|`StopAll`|Stop all animations.|
|`UpdateAnimationTime`|Adjust time of the animation, keeping all other parameters intact.|
|`UpdateAnimationWeight`|Adjust weight of the animation, keeping all other parameters intact.|
|`UpdateAnimationSpeed`|Adjust speed of the animation, keeping all other parameters intact.|
