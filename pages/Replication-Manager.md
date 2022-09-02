# Replication Manager

## Overview

`ReplicationManager` is the core component that drives `Scene` replication over `Network`.

`ReplicationManager` has three modes of execution:

* **Standalone**. `Scene` is neither server nor client.

  If `ReplicationManager` is created manually, it stays in **Standalone** mode.

  If client got disconnected from the server, `ReplicationManager` transitions to **Standalone** mode automatically.

  **Standalone** mode enables reusing the logic in `NetworkObject`s for local game modes.

* **Server**. `Scene` is running on the server.

  When `Connection::SetScene` is called on server, `ReplicationManager` is automatically created for this scene if missing and transitions to **Server** mode.

  `ServerReplicator` utility class performs all server-side logic.

* **Client**. `Scene` is running on the client.

  When `Network::Connect` call with non-null `Scene` is successful, `ReplicationManager` is automatically created for this scene if missing and transitions to **Client** mode.

  `ClientReplica` utility class performs all client-side logic.

## Time Management

Time on server (aka **ServerTime**) is discrete and advances with specified frequency (30 frames per second by default).

Time on client is more complicated.

Client attempts to estimate current **ServerTime**, but it doesn't use it directly.
Instead, client maintains two times: **ReplicaTime** and **InputTime**.
Both **ReplicaTime** and **InputTime** are steadily increasing and are smoothly corrected on clock updates. Client times are not discrete and smoothly transition from frame to frame.

**ReplicaTime** is the time that can be reliably interpolated from the data received from the server.
The delay usually consists of the ping itself and small **InterpolationDelay** used for smoother value sampling.

If the delay between **ReplicaTime** and **ServerTime** is too big, it's clamped and the client is forced to extrapolate server state with occasional extrapolation errors.

**InputTime** is the time of current client input being processed on the server.
It consists of the ping itself and additional **InputBuffer** which depends on the connection quality and is used to recover lost input.

This diagram (hopefully) illustrates the relationship between **ServerTime**, **ReplicaTime** and **InputTime**.

[[images/Network-Times.svg]]

## Sequence of the Frame on Server

Sequence of the network frame on the server:

1. All incoming messages are processed.

1. Index of current frame is incremented.

1. `BeginServerNetworkFrame` event is sent. User input may be applied here.

1. Logic and physics is updated for the whole network frame.

1. `EndServerNetworkFrame` event is sent. Server state may be tracked here.

1. Update is sent to clients.

## Sequence of the Frame on Client

Network frames on the client are determined based on **InputTime**: "network frame" happens when integer part of **InputTime** changes.

Sequence of the "network frame" on the client:

1. All local input and incoming messages are processed.

1. **ReplicaTime** and **InputTime** are updated. **InputTime** is incremented here.

1. State of `NetworkObject`s is synchronized as of **ReplicaTime**.

1. `BeginClientNetworkFrame` event is sent.

1. Logic and physics is updated normally. Physics updates are synchronized with "network frame".

1. `EndClientNetworkFrame` event is sent.

1. Client input is sent to the server.

If `PhysicsPreStep` event has parameter `NetworkFrame`, the current state of physical world on the client matches the state of *previous* frame on the server.

Other render frames on the client have simpler sequence:

1. All local input and incoming messages are processed.

1. **ReplicaTime** and **InputTime** are updated.

1. State of `NetworkObject`s is synchronized as of **ReplicaTime**.

1. Logic and physics is updated normally.
