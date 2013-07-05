## Scripting Overview

Roar Scripting provides a powerful, embedded LUA scripting engine to facilitate arbitrary application logic within your game.

The four primary functional mappings are:

- **Players** - access and modify player attributes, resources, currencies and inventory
- **Tasks** - work with Roar internal tasks
- **Maps** - manage and modify Roar maps
- **Items** - work with Roar items and variables


## A quick how-to: Scripts

All script functionality works by:

1. Defining the **function name** for the logic block, e.g. `function moonwalk( args )`
2. Returning a code and return string, e.g. `return 0, "Worked!"`
    Here a first argument of 0 is used to signify that the call succeeded, and the second argument is the return value. A first argument that is non-zero represents an error, and the second argument is the error message returned to the client.
3. Whitelisting the function for external use e.g. `whitelist("moonwalk")`

Missing *any* of these steps will cause your script to fail (sometimes silently and cryptically).

We force whitelisting of functions so that you can create helper functions that you would not want players calling directly. Players can only call whitelisted functions, but these whitelisted functions are free to call your helper functions.

A typical function would look like this:
*Note: this script assumes you've already created a **moonwalk** attribute or resource or currency. It's just an example. #protip*

~~~lua
function moonwalk( args )
    -- Do some logic (a simple increment stat for example)
    player.increment_stat( args.player, 'moonwalk', 1)

    if( player.get_stat( args.player, 'moonwalk')==2) then
      -- Report an error
      return 10, "Your moonwalk is now 2"
   end

    -- Remember to return a code and return object
    return 0, {
       moonwalk=player.get_stat( args.player, 'moonwalk'),
       mesg='have a nice moonwalk',
       foo={1,2,3} }
end

-- Now explicitly allow external access to this function
whitelist("moonwalk")
~~~

You could now access this function through your API by POSTing the player's `auth_token` along with `script:"moonwalk"` to:

`http://api.roar.io/YOUR_GAME/scripts/run`

If the player calls this when their moonwalk stat is 0 their moonwalk stat will be set to 1 and they will receive XML like this

~~~xml
<roar tick="130271252424">
  <scripts>
    <run status="ok">
      <result>
        <moonwalk>1</moonwalk>
        <mesg>have a nice moonwalk</mesg>
        <foo>
            <value>1</value>
            <value>2</value>
            <value>3</value>
       </foo>
     </result>
   </run>
  </scripts>
</roar>
~~~

If they call it when their moonwalk stat is 1 their moonwalk stat will be set to 2 and they will receive XML like this

~~~xml
<roar tick="130271250528">
  <scripts>
    <run status="error">
      <error type="10">Your moonwalk is now 2</error>
    </run>
  </scripts>
</roar>
~~~


## Passing arguments to scripts
### The implicit player argument
When a script is called there is always a field in the argument passed to the script called player. This object represents the current player.
### Additional arguments
If you wish to pass additional parameters to a script you JSON encode the parameters array, set that to the `args` POST variable.

## Scripted Modifiers and Requirements
Scripts can be used to programmatically define modifiers or requirements used in the Roar framework.

### Scripted Modifiers

A scripted modifier is implemented as an unnamed function with no return value. The scripts only argument is the same `args.player` that is passed to runnable scripts. This example shows how you could implement a modifier that increments a players stats

~~~lua
function(args)
   args.player:increment_stat("gold",10)
end
~~~

### Scripted Requirements

A scripted requirement is expected to return `true` or `false` as its only return value. Here's an example that returns true if a players `gold` is less than his `strength`

~~~lua
function(args)
   return args.player:get_stat("gold")<args.player:get_stat("strength")
end
~~~

### Style
It is considered bad practice to have long scripted requirements or modifiers. Often these are better handled by creating a setup script (in the scripts section of the UI) and defining the long functions in that file. Then have the requirements and modifiers call those functions. The primary advantage of this is that error reporting for the script files is better than for modifiers and requirements. Also testing is easier if the main lua functions are contained in the script files.

For example this could be a scripted requirement

~~~lua
function(args)
   return is_player_rich(args.player)
end
~~~

Then in one of the script files

~~~lua
function is_player_rich( p )
   return p:get_stat("gold")>10 or p:get_stat("gems")>3
end
~~~


# API Reference

<a name="player_functions"></a>
## Players

There are two ways to access players in the Roar scripting environment. The current player is always passed to a script as the `args.player` argument. Additional players can be accessed through the `player.get_player` function, documented below.

### player.get_player
#### description
Returns the player with the specified id (string). It will return null if that player does not exist.
#### example
~~~lua
local p = player.get_player("1212134")
~~~
#### notes

