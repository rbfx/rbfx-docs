Low-level Transport {#low-level-transport}
========================================

The `Network` subsystem provides reliable and unreliable UDP messaging using `SLikeNet`.
See Samples 16, 17, 52, 53, 110.

**Note**: Networking is not yet supported on Web platform.

## Starting Server

`Network::StartServer` starts UDP server at specified port.

User code should subscribe to networking events like `ClientConnected` and `ClientDisconnected`
and handle client connection and disconnection manually.

In order to use high-level scene replication, server should assign replicated `Scene` to client's `Connection`.

## Connecting to Server

`Network::Connect` connects to existing UDP server.

In order to use high-level scene replication, client should specify non-null `Scene` on `Connect`.
This scene may be empty or may be preloaded with common objects that don't need any replication (e.g. static level geometry).

If `Scene` on the server was loaded from file, client will attempt to load the same file.

## System Clock Synchronization

When connection is established, both sides independently synchronize system times with each other.

"Local time" is the result of `Time::GetSystemTime` on the machine executing the code.

"Remote time" is the result of `Time::GetSystemTime` on the remote machine.

After `Connection::IsClockSynchronized` returns `true`, system times are synchronized and can now be converted
via `Connection::RemoteToLocalTime` and `Connection::LocalToRemoteTime`.

Connection also provides estimated ping via `Connection::GetPing`.

Note that clock synchronization has limited precision that depends on connection quality.
Code that uses clock synchronization, should be able to handle time fluctuations in both direction.

`Network` properties control how often measurements are taken and how they are filtered.

## NAT punchthrough

It is possible to use NAT punchthrough functionality with network subsystem.
This requires NAT punchthrough master server to be running on a public host.
To set it up you first have to tell the networking subsystem the IP address or a domain name to NAT punchthrough master server, to do this, call SetNATServerInfo().

**Server**: Server should be started first by calling StartServer() and after that StartNATClient().
If connection to NAT punchthrough master server succeeds, unique GUID will be generated.
Clients should use this generated GUID to connect to this server.

**Client**: When server has been successfully started and connected with NAT punchthrough master server,
clients should connect to server by calling AttemptNATPunchtrough() and pass in the server generated GUID.

## Lan Discovery

Network subsystem supports LAN discovery mode to search for running servers.
When creating server you can set what data should be sent to the network when someone starts LAN discovery mode, see SetDiscoveryBeacon().
This data can contain any information about the server.

To start LAN discovery mode you should call DiscoverHosts().

When server is found, `NetworkHostDiscovered` event will be sent.

## Raw network messages

All network messages have an integer ID.

The first ID you can use for custom messages is 512.
Messages can be sent either unreliably or reliably, in-order or unordered.
The data payload is raw binary data that can be crafted by using for example VectorBuffer.

Use `Connection::SendMessage` to send a message to client or server.
Listen to `E_NETWORKMESSAGE` to handle incoming message.

Unordered messages have lower latency than ordered messages.
Unreliable messages have lower latency than reliable messages.

## Remote events

A remote event consists of its event type (name hash), a flag that tells whether it is to be sent in-order or unordered, and the event data VariantMap.

Use `Connection::SendRemoteEvent` to send a remote event.
It will arrive like normal event on the remote machine.

For security reasons, allowed remote events should be registered both on the client and the server via `Network::RegisterRemoteEvent`.

Remote events will always have the originating connection as a parameter in the event data.
