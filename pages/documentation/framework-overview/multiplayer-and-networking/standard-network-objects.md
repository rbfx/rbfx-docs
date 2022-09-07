Standard Network Objects {#standard-network-objects}
========================================

## StaticNetworkObject

Inherits `NetworkObject`.

`StaticNetworkObject` performs one-time replication of the following:

* Parent `NetworkObject` in the hierarchy.

* Name of the `Node`.

* Position, rotation and scale in world space.

* If specified, name of XML prefab to be instantiated on the client side.

    On client, this prefab will be instantiated on the initialization of the `NetworkObject`.

`StaticNetworkObject` also maintains its position in the hierarchy of `NetworkObject`s.

## BehaviorNetworkObject

Inherits `StaticNetworkObject`.

`BehaviorNetworkObject` allows user to supply networking behaviors as independent components.

When `BehaviorNetworkObject` is created on the client or on the server, `NetworkBehavior`s are attached to the *closest* parent `NetworkObject`.

The client and the server are responsible for the `BehaviorNetworkObject` having the same set of behaviors.
The list of connected behaviors should not change during the lifetime of the `BehaviorNetworkObject`.

During its lifetime, `BehaviorNetworkObject` redirects `NetworkObject` calls directly into connected `NetworkBehavior`s.

User class derived from `NetworkBehavior` should specify which callbacks should be called for this behavior, for performance reasons.

See \ref standard-network-behaviors for built-in `NetworkBehavior`s.