### player:level
#### description
Returns the players level
#### example
~~~lua
local p = args.player
local level = p:level()
~~~

### player:name
#### description
Returns the players name
#### example
~~~lua
local p = args.player
local name = p:name()
~~~

### player:id
#### description
Returns the players id as a string
#### example
~~~lua
local p = args.player
local id = p:id()
~~~

### player:xp
#### description
Returns the players xp
#### example
~~~lua
local p = args.player
local xp = p:xp()
~~~

### player:facebook_uid
#### description
Returns the players facebook user id as a string if known, and "0" if not.
#### example
~~~lua
local p = args.player
local fbuid = p:facebook_uid()
~~~

### player.:get_stat
#### description
This returns the current value of a given stat. It will raise an error if an invalid stat name is passed.
#### example
~~~lua
local p = args.player
v = p:get_stat( "energy" )
~~~
#### notes
 * There is an old version of this that would be called like this:

    ~~~lua
    v = player.get_stat(p,"energy")
    ~~~

<a name="player_set_stat"></a>
### players.set_stat
#### description
This sets the current value of a given stat. It will raise an error if an invalid stat name is passed.
#### example
~~~lua
local p = args.player
p:set_stat( "energy", 10 )
~~~
#### notes
 * This will generate a stat change server notification for the player if the stat is a visible stat.
 * There is an old version of this that would be called like this:

    ~~~lua
    player.set_stat(p,"energy",10)
    ~~~

 * Note that _special_ stats like `xp` and `level` (Those returned by `player/info` with type `special`) need to
   be accessed by other functions. These functions are listed above and are named after their corresponding
   stats, e.g. `p:level()`.


### players.increment_stat
#### description
Increase (or decrease) the players stat by some amount
#### example
~~~lua
local p = args.player
p:increment_stat( "energy", -3 )
~~~
#### notes
 * This will generate a stat change server notification for the player if the stat is a visible stat.
 * There is an old version of this that would be called like this:

    ~~~lua
    player.increment_stat(p,"energy",-3)
    ~~~

### players.get_custom
#### description
Gets the given custom property of the player as a string.
#### example
~~~lua
local p = args.player
local s = p:get_custom( "a_property" )
~~~
#### notes
 * There is an old version of this that would be called like this:

    ~~~lua
    player.get_custom(p,"a_property")
    ~~~

### players.set_custom
#### description
Sets the given custom property of the player to a given string.
#### example
~~~lua
local p = args.player
p:set_custom( "a_property", "some value" )
~~~
#### notes
 * There is an old version of this that would be called like this:

    ~~~
    player.set_custom(p,"a_property","some value")
    ~~~

### players.grant_xp
#### description
Award some amount of XP to the player
#### example
~~~lua
player.grant_xp( args.plater , 10 )
~~~
#### notes
 * This function lives in the `players` module, rather than within the player object directly.
 * This will soon become `v = args.player:grant_xp("energy",-3)`
 * This will generate a stat change server notification for the player if the stat is a visible stat.

