# `shops.xml`

Shops are a surprisingly general feature that can be used to represent in game shops, as well as having a variety of other applications. Technically they are equivalent to [tasks](tasks.xml.md), but without completion count tracking. Thus they can be used to implement "crafting" or even "quests".

Rather than thinking of shops as providing a way to buy "items", it is best to think of them as a way of applying modifiers to players, subject to requirements and costs.

Here is a simple example `shop.xml` file:

~~~xml
<?xml version='1.0' encoding='UTF-8'?>
<chameleon_data>
  <shops>
    <shop ikey="shop_1" label="Shop 1">
      <shopitems>
        <shopitem
          ikey="a_thing"
          label="A Thing"
          description="Things are really great, this one is cheap">
          <costs>
            <stat_cost ikey="cash" value="100"/>
          </costs>
          <modifiers>
            <grant_item ikey="a_thing"/>
          </modifiers>
        </shopitem>
      </shopitems>
    </shop>
  </shops>
</chameleon_data>
~~~

The first thing to note is the deep nesting of the XML. The reason for this is that there was plans to support multiple shops with each shop having its own additional requirements etc. While that feature never eventuated, it's legacy lives on in the XML structure. All you really care about is the `shopitem` nodes.

## `shopitem` Attributes.
### `ikey`
A unique identifier used to buy the item.
### `label`
A short description of the item.
### `description`
A longer description of the item.

## `shopitem` Elements.
### `costs`
This is a standard roar [cost](../concepts/costs.md) element. It gets applied to the purchaser when the purchase is complete, and must be satisfiable for the purchase to occur.

### `requirements`
This is a standard roar [requirement](../concepts/requirement.md) element. It must be satisfiable for the purchase to occur.

### `visibility`
This is a standard roar [requirement](../concepts/requirement.md) element. It must be satisfiable for the purchase to be listed or occur.

### `modifiers`
This is a standard roar [cost](../concepts/modifiers.md) element. It gets applied to the purchaser when the purchase is complete.

## `tag` elements
Provided purely for client side sorting/filtering/grouping.


