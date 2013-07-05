# `global_gifts.xml`

This file contains items that can be freely gifted to other players.
(Subject to the gifting limits specified in [`game.conf`](game.conf.md).

Typical file structure looks like this:

~~~xml
<?xml version="1.0"?>
<chameleon_data>
  <global_gifts>
    <gift id="1">...</gift>
    <gift id="2">...</gift>
    <gift id="3">...</gift>
  </global_gifts>
</chameleon_data>
~~~

Here each gift has the structure

~~~xml
<gift id="1"> <!-- unique id key -->

  <!--
    An optional RoarEngine requirement element to able
    to see that the gift can be sent.
  -->
  <visibility>...</visibility>

  <!--
    An optional RoarEngine requirement element to able
    to send the gift.
  -->
  <requirements>...</requirements>

  <!--
    An optional RoarEngine cost element to send the gift.
  -->
  <costs>...</costs>

  <!--
    An optional modifier that gets applied to the sender when
    the gift is sent.
  -->
  <on_give>...</on_give>

  <!--
    A modifier that is applied to the recipient when the
    gift is accepted.
  -->
  <on_accept>...</on_accept>

  <!--
    Any number of tags for client-side sorting and filtering
  -->
  <tag value="a_tag"/>
  <tag value="another_tag"/>

</gift>
~~~

The [requirement](../concepts/requirements.md), [costs](../concepts/costs.md) and [modifiers](../concepts/modifiers.md) are documented elsewhere.
S