## Friend functions
### p:friends()
#### description
Gets a list of friends info. The entries returned contain only the `id`, `name` and `level` fields, if you want more details you can load the friend using `player.get_player( friend_id )`.
#### example
~~~lua
local friends = p:friends()
if(#friends > 0 )
  local  f = players.get_player( friends[1].id )
  .. do something with f ..
end
~~~
#### notes
This function is also available as `player.get_friends( p )`.

## Time Functions
###  players.now
#### description
current time in seconds
#### example
~~~lua
player.now(args.player)
~~~
#### notes
 * This function lives in the `player` module, rather than within the player object directly.
 * This will soon become `v = args.player:now()`


<a name="modifier_functions"></a>
## Modifier Functions
### player.grant_drop( p,  reward_group )
#### description
Apply the given *reward group* to the player
#### example
Assuming there is a reward group called `my_modifier`

~~~lua
player.grant_drop( args.player, "my_modifier" )
~~~

#### notes
 * This function lives in the `player` module, rather than within the player object directly.
 * This will soon become `v = args.player:apply_modifier("my_modifier")`

<a name="script_object_functions"></a>
## Persistent Script Object functions

### player.get_script_object
#### description
Loads the players script object associated with the given string key. It returns `nil` if the object
is not found.
#### example
~~~lua
obj = player.get_script_object( args.player, "an_object" )
~~~
#### notes
 * There are limitations on what can be saved into a script object: It must be a composite of
   * strings
   * numbers
   * proper arrays
   * proper dictionaries

   This is because the intermediate storage format is JSON based, and thus unable to support
   luas hybrid dictionary-array types directly. Similarly storing a player object, map object or
   other non-native lua types is not supported. Finally storing lua functions is not supported.
 * This is likely to be provided as `obj = args.player:get_script_object("an_object")` in future

### player.set_script_object
#### description
Save a lua object away for later use for the given player, using the given key
#### example
~~~lua
player.set_script_object( args.player, "an_object", {1,2,3} )
~~~
#### notes
 * The notes for `player.get_script_object` are equally relevant here
 * This is likely to be provided as `args.player:set_script_object("an_object",{1,2,3})` in the future

## Examples

Grab our example script file showing demo player functions: **
[player.lua](http://s3.amazonaws.com/entp-tender-production/assets/952f524604b293ed058220825aad860c72cff393/player.lua)
**

<a name="task_functions"></a>
## Tasks/Jobs

Grab our example script file showing demo task functions: **
[tasks.lua](http://s3.amazonaws.com/entp-tender-production/assets/8ebb24da318dd5f0e31001d93aa99f614aa98b6f/tasks.lua)
**

### player:get_job_completion_count
#### description
Returns how many times the player has completed the given task
#### example
~~~lua
v =args.player:get_job_completion_count( "a_task_ikey" )
~~~
#### notes

### player:set_job_completion_count
#### description
Set the players job completion count for a specific task/job, overriding the actual completion count.
#### example
~~~lua
args.player:set_job_completion_count( "a_task_ikey", 5)
~~~
#### notes

## Maps

Go crazy with our example script file showing demo map functions:

- **[setupmaps.lua](http://s3.amazonaws.com/entp-tender-production/assets/5b986a266248281066cc1c1e174303b31bc6c0f0/setupmaps.lua)**

### p:map_create(  ikey, nx, ny, bits )
#### description
Create a new map for the given ikey for the given player.
Map will be `nx` x `ny` in dimension, using a bit_depth per cell of `bits`
Here `bits` must be one of:
 * `maps.eight_bit`  [map allows values 0-255]
 * `maps.one_bit`    [map allows only values of 0 or 1]

Call returns a map object to be used in subsequent calls.

#### example
~~~lua
-- create a 8bit 5x10 map
m = p:map_create(  "new_map", 5, 10, maps.eight_bit )
~~~

#### note
 * also callable as `maps.create(  player, ikey, nx, ny, bits )`

### p:map_find
#### description
Returns a previously created map, or nil if not found.
#### example
~~~lua
m = args.player:map_find( "a_map" )
~~~
#### notes
 *  also callable as `player.maps.find( player, ikey  )`

### map:get
#### description
Returns the value of a cell in a map
#### example
~~~lua
m = p:map_find("a_map")
if( m==nil ) then ... end
v =m:get(3,4) -- Get the value at (3,4) in the given map
~~~

### map:set
#### description
Set the value of a cell in a map
#### example
~~~lua
m = p:map_find("a_map")
m:set(3,4,7) -- set the value at (3,4) to 7
~~~

### map:dimension
#### description
Get the dimensions of a map
#### example
~~~lua
m = p:map_find("a_map")
nx,ny = m:dimension()
~~~

### map:max_value
#### description
Returns the maximum value that can be stored in a maps cells.
This will be 1 if the map was created with `maps.one_bit` and 255 if
the map was created with `maps.eight_bit`.
#### example
~~~lua
m = p:find_map("a_map")
v = m:max_value()
~~~

### map:resize
#### description
Changes the size of a map, preserving data when expanding and filling new cells with zero.
#### example
~~~lua
m = p:find_map("a_map")
m:resize(10,10)
~~~

<a name="item-variables"></a>
## Items and Item Variables
Item variables are string or 64 bit floating point values of two types.
 - _archetype variables_  which are read-only variables shared between all items of that type (ikey).
 - _instance variables_ which are read-write variables set on a per-item-instance basis.
Each item type can have arbitrarily many of each of these types of variables.

### player:items
#### description
Gets a list of items owned by the player. These can be filtered by tags or ikey.
#### example
Return all of the players items:

~~~lua
local items = p:items()
~~~

Return only item tagged with weapon:

~~~lua
local weapons = p:items({tag="weapon"})
~~~

Return only lemon items:

~~~lua
local lemons = p:items({ikey="lemon"})
~~~


### player:get_item
#### description
Gets an item from a player given an item id (as a _string_ ). You can then use the item related functions on that item.
Returns `nil` and an error message if the item is not found.
#### example
~~~lua
i, mesg = p:get_item("1234")
if( i==nil) then print(mesg) end
~~~
<a name="grant_item"></a>
### player:grant_item
#### description
Creates a new item and gives it to the player. Errors on failure.
#### example
~~~lua
local p = args.player
local item =  p:grant_item("an_item_ikey")
~~~

### player:remove_item
#### description
Removes an item from a player.
#### example
~~~lua
item, mesg = player:get_item(item_id)
if( item==nil ) then print("error: "..mesg) end
player:remove_item(item)
~~~

### player:equip
#### description
Equips an item. Generates an error on failure.
#### example
~~~lua
function equip_an_item( args )
   local p = args.player
   local item = p:get_item(args.item_id)
   p:equip(item)
   return 0, nil
end
~~~

### player:unequip
#### description
Unequips an item. Generates an error on failure.
#### example
~~~
function unequip_an_item( args )
   local p = args.player
   local item = p:get_item(args.item_id)
   p:unequip(item)
   return 0, nil
end
~~~

### item:id
#### description
Returns the internal id of the item as a string.
#### example
~~~lua
local item = p:grant_item( "an_item_ikey" )
local item_id = item:id()
~~~

### item:ikey
#### description
Returns the ikey of the item as a string
#### example
~~~lua
local item = p:get_item( item_id )
local ikey = item:ikey()
~~~

### item:is_equipped
#### description
Returns true is the item is equipped, otherwise false
#### example
~~~lua
function is_equipped( args )
  local p = args.player
  local item = p:get_item(args.item_id)
  return 0, { item:is_equipped() }
end
~~~


### item:has_property
Returns true if the item has an item variable with the given name. Otherwise returns false.
#### example
~~~lua
if( i:has_property("my_item_variable") ) then ... end
~~~

### item:get_property
#### description
Gets the value of an item variable associated with a particular item. Returns `nil` if the item does not have that variable.
#### example
~~~lua
v = i:get_property("my_item_variable")
~~~

### item:set_property
#### description
Sets the value of an item variable. Will raise an error if the item does not have an instance variable of the required name, or if the value is not of the appropriate type.
#### example
~~~lua
i:set_property("my_item_variable", 1.45)
i:set_property("my_str_item_variable", "hello")
~~~

### players.count_item
#### description
Returns the count of items with the given ikey owned by the current player.
#### example
~~~lua
player.count_item( args.player, "my_item" )
~~~
#### notes
 * This function lives in the `player` module, rather than within the player object directly.
 * This will soon become `v = args.player:count_item("my_item")`

<a name="complete_example"></a>
## More complete example
This is an example script that just returns what you passed to it, and logs that information into the
script log.

~~~lua
-- Helper function that prints a table into a string
function deep_to_string( what )
   if ( type(what)=="table" ) then
      local s = "{"
      for k,v in pairs(what) do
         s = s .. deep_to_string(k) .. ":" .. deep_to_string(v) .. ", "
      end
      s = s .. "}"
      return s
   end
   return tostring(what)
end

-- Helper that copies the arg array without the "special" player object
function dupe_without_player( args )
  local cpy = {}
  for k,v in pairs(args) do
    if( k ~= "player" ) then
      cpy[k]=v
    end
  end
  return cpy
end

-- Function that just returns the array you passed in
function echo( args )
  local arg_cpy = dupe_without_player(args)
  print( deep_to_string( arg_cpy ) )
  return 0, arg_cpy
end
whitelist("echo")
~~~

Here's how you can test this script from python, using the args `{"v":456,"w":[1,2,3,4]}`

~~~python
import simplejson
import httplib
import sys
import urllib
import socket

conn = httplib.HTTPConnection('api.roar.io')
headers = {"Content-type": "application/x-www-form-urlencoded"}
url =  "/GAMENAME/scripts/run/"
auth_token = "123456789"

try:
  script_args = simplejson.dumps({"v":456,"w":[1,2,3,4]})
  params = urllib.urlencode({"auth_token":auth_token, "script":"echo", "args":script_args })
  conn.request("POST", url, params, headers)
except socket.error, e:
  print >> sys.stderr, "SOCKET ERROR : " + repr(e) +"\n" + "maybe the server isn't running?"

response = conn.getresponse()
if( response.status != 200 ):
     print >> sys.stderr, "BAD HTTP RESPONSE : \n  status = %d\n  reason = %s\n\n %s" % ( response.status, response.reason, response.read() )
  sys.exit(1)

data = response.read()
print >> sys.stderr, data
~~~

And here's the response:

~~~xml
<roar tick="130862166389">
  <scripts>
    <run status="ok">
      <result v="456">
<w>
<value>1</value>
<value>2</value>
<value>3</value>
<value>4</value>
</w>
</result>
    </run>
  </scripts>
  <server/>
</roar>
~~~

## References

Handy LUA scripting references include:

- [http://www.luafaq.org](http://www.luafaq.org/)
