# Game.conf

This JSON file is the root RoarEngine server config file. It contains all the information about your server.
It tells the server where to find other config files, and sets up many of the service passwords your
server needs access to.

### debug (boolean)
If set to true then the server will output every call it receives to STDOUT. This is typically a lot of data,
much of which is available in the firehose logs. Only enable it if you want to see the low-level info about the
communications occurring on the server.

### port & fastjson_port (number)
These are the two ports that the server will listen for connections from the web layer on.
These must match the values specified in the `serverconfig.php` file.

## Player Cache Parameters
These parameters control how many players are stored in memory and how often some of them are written out to
the database. These are the primary performance tuning parameters for the system. Your entire
"live" player base will be written to the database every `(cachesize/dbsyncchunksize)*dbsync` seconds. For least data loss
on server failure, you want a low `dbsync`, large `dbsyncchunksize` and a small `cachesize`. However for optimal performance
you want the opposite. Finding the right balance will depend on your game details.

### dbsync (integer)
How frequently (in seconds) to write down some of the users to the database.

### dbsyncchunksize (integer)
How many of the users to write to the database down each write cycle.

### cachesize (integer)
How many users to keep in server memory at any given time.

## Config file locations
These parameters tell the server where to look for other configuration files.
The paths can be absolute paths, or relative to the `game.conf` file location.

### tasks
Configuration file for [tasks](tasks.xml.md).

### items
Configuration file for [items](item_archetypes.xml.md).

### shops
Configuration file for [shops](shops.xml.md).

### facebookshop
Configuration file for [facebook shop](facebook_shop.xml.md).

### chrome_webstore
Configuration file for [google chrome webstore](chrome_webstore.xml.md).

### appstore
Configuration file for [iOS App Store](appstore.xml.md).

### custom_item_attributes
Configuration for [custom item attributes](custom_item_attributes.xml.md).

This has been replaced with the per item custom *variables*, which are configured in [item_archetypes.xml](item_archetypes.xml.md).
However the file must still exit, and be properly formatted.

### custom_player_attributes
Configuration for [custom player attributes](custom_player_attributes.xml.md).

This file configures the read/write text attributes that are available on the player.

### paperdoll
Configuration for [item equip counts](paperdoll.xml.md).

### admin_token
[Config file](admin_token.xml.md) containing the `admin_token` used for all admin related calls.

### level_brackets
[Configuration](level_brackets.xml.md) for the `xp` levels required to reach each of the `levels` in the game.
If you don't want levels in your game a single entry in this file will suffice.

### stats
The [stats](stats.xml.md) that all players have with associated max/min values, regen and per-level gains.

### misc_settings
[Holder file](misc.xml.md) for miscellaneous settings.
Currently only contains a single flag for whether xp is tracked in leaderboards.

### achievements
[Achievements](achievements.xml.md) tracked by the system. The tasks system is generally more flexible than this
system, but this can still be useful in some cases.

### scripts
GLOB specifying where to find the [script files](scripts.md), this is usually `data/scripts/*.lua`. This is relative to the config file location.

### named_modifiers
[Named modifiers](named_modifiers.xml.md) provide a higher level way of working with RoarEngines [modifiers](../concepts/modifiers.md).

## Leaderboard
The leaderboard system is a separate server that tracks players stat changes and maintains rankings for those players.
For the server to store the players stats it needs to know where to find the leaderboard server.


    "leaderboard": {
      "app_id":"4321",
      "user_id":"777",
      "auth_token":"1234",
      "base_url":"http://localhost/server/leaderboards"
    }


### leaderboard.base_url
The URL that the leaderboard server can be reached on, from the server.
If you are running the leaderboard on the same machine as the roarengine server then this will typically be
something like `http://localhost/roar/leaderboards`.

### leaderboard.app_id
Leaderboard servers can be shared between multiple games. The `app_id` identifier used to determine
which game specific stats relate to.

### leaderboard.user_id & leaderboard.auth_token
Used to identify the leaderboard user, to ensure they are allowed to access the game permissions.

## Gifting
These variables are used to limit how many gifts a player can give, and how often they can give them.
The fields are optional, and if unspecified count fields default to `unlimited` and reset fields default to `8640000` (one day).
Reset periods are measured in 100ths of a second.

    "gift_limits": {
       "single":7,
       "single_reset":8640000,
       "multiple":10,
       "multiple_reset":8640000
    },

### gift_limits.single & gift_limits.single_reset
How many gifts can I give you per reset period. In the example above I can give you 7 gifts every day.

### gift_limits.multiple & gift_limits.multiple_reset
How many gifts can I give out per period total. In the example above I can give out 10 gifts every day.
This means I can, for example, give 7 to you, and 3 to someone else.


## Database config
The RoarEngine server uses one core database, and one optional lua accessible database. These are both Postgres databases.
(Note that web layer logging and leaderboards may use other databases, and these are usually MySQL database,
and are not configured here).

### postgres
This is the Postgres connection string required to connect to the core database.

    "postgres": "host=localhost port=5432 dbname=RoarTest user=roar password=awesomesauce"

### lua_db.postgres
This is the Postgres connection string required to connect to the lua database if used.
This field is optional, but if not provided the low level lua database access features will not be available.

    "lua_db":{
      "postgres":"host=localhost port=5432 dbname=luadb user=roarlua password=awesomesaucier"
    }
