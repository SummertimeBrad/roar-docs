# Requirements

A RoarEngine requirement is just a yes/no question that can be applied to a player. They're used to construct requirements for tasks and shops, and can be combined with other costs, modifiers and requirements for extra power.

# Level Requirement
This is true only if the player is at least a given level.

~~~xml
<level_requirement level="3"/>;
~~~

# Item Requirement
True if the player has at least the required number of items.


~~~xml
<item_requirement ikey="blue_bottle" number_requires="7" />
~~~

# Stat Requirement

~~~xml
<stat_requirement ikey="strength" value="10" />
~~~

# And Requirement
True if _all_ the sub requirements are true.

~~~xml
<and>
  <level_requirement level="3" />
  <stat_requirement ikey="strength" value="10" />
</and>
~~~

# Script Requirement
Runs a [lua script](scripting.md) on the player. This should have no side-effects, should be consistent and not modify the player, as it may be executed multiple times. The element can be either `<script>` or `<script_requirement>`.

In the interest of keeping the XML clean, it is recommended that you wrap the script body into a CDATA block.

Here's an example script requirement that will return true if the players name is John.

~~~xml
<script_requirement><![CDATA[
function(args)
  if args.player:name()=="John" then
    return 0, true
  else
    return 0, false
  end
end
]]></script_requirement>
~~~

A more interesting example might be a condition that is true if the players position in a map has a specific value.

~~~xml
<script><![CDATA[
function(args)
  local m = args.player:map_find("location")
  if m==nil then return 1, "No Map" -- non-zero indicates an error.
  local x = args.player:get_stat("x")
  local y = args.player:get_stat("y")
  return 0,  m:get(x,y)==3
end
]]></script>
~~~

# True Requirement
Always returns true.

~~~xml
<true />
~~~

# False Requirement
Always returns false.

~~~xml
<false />
~~~

# Friends Requirement
True if the player has at least the given number of in-game friends. This kind of mechanic is commonly used to drive the viral loop for social games.

~~~xml
<friends_requirement required="7" />
~~~
