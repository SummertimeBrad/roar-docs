# `stats.xml`

Stats in RoarEngine are divided into 3 categories, but they differ only very slightly in real behaviour. However conceptually they are quite different.

## resources

Resources are stats that go up and down frequently while the player plays. They often have a max value and a min value of 0. They often have regeneration. Think "health" or "mana".

## attributes

Attributes are stats that are typically effected by player level and
equipment. They're often unbounded and without regeneration. Think "strength".

## currencies

Currencies are stats that are spent or earned. The usually have a minimum of 0, and no maximum. Often they have no regeneration.

## Stat attributes
These attributes are shared between attributes, currencies and resources.

### `id` (integer < 16000 )
A unique numeric identifier for the stat. This is really only used for
internal tracking of stats.
### `ikey` (string)
A unique string identifying the stat. Anything which refers to a stat uses this `ikey` rather than the `id`.
### `label`
A short description of the stat.
### `regen_amount` (string) & `regen_period` (integer) (optional)
These control which `stat` determines the amount of regeneration for the
stat and how often (in 100ths of a second) to apply that regeneration.

For example if you wanted a player to gain 10 health every 10 minutes (10*60*100 = 60000 ticks).

~~~xml
<attribute
  ikey="health"
  regen_period="60000"
  regen_amount="health_regen"
  ...
  />
<attribute
  ikey="health_regen"
  start_value="10"
  ...
  />
~~~

 Note that this is different to 1 health every 1 minute, as you receive all the regenerated value in one chunk.

### `per_level` (integer)
How much of that stat do you gain on level up.
### `max` (integer or string, optional)
Is there a cap on the total of the stat that you can have. For example maybe you want to limit your player to having at most 100 cash.

~~~xml
<attribute
  ikey="cash"
  max="100"
  ...
  />
~~~

If you want to let the player upgrade their cash carrying potential you
can use something like this:

~~~xml
<attribute
  ikey="cash"
  max="cash_max"
  ...
  />
<attribute
  ikey="cash_max"
  ...
  />
~~~

Here `cash_max` can be changed by any of the usual manners.

### `min` (integer, optional)

Same as `max` but for the minimum allowable value for a stat.
*Currencies* default a minimum of 0.

### `tracked` (boolean, optional)

Whether or not to track the stat on the leaderboards. For performance reasons you should only track the values that you really want to see.

*Currencies* default this to true, all other stats default to false.

### `visible` (boolean, optional)

Whether the stat should show up in the players stats when listed. If set to false the current value of the stat is not available to the player. However it remains modifiable and accessible to lua scripting. This can be useful for hiding state from the player.




