# `leaderboard_exetnded_config.json`

This file contain information about extra properties returned for each leadeboard entry.

If the server has `memcached` enabled and running, then these values will be cached for a short period to reduce server load.

The leaderboard can return both "normal" attributes from [`stats.xml`](stats.xml.md) and "custom" attributes from [`custom_player_attributes.xml`](custom_item_attributes.xml.md), the file structure is as follows:

~~~javascript
{
  "custom":["stamina","intelect","agility","strength"],
  "stats":["attack","defence","hit","avoid","profile_points","cash"]
}
~~~
