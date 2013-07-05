# `item_archetypes.xml`
Item archetypes are the blue-print from which all item instances owned by players are derived.  They are probably one of the more complicated entities in RoarEngine. The `item_archetype.xml` file is simply a list of all the item archetypes available within your game.

A typical example of the file looks like this:

~~~
<?xml version="1.0"?>
<chameleon_data>
  <items>
    <item ... >...</item>
    <item ... >...</item>
    <item ... >...</item>
    <item ... >...</item>
  </items>
</chameleon_data>
~~~

## The `item` element

### Core attributes

#### `ikey` (string)
A unique identifier for this item type.

#### `label` (string)
A short description of the item.

#### `description` (string)
A longer description of the item.

#### `type` (string)
The type references the types defined in [paperdoll](paperdoll.xml.md) and control how many of the given item can be equipped. For example you may be able to equip 2 *ring* type objects, but only 1 *helm* type object, and not equip anything of type *statue*.

#### `consumable` (boolean)
This flag is required, but the engine does not use it for anything.

#### `sellable` (boolean, optional, default=true)
Whether the item can be sold back to the shop. An item with this set to false can be difficult for a user to get rid of. If it is set to true, but with an empty or missing `<price>` element, then the item will be sold to the shop for nothing - destroying the item, but not rewarding the player with anything.

### Elements

#### `<tag value="xyz">`
These are tags that can be used by the client for filtering and grouping items.

#### `<price...>`
Contains `<grant_stat>` nodes that apply to the player when the item is sold back to the shop.
These simply identify a stat from [stats.xml](stats.xml.md) and a value.

~~~
<price>
  <grant_stat ikey="cash" value="100" />
  <grant_stat ikey="health" value="5" />
</price>
~~~

#### `<variable...>`
These represent custom variables that occur on items. They are accessible through scripting. The `<variable>` elements have the following attributes:

* `store` : Item variables come in two kinds (determined by the `store` attribute), *instance* and *archetype*. Archetype variables are the same for all instances of that item. Instance variables vary between item instances.

* `value` : The default value of the variable. Instance variables need not provide a default value, but this attribute is mandatory for archetype variables.

* `ikey` : A unique key for the attribute. (Need only be unique within this item - two different items can use the same ikey. "Hospitals" and "Police Stations" can both have a `width` variable.

* `type` : must be `"double"` or `"string"`, and represents how the value is stored, and how it is made available in the lua scripts.

For example in a building game where items are used to represent the individual buildings all "Hospitals" may have a `width` of 100 and a `height` of 50, however each Hospital may have a different `x_position` and `y_position`. This could be represented as follows:

~~~
<variable store="archetype" ikey="width" type="double" value="50" />
<variable store="archetype" ikey="height" type="double" value="100" />
<variable store="instance" ikey="x_position" type="double" value="0" />
<variable store="instance" ikey="y_position" type="double" value="0" />
~~~

### Equipment Modifier Elements
#### `<equip_stat ...>`
An item with an `equip_stat` modifier will change the players stats by the amount specified when the item is equipped. It has the following attributes:

* `id` (integer) : a unique (within this item) id for tracking the stat
* `stat` (string) : the key of the users stat to modify, as specified in [stats.xml](stats.xml.md).
* `value` (integer) : how much to change the value by. (can be negative).

An example might be a helm which grants +3 armour.

#### `<grant_stat ...>`
When the item is "used" (and destroyed) the user will gain the associated stat. These changes will not occur on equipping the item. It has the following attributes:

* `id` (integer) : a unique (within this item) id for tracking the stat
* `stat` (string) : the key of the users stat to modify, as specified in [stats.xml](stats.xml.md).
* `value` (integer) : how much to change the value by. (can be negative).

An example might be a health pill that grants +100 health.

#### `<regen_stat ...>`
When the item is equipped the player will gain the stat periodically. It has the following attributes:

* `id` (integer) : a unique (within this item) id for tracking the stat
* `stat` (string) : the key of the users stat to modify, as specified in [stats.xml](stats.xml.md).
* `value` (integer) : how much to change the value by. (can be negative).
* `every` (integer) : how many ticks between regens. (100 ticks = 1 second).

An example might be a gold mine that generates +10 gold every hour.

#### `<regen_stat_limited ...>`
When the item is equipped the player will gain the stat periodically, but you will only gain it a set number of times.

* `id` (integer) : a unique (within this item) id for tracking the stat
* `stat` (string) : the key of the users stat to modify, as specified in [stats.xml](stats.xml.md).
* `value` (integer) : how much to change the value by. (can be negative).
* `every` (integer) : how many ticks between regens. (100 ticks = 1 second).
* `repeat` (integer) : how many times can it be gained.

An example might be the gold min that generates +10 gold per hour for 10 hours (repeat=10), or
a term deposit that gives you +100 cash when it completes (repeat=1).

#### `<collect_stat ...>`
A collect stat is similar to a regen, except that the player must explicitly use the item to gain the
stat and reset the collect timer.

* `id` (integer) : a unique (within this item) id for tracking the stat
* `stat` (string) : the key of the users stat to modify, as specified in [stats.xml](stats.xml.md).
* `value` (integer) : how much to change the value by. (can be negative).
* `every` (integer) : how many ticks between regens. (100 ticks = 1 second).
* `window` (integer) : how long do you have to collect after it completes. ( 0 means unlimited ).

An example might be a farm plot that takes 3 hours to grow, and can then be collected for 1 hour after that. Collecting replants the plot.

