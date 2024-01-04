## LLTurbo

> A stable and updated wrapper around Lavalink

![Github Stars](https://img.shields.io/github/stars/Fyphen1223/LLTurbo?style=flat-square)
![GitHub issues](https://img.shields.io/github/issues-raw/Fyphen1223/LLTurbo?style=flat-square)

### Features

✅ Stable

✅ Documented

✅ Updated

✅ Extendable

✅ ESM & CommonJS supported

✅ Very cold-hearted (Very Important)

### Supported Libraries

Refer to [/src/connectors](https://github.com/Fyphen1223/LLTurbo/tree/master/src/connectors) for list of supported libraries + how to support other libraries

### Installation

> `npm install LLTurbo`

### Documentation

https://LLTurbo.shipgirl.moe/

### Small code snippet examples

> Initializing the library (Using Connector Discord.JS)

```js
const { Client } = require("discord.js");
const { LLTurbo, Connectors } = require("LLTurbo");
const Nodes = [
  {
    name: "Localhost",
    url: "localhost:6969",
    auth: "re_aoharu",
  },
];
const client = new Client();
const LLTurbo = new LLTurbo(new Connectors.DiscordJS(client), Nodes);
// ALWAYS handle error, logging it will do
LLTurbo.on("error", (_, error) => console.error(error));
client.login("token");
// If you want LLTurbo to be available on client, then bind it to it, here is one example of it
client.LLTurbo = LLTurbo;
```

> Never initialize LLTurbo like this, or else LLTurbo will never initialize, start LLTurbo before you call `client.login()`

```js
// NEVER DO THIS, OR LLTurbo WILL NEVER INITIALIZE
client.on("ready", () => {
  client.LLTurbo = new LLTurbo(new Connectors.DiscordJS(client), Nodes);
});
```

> Join a voice channel, search for a track, play the track, then disconnect after 30 seconds

```js
const player = await LLTurbo.joinVoiceChannel({
  guildId: "your_guild_id",
  channelId: "your_channel_id",
  shardId: 0, // if unsharded it will always be zero (depending on your library implementation)
});
// player is created, now search for a track
const result = await player.node.rest.resolve("scsearch:snowhalation");
if (!result?.tracks.length) return;
const metadata = result.tracks.shift();
// play the searched track
await player.playTrack({ track: metadata.encoded });
// disconnect after 30 seconds
setTimeout(() => LLTurbo.leaveVoiceChannel(player.guildId), 30000).unref();
```

> Playing a track and changing a playback option (in this example, volume)

```js
await player.playTrack({ track: metadata.encoded });
await player.setGlobalVolume(50);
```

> Updating the whole player if you don\'t want to use my helper functions

```js
await player.update({ ...playerOptions });
```

> Setting a custom get node ideal function

```js
const LLTurbo = new LLTurbo(
  new Connectors.DiscordJS(client),
  [{ ...yourNodeOptions }],
  {
    ...yourLLTurboOptions,
    nodeResolver: (nodes, connection) => getYourIdealNode(nodes, connection),
  }
);
const player = await LLTurbo.joinVoiceChannel({
  guildId: "your_guild_id",
  channelId: "your_channel_id",
  shardId: 0,
});
```

### Updating from V3 -> V4 (notable changes)

> The way of joining and leaving voice channels is now different

```js
const { Client } = require("discord.js");
const { LLTurbo, Connectors } = require("LLTurbo");
const Nodes = [
  {
    name: "Localhost",
    url: "localhost:6969",
    auth: "marin_kitagawa",
  },
];
const client = new Client();
const LLTurbo = new LLTurbo(new Connectors.DiscordJS(client), Nodes);
LLTurbo.on("error", (_, error) => console.error(error));
client.login("token");
client.once("ready", async () => {
  // get a node with least load to resolve a track
  const node = LLTurbo.options.nodeResolver(LLTurbo.nodes);
  const result = await node.rest.resolve("scsearch:snowhalation");
  if (!result?.tracks.length) return;
  // we now have a track metadata, we can use this to play tracks
  const metadata = result.tracks.shift();
  // you now join a voice channel by querying the main LLTurbo class, not on the node anymore
  const player = await LLTurbo.joinVoiceChannel({
    guildId: "your_guild_id",
    channelId: "your_channel_id",
    shardId: 0, // if unsharded it will always be zero (depending on your library implementation)
  });
  // if you want you can also use the player.node property after it connects to resolve tracks
  const result_2 = await player.node.rest.resolve("scsearch:snowhalation");
  console.log(result_2.tracks.shift());
  // now we can play the track
  await player.playTrack({ track: metadata.encoded });
  setTimeout(async () => {
    // simulate a timeout event, after specific amount of time, we leave the voice channel
    // you now destroy players / leave voice channels by calling leaveVoiceChannel in main LLTurbo class
    await LLTurbo.leaveVoiceChannel(player.guildId);
  }, 30000);
});
```

> Usual player methods now return promises

```js
await player.playTrack(...data);
await player.stopTrack();
```

> There are 2 kinds of volumes you can set, global and filter

```js
// global volume accepts 0-1000 as it's values
await player.setGlobalVolume(100);
// to check the current global volume
console.log(player.volume);
// filter volume accepts 0.0-5.0 as it's values
await player.setFilterVolume(1.0);
// to check the current filter volume (filters.volume can be undefined)
console.log(player.filters.volume);
```

> There are other internal changes like

```js
// new variable in LLTurbo class, which handles the "connection data" of discord only
console.log(LLTurbo.connections);
// players are moved from `node.players` to `LLTurbo.players`
console.log(LLTurbo.players);
// getNode() is removed in favor of joinVoiceChannel, you can still get the default least loaded node via `LLTurbo.options.nodeResolver()`
const player = await LLTurbo.joinVoiceChannel({
  guildId: "your_guild_id",
  channelId: "your_channel_id",
  shardId: 0,
});
// you can supply a custom node resolver for your own way of getting an ideal node by supplying the nodeResolver option in LLTurbo options
const LLTurboOptions = {
  ...yourLLTurboOptions,
  nodeResolver: (nodes, connection) => getYourIdealNode(nodes, connection),
};
// and other changes I'm not able to document(?);
```

### LLTurbo's options

| Option                 | Type                   | Default  | Description                                                                                                                                          |
| ---------------------- | ---------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| resume                 | boolean                | false    | Whether to resume a connection on disconnect to Lavalink (Server Side) (Note: DOES NOT RESUME WHEN THE LAVALINK SERVER DIES)                         |
| resumeTimeout          | number                 | 30       | Timeout before resuming a connection **in seconds**                                                                                                  |
| resumeByLibrary        | boolean                | false    | Whether to resume the players by doing it in the library side (Client Side) (Note: TRIES TO RESUME REGARDLESS OF WHAT HAPPENED ON A LAVALINK SERVER) |
| reconnectTries         | number                 | 3        | Number of times to try and reconnect to Lavalink before giving up                                                                                    |
| reconnectInterval      | number                 | 5        | Timeout before trying to reconnect **in seconds**                                                                                                    |
| restTimeout            | number                 | 60       | Time to wait for a response from the Lavalink REST API before giving up **in seconds**                                                               |
| moveOnDisconnect       | boolean                | false    | Whether to move players to a different Lavalink node when a node disconnects                                                                         |
| userAgent              | string                 | (auto)   | User Agent to use when making requests to Lavalink                                                                                                   |
| structures             | Object{rest?, player?} | {}       | Custom structures for LLTurbo to use                                                                                                                 |
| voiceConnectionTimeout | number                 | 15       | Timeout before abort connection **in seconds**                                                                                                       |
| nodeResolver           | function               | function | Custom node resolver if you want to have your own method of getting the ideal node                                                                   |
