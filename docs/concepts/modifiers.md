# Modifiers

Roar represents many of the things that can change a players state as *Modifiers*.

There is quite a lot of them...

## Remove Items
The example removes 3 `green_bottle` items from the player. Will remove less if the player does not have enough. Does not error if the player has insufficient items. (If you expect it to error you should probably be using a [cost](costs.md) instead.

~~~xml
<remove_items ikey="green_bottle" count="3" />
~~~

## Grant Stat
The example grants the player 17 `energy`. It will clip to the max (or min if value is negative) if the stat has a limit.

~~~xml
<grant_stat ikey="energy" value="17" />
~~~

## Grant Stat Range
The example grants the player between 17 and 26 `energy` (inclusive). It is otherwise identical to `grant_stat`.

~~~xml
<grant_stat_range ikey="energy" min="17" max="26" />
~~~


## Grant XP
Gives the player XP.

~~~xml
<grant_xp value="10"/>
~~~

## Grant XP Range
Grants the player between 10 and 20 XP (inclusive).

~~~xml
<grant_xp_range min="10" max="20"/>
~~~

## Grant Item
Gives the player a single item

~~~xml
<grant_item ikey="an_item_ikey"/>
~~~

## Random Choice
Applies a random one of the sub `<choice>` nodes `<modifiers>`, weighted by the `<choices>` `weight` attribute. The `<choice>` nodes are filtered before selection using their `<requirement>` node. Best illustrated with an example:

Here if you're level 2 or higher you have a 70% (7/10) chance of getting 8 energy and "some_other_drop", 20% (2/10) chance of getting a "gold_mine" item and 10% (1/10) change of nothing. If you're level 1 then you get the "gold_mine" 66.6% (2/3) of the time and nothing 33.3% (1/3) of the time.

~~~xml
<random_choice>
  <!-- ONLY AVAILABLE IF YOU ARE LEVEL 2 OR HIGHER -->
  <choice weight="7">
    <requirement><level_requirement level="2"/></requirement>
    <modifier>      
     <grant_stat ikey="energy" value="8"/>
     <named ikey="some_other_drop"/>
    </modifier>
  </choice>
  <!-- EVERYONE CAN GET THIS -->
  <choice weight="2">
    <modifier>
      <grant_item ikey="gold_mine"/>
    </modifier>
  </choice>
  <!-- AND IF YOU ARE UNLUCKY YOU GET NOTHING -->
  <choice weight="1"/>
</random_choice>
~~~

## Multiple
All the sub-nodes are applied

~~~xml
<all>
  <grant_item ikey="item_one"/>
  <grant_item ikey="item_two"/>
</all>
~~~

## Nothing
Does nothing to the target.

This can be handy to make behaviour explicit where a
modifier is expected.

~~~xml
<nothing/>
~~~

## If Then Else
Selects between two alternatives based on a [requirement](requirements.md) node.

An example that decreases your health by 10 if you are level 5 or higher, and reduces it by 5 otherwise.

~~~xml
<if_then_else>
   <if>
      <level_requirement level="5"/>
   </if>
   <then>
      <grant_stat ikey="health" value="-10"/>
   </then>
   <else>
      <grant_stat ikey="health" value="-5"/>
   </else>
</if_then_else>
~~~

## Named
Uses the corresponding modifier listed in the [`named_modifiers.xml`](../configuration_files/named_modifiers.xml.md) file.

~~~xml
<named ikey="some_modifier"/>
~~~

## Script
Runs the given script on the user. See [scripting](scripting.md) for further details. To keep the XML bearable it is customary to wrap the nodes content in a CDATA block.

Here is an example that uses the script to increase the players
energy by 5.
~~~xml
<script><![CDATA[
function(args)
  player.increment_stat(args.player,"energy",5)
  return 0, nil
end
]]></script>
~~~

