## LLTurbo

> A stable and updated wrapper around Lavalink, based on Shoukaku client by @Deivu.

![Github Stars](https://img.shields.io/github/stars/Fyphen1223/LLTurbo?style=flat-square)
![GitHub issues](https://img.shields.io/github/issues-raw/Fyphen1223/LLTurbo?style=flat-square)

### Features

✅ Stable

✅ Documented

✅ Updated

✅ Extendable

✅ ESM & CommonJS supported

✅ Very cold-hearted (Very Important)

### Installation

> `npm install https://github.com/Fyphen1223/LLTurbo`

### Small code snippet examples

> Initializing the library (Using Connector Discord.JS)

```js
const { Client } = require("discord.js");
const { LLTurbo, Connectors } = require("LLTurbo");
const Nodes = [
  {
    name: "Localhost",
    url: "localhost:6969",
    auth: "youshallnotpass",
  },
];
const client = new Client();
const LLTurbo = new LLTurbo(new Connectors.DiscordJS(client), Nodes);
LLTurbo.on("error", (_, error) => console.error(error));
client.login("token");
client.LLTurbo = LLTurbo;
```

> Join a voice channel, search for a track, play the track, then disconnect after 30 seconds

```js
const player = await LLTurbo.joinVoiceChannel({
  guildId: "your_guild_id",
  channelId: "your_channel_id",
  shardId: 0,
});
const result = await player.node.rest.resolve("scsearch:snowhalation");
if (!result?.tracks.length) return;
const metadata = result.tracks.shift();
await player.playTrack({ track: metadata.encoded });
```

```js
await player.playTrack({ track: metadata.encoded });
await player.setGlobalVolume(50);
```

### Updating from V3 -> V4 (notable changes)

> The way of joining and leaving voice channels is now different

```js
const { Client } = require("discord.js");
const { LLTurbo, Connectors } = require("LLTurbo");
const Nodes = [
  {
    name: "Localhost",
    url: "localhost:2333",
    auth: "youshallnotpass",
  },
];
const client = new Client();
const LLTurbo = new LLTurbo(new Connectors.DiscordJS(client), Nodes);
LLTurbo.on("error", (_, error) => console.error(error));
client.login("token");
client.once("ready", async () => {
  const node = LLTurbo.options.nodeResolver(LLTurbo.nodes);
  const result = await node.rest.resolve("scsearch:snowhalation");
  if (!result?.tracks.length) return;
  const metadata = result.tracks.shift();
  const player = await LLTurbo.joinVoiceChannel({
    guildId: "your_guild_id",
    channelId: "your_channel_id",
    shardId: 0,
  });
  const result_2 = await player.node.rest.resolve("scsearch:snowhalation");
  console.log(result_2.tracks.shift());
  await player.playTrack({ track: metadata.encoded });
});
```

> Usual player methods now return promises

```js
await player.playTrack(...data);
await player.stopTrack();
```

> There are 2 kinds of volumes you can set, global and filter. I recommend using setGlobalVolume().

```js
await player.setGlobalVolume(100);
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

# Copyright

For this repository, any copyright will be with Deivu (Saya) of Shoukaku client.

```
MIT License

Copyright (c) 2023 Deivu (Saya)

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
