# @hyperspace/client

Standalone Hyperspace RPC client

```
npm install @hyperspace/client
```

# Usage

``` js
const HyperspaceClient = require('@hyperspace/client')

const client = new HyperspaceClient() // connect to the daemon

const corestore = client.corestore() // make a corestore

const feed = corestore.get(someHypercoreKey) // make a hypercore

await feed.get(42) // get some data from the hypercore
```

# API

#### `const client = new HyperspaceClient([options])`

Make a new Hyperspace RPC client. Options include:

``` js
{
  host: 'hyperspace' // the ipc name of the running server
                     // defaults to hyperspace
}
```

#### `await HyperspaceClient.serverReady([host])`

Static method to wait for the local IPC server to be up and running.

#### `status = await client.status([callback])`

Get status of the local daemon. Includes stuff like API version etc.

#### `await client.close([callback])`

Fully close the client. Cancels all inflight requests.

#### `await client.ready([callback])`

Wait for the client to have fully connected and loaded initial data.

#### `corestore = client.corestore([namespace])`

Make a new remote corestore. Optionally you can pass a specific namespace
to load a specific corestore. If you do not pass a namespace a random one is generated for you.

#### `client.network`

The remote corestore network instance.

## Remote Corestore

The remote corestore instances has an API that mimicks the normal [corestore](https://github.com/andrewosh/corestore) API.

#### `feed = corestore.get([key])`

Make a new remote hypercore instance. If you pass a key that specific feed is loaded, if not a new one is made.

#### `feed = corestore.default()`

Get the "default" feed for this corestore, which is derived from the namespace.

#### `feed.name`

The name (namespace) of this corestore.

#### `async feed.close([callback])`

Close the corestore. Closes all feeds made in this corestore.

## Remote Networker

The remote networker instance has an API that mimicks the normal [corestore networker](https://github.com/andrewosh/corestore-networker) API.

#### `await network.ready([callback])`

Make sure all the peer state is loaded locally. `client.ready` calls this for you.
Note you do not have to call this before using any of the apis, this just makes sure network.peers is populated.

#### `networks.peers`

A list of peers we are connected to.

#### `network.on('peer-add', peer)`

Emitted when a peer is added.

#### `network.on('peer-remove', peer)`

Emitted when a peer is removed.

#### `await network.configure(discoveryKey, options)`

Configure the network for this specific discovery key.
Options include:

```
{
  lookup: true, // should we find peers?
  announce: true, // should we announce ourself as a peer?
  flush: true // wait for the full swarm flush before returning?
  remember: false // persist this configuration so it stays around after we close our session?
}
```

#### `const ext = network.registerExtension(name, { encoding, onmessage, onerror })`

Register a network protocol extension.

## Remote Feed

The remote feed instances has an API that mimicks the normal [hypercore](https://github.com/hypercore-protocol/hypercore) API.

# License

MIT
