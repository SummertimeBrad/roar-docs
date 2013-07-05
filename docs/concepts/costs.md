# Costs

A *Cost* is a condition that a player must satisfy, bundled with an associated change to the player. They can be thought of as a combination of a [requirement](requirements.md) and a [modifier](modifiers.md).

## Item Cost
This requires that a player has at least a given number of the specified item and then removes them from the player.

~~~xml
<item_cost ikey="blue_bottle" number_required="4"/>
~~~

## Stat Cost
This requires that a player has at least a given stat value, and subtracts the given amount from the stat.

~~~xml
<stat_cost ikey="cash" value="10"/>
~~~

## All
Applies all the contained costs

~~~xml
<all>
  <item_cost ikey="blue_bottle" number_required="4"/>
  <stat_cost ikey="cash" value="10"/>
  <stat_cost ikey="tofu_balls" value="1"/>
</all>
~~~
