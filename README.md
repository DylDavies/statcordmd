<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [Statcord][1]
    -   [post][2]
    -   [autopost][3]
    -   [postCommand][4]
        -   [Parameters][5]
-   [ShardingClient][6]
    -   [postCommand][7]
        -   [Parameters][8]
    -   [post][9]
        -   [Parameters][10]
-   [Examples][15]
    -   [Normal Usage][16]
    -   [Sharding Usage][17]

## Statcord

### post

Manual posting

Returns **[Promise][11]&lt;([boolean][12] \| [Error][13])>** returns false if there was no error, returns an error if there was.

### autopost

Auto posting

Returns **[Promise][11]&lt;([boolean][12] \| [Error][13])>** returns false if there was no error, returns an error if there was. Only on the first run, otherwise the rest will be ignored

### postCommand

Post stats about a command

#### Parameters

-   `command_name` **[string][14]** The name of the command that was run
-   `author_id` **[string][14]** The id of the user that ran the command

## ShardingClient

> **NB**: The ShardingClient always has autopost enabled

### postCommand

Post stats about a command

#### Parameters

-   `command_name` **[string][14]** The name of the command that was run
-   `author_id` **[string][14]** The id of the user that ran the command
-   `client` **any** The discord client this command is being posted for

### post

Post all current stats to statcord

#### Parameters

-   `client` **any** The discord client this command is being posted for

# Examples

## Normal Usage

```javascript
const Statcord = require("statcord.js");
const Discord = require("discord.js");

const client = new Discord.Client();
// Create statcord client
const statcord = new Statcord.Client("statcord.com-APIKEY", client);

// Client prefix
const prefix = "cs!";

client.on("ready", async () => {
    console.log("ready");

    // Start auto posting
    let initalPost = await statcord.autopost();

    // If there is an error, console.error and exit
    if (initalPost) {
        console.error(initalPost);
        process.exit();
    }
});


client.on("message", async (message) => {
    if (message.author.bot) return;
    if (message.channel.type !== "text") return;

    if (!message.content.startsWith(prefix)) return;

    let command = message.content.split(" ")[0].toLowerCase().substr(prefix.length);

    // Post command
    statcord.postCommand(command, message.author.id);

    if (command == "say") {
        message.channel.send("say");
    } else if (command == "help") {
        message.channel.send("help");
    } else if (command == "post") {
        // Only owner runs this command
        if (message.author.id !== "bot_owner_id") return;

        // Example of manual posting
        let postError = await statcord.post();

        // If there is a post error notify command runner
        if (postError) {
            message.channel.send(postError.message);
        }
    }
})

client.login("TOKEN");
```

## Sharding Usage

#### **`sharder.js`**
```javascript
    const Discord = require("discord.js");
    const Statcord = require("statcord.js");

    const manager = new Discord.ShardingManager('./bot.js', { token: "TOKEN"});
    // Create statcord sharding client
    const statcord = new Statcord.ShardingClient("statcord.com-APIKEY", manager);

    // Spawn shards, statcord works with both auto and a set amount of shards
    manager.spawn();

    // Normal shardCreate event
    manager.on("shardCreate", (shard) => {
        console.log(`Spawned shard ${shard.id}`);
    });
```

#### **`bot.js`**
```javascript
    const Discord = require("discord.js");
    const Statcord = require("statcord.js");

    const client = new Discord.Client();
    /* There is no need to create a statcord client in the bot script,
    because it has already been made in the sharding script
    */

   // Client prefix
   const prefix = "cs!";

    client.on("ready", async () => {
        console.log("ready");
    });

    client.on("message", async (message) => {
        if (message.author.bot) return;
        if (message.channel.type !== "text") return;

        if (!message.content.startsWith(prefix)) return;

        let command = message.content.split(" ")[0].toLowerCase().substr(prefix.length);

        // Post command
        Statcord.ShardingClient.postCommand(command, message.author.id, client);

        if (command == "say") {
            message.channel.send("say");
        } else if (command == "help") {
            message.channel.send("help");
        } else if (command == "post") {
            // Only owner runs this command
            if (message.author.id !== "bot_owner_id") return;

            // Example of manual posting
            Statcord.ShardingClient.post(client);

            // Errors on the sharding client will be sent to the console straight away
        }
    });

    client.login("TOKEN");
```

[1]: #statcord

[2]: #post

[3]: #autopost

[4]: #postcommand

[5]: #parameters

[6]: #shardingclient

[7]: #postcommand-1

[8]: #parameters-1

[9]: #post-1

[10]: #parameters-2

[11]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise

[12]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean

[13]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error

[14]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String

[15]: #examples

[16]: #normal-usage

[17]: #sharding-usage
