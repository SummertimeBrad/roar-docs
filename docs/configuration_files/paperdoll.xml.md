# `paperdoll.xml`

This file controls how many of each item *type* can be equipped at one time.
Here's a simple example:

~~~xml
<chameleon_data>
<!--
  `default_count` is the amount for ikeys not in this list,
  set to zero if you want unlisted types to be unequipable.
-->
<paperdoll default_count="1">
  <!--
    `ikey` is the item "type" and `count` is the maximum equipable
    of that type.
  -->
  <slot ikey="item" label="Item" count="0" />
  <slot ikey="ring" label="Ring" count="2" />
  <slot ikey="token" label="token" count="0" />
</paperdoll>
</chameleon_data>
~~~
