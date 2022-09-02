# Standard Network Behaviors

## ReplicatedTransform

Replicates position and rotation of the `Node` it is attached to.

One `NetworkObject` may have multiple `ReplicatedTransform` behaviors attached to different children.

Synchronization and extrapolation are optional for both position and rotation.

It's possible to configure smoothing constants and thresholds.

In "Track Only" mode `ReplicatedTransform` doesn't affect actual `Node` transform on the client.
It may be useful for client-side prediction.

## ReplicatedAnimation

Replicates currently playing animations of sibling `AnimationController`.

Animation times are precisely synchronized.

**Note**: When animation is played for the first time, its replication may be delayed.

## FilteredByDistance

Server only.

Provides relevance management based on the distance from this `Node` to the `NetworkObject`s owned by the client.

If the distance between this `Node` and the closest client-owned `Node` is greater than specified threshold, relevance is set to configured value for this `NetworkObject`.

Otherwise, no relevance is provided and default value is used.

It allows to stack multiple `FilteredByDistance` behaviors on the same `NetworkObject`, as long as they go from bigger to lower threshold distance.

## TrackedAnimatedModel

Server only.

Tracks bone positions and rotations of an `AnimatedModel`.
Provides temporal queries to perform raycasts at different points in time.

## PredictedKinematicController

Client-side predicted `KinematicCharacterController`.

Note that manipulation of `KinematicCharacterController` on the client side should be performed via `PredictedKinematicController` only.
Supports only moving and jumping for now.
