# `named_modifiers.xml`

This file provides a way of grouping [modifiers](../concepts/modifiers.md) so that frequently used groups can referenced and updated easily.

They are also the most direct way to access modifiers from within [lua scripts](../concepts/lua.md). For example:

~~~lua
function reward_me(args)
   player.grant_drop( args.player, "a_named_modifier")
   return 0, nil
end
whitelist("reward_me")
~~~

For example you might have `goblin_killed`, `newt_killed` and `snake_killed` tasks that should all reward similarly. By creating a `level_1_kill` named modifier and using that in the tasks you can encapsulate the required logic for reuse.

For example in [`tasks.xml`](tasks.xml.md) you might have goblins always rewarding from the `level_1_kill` list, but snakes only rewarding 50% of the time.

~~~xml
<task ikey="goblin_killed" >
  <on_complete>
     <named ikey="level_1_kill">
  </on_complete>
</task>
<!--
   Snakes aren't as cool as goblins so you don't get
   stuff as often.
-->
<task ikey="snake_killed">
  <on_complete>
     <random_choice>
       <choice weight="1"></choice>
       <choice weight="1">
         <named ikey="level_1_kill">
       </choice>
     </random_choice>
   </on_complete>
</task>
...
~~~

Now the named modifier can also be arbitrarily complicated and contain any other modifiers including other named modifiers. (Watch out for recursion, which while possible and OK in some cases can cause the server to grind to a halt.)

Here's an example where if you're level 2 or higher you have a 70% chance of getting 8 energy and "some_other_drop", 20% chance of getting a "gold_mine" item and 10% change of nothing. If you're level 1 then you get the "gold_mine" 66.6% of the time and nothing 33.3% of the time.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<chameleon_data>
  <named_modifiers>
    <named_modifier ikey="level_1_kill" >
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
        <!-- AND IF YOURE UNLUCKY YOU GET NOTHING -->
        <choice weight="1"/>
      </random_choice>
    </named_modifier>
    <named_modifier ikey="some_other_drop">
      ...
    </named_modifier>
  </named_modifiers>
</chameleon_data>
~~~
