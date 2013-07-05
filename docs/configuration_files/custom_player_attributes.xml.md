# `custom_player_attributes.xml`

This file configures the read/write text attributes that are available on the player.

Note that these values can be read and written to directly by the client, and so should not be used for storing sensitive or game critical data. However they are suitable for non-game critical flavour data.

A typical file might look like

~~~
<chameleon_data>
  <player_attributes>
    <attributes ikey="player_bio" default="Greatest warrior of all time" />
    <attributes ikey="fave_colour" default="blue" />
    <attributes ikey="faction" default="Rage Runner" />
  </player_attributes>
</chameleon_data>
~~~

## `attributes` nodes
Each custom attribute is represented by a single `attributes` node.

### `ikey` attribute
Unique identifier for this attribute.

### `default` attribute
All new players will be created with their value set to this value.
