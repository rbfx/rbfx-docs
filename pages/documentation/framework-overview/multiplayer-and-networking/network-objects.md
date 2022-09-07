Network Objects {#network-objects}
========================================

## Overview

`NetworkObject` represents an independent entity with unique ID that is automatically replicated over network.
`NetworkObject` doesn't contain any useful behaviors, but it can be derived to implement arbitrary replication logic.

**Note**: `Node` may have *at most* one `NetworkObject` component attached.

**Note**: The client code *must never* create `NetworkObject` components on its own!

## Lifetime

When user creates `NetworkObject` on the server, the following replication sequence is performed:

1. Unique ID is assigned to this `NetworkObject`.

1. If this object is not relevant for the client, it is not replicated and nothing else happens.

1. Server sends the snapshot that consists of:

    1. Unique ID of the `NetworkObject`.

    1. Type of the derived component.

    1. User-defined payload of data.

    Note that `NetworkObject` itself doesn't write *any* information about the nodes being replicated.

1. When client reads the snapshot, it does the following:

    1. New node is created in the `Scene`.

    1. New `NetworkObject` component of specified type is created for this node.

    1. User-defined payload is read.

It's up to derived classes to provide necessary data payload.

When the server removes `NetworkObject`, the client executes `NetworkObject::PrepareToRemove` and removes the `NetworkObject` *component*.

Default implementation of `NetworkObject::PrepareToRemove` removes the owner `Node` as well, but this behavior may be overridden at your own risk.

## Ownership

Server may assign ownership to the `NetworkObject` by calling `NetworkObject::SetOwner` right after `NetworkObject` is created.

Ownership may not be changed afterwards.

`ServerReplicator` tracks all `NetworkObject`s owned by all clients.
`ClientReplica` tracks all `NetworkObject`s owned by itself.

Only `NetworkObject`s owned by the client may send the data from this client back to the server.

## Mode

User code can check the mode of `NetworkObject` to have different behaviors on the client and the server.

|Mode|Description|
|-|-|
|`Standalone`|The scene is running in standalone mode, *or* the `NetworkObject` has just been created and is not yet processed by the client or the server.|
|`Server`|The scene is running on the server. The `NetworkObject` has been processed by the `ReplicationManager` and is possibly replicated on remote clients.|
|`ClientReplicated`|The scene is running on the client. The `NetworkObject` has been fully created. The `NetworkObject` is not owned by this connection.|
|`ClientOwned`|The scene is running on the client. The `NetworkObject` has been fully created. The `NetworkObject` is owned by this connection.|

Note that `NetworkObjectMode` are assigned *after* `InitializeOnServer` or `InitializeFromSnapshot` callbacks.
This is intentional and allows to distinguish objects that are being initialized now from already processed objects.

## Relevance

`NetworkObjectRelevance` indicates how much does any specific client cares about this `NetworkObject`.

|Relevance|Description|
|-|-|
|`Irrelevant`|The client is not interested in this `NetworkObject` and it won't be aware of this object.|
|`NoUpdates`|The client will not receive unreliable updates, but it will be aware of this object and will receive reliable updates.|
|`NormalUpdates`|The client needs normal updates for this `NetworkObject`. This is default relevance of any `NetworkObject`.|
|Any number greater than 1|The client needs throttled unreliable updates for this `NetworkObject`. Only every N-th unreliable update will be sent to the client.|

## Hierarchy

Hierarchy of `NetworkObject`s is tracked on server, and it is guaranteed that parent `NetworkObject` is processed before a child `NetworkObject`.

However, `NetworkObject` doesn't replicate this hierarchy on its own and it's up to derived classes to preserve this hierarchy on the client.

## Updates

When `NetworkObject` is created on the client, it is created from the snapshot.
Client receives exactly one snapshot for any specific `NetworkObject`.

There are three types of updates that `NetworkObject` may receive after the snapshot.

* **Reliable update** is sent to the client.

    It should contain the only the data crucial for game logic.
    When client receives reliable update, it is guaranteed that:

    * The client has already received either a snapshot or previous reliable update.

    * Reliable updates, creation and destruction *of all `NetworkObject`s* are processed in the same order as they happen on the server.

* **Unreliable update** is sent to the client.

    Is should contain the data that needs to be delivered quickly.

    No guarantees are given for unreliable updates. For example:

    * Unreliable update may be lost and never processed by the client.

    * Unreliable update may be processed in the arbitrary order.

    * Unreliable update may be processed at arbitrary time relative to reliable updates.

* **Unreliable feedback** is similar to **Unreliable update**, except it is sent from the client to the server.

    It is usually used to handle user input.

## Interface

`NetworkObject` provides a variety of virtual methods that may be overridden.

These methods are called when the server is running:

|Function|Description|
|-|-|
|`InitializeOnServer`|Called for every `NetworkObject` before any server-side processing.|
|`GetRelevanceForClient`|Evaluates the relevance of the `NetworkObject` for specified client connection.|
|`WriteSnapshot`|Writes a data payload that should be sufficient to construct the object on the client.|
|`PrepareReliableDelta`|Checks whether the `NetworkObject` has changed and reliable update is required for all interested clients.|
|`WriteReliableDelta`|Writes data payload for reliable update.|
|`PrepareUnreliableDelta`|Checks whether the `NetworkObject` needs to send unreliable update. You may want to send the same thing several times due to possible update loss.|
|`WriteUnreliableDelta`|Writes data payload for unreliable update.|
|`ReadUnreliableFeedback`|Reads data payload from the client.|

These methods are called when the client is running:

|Function|Description|
|-|-|
|`InitializeFromSnapshot`|Called for every `NetworkObject` before any client-side processing. Contains payload from `WriteSnapshot`.|
|`PrepareToRemove`|Called before object removal.|
|`InterpolateState`|Called in the beginning of each frame after processing incoming messages.|
|`ReadReliableDelta`|Reads the data payload of reliable update.|
|`ReadUnreliableDelta`|Reads the data payload of unreliable update.|
|`PrepareUnreliableFeedback`|Checks whether the owned `NetworkObject` needs to send feedback to the server.|
|`WriteUnreliableFeedback`|Writes data payload for feedback.|

These methods are unrelated to client or server:

|Function|Description|
|-|-|
|`InitializeStandalone`|Called for every `NetworkObject` created in the `Scene` that is not (yet) handled by the server-side or client-side logic.|
|`Update`|Similar to `SceneUpdate` event, but it is aware of the network context. In `Standalone` mode it's identical to `SceneUpdate` event. In `Server` mode it's called with fixed rate and fixed time step. In `Client` mode it is called like normal `SceneUpdate` but with dilated time steps corresponding to the changes in **ReplicaTime** and **InputTime**.|
|`OnTransformUpdated`|Called when the `NetworkObject` transform or place in the node hierarchy changes.|

## Built-in Implementations

See [Standard Network Objects](Standard-Network-Objects.md).
