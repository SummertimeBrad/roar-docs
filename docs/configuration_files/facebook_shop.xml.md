# `facebook_shop.xml`

This file controls the products available for purchase as in game purchases via facebook credits.

Further details about how to setup the facebook shop can be found in the [Facebook Integration guide](../guides/Facebook-Integration.md).

A typical file will look like this:

~~~
<?xml version='1.0' encoding='UTF-8'?>
<chameleon_data>
  <facebook_shop>
     <fbshopitem ...>...</fbshopitem>
     <fbshopitem ...>...</fbshopitem>
  </facebook_shop>
</chameleon_data>
~~~

Each `fbshopitem` will look like this:

~~~
<fbshopitem
  ikey="woozle"
  label="A Woozle"
  description="Buy a cute little woozle."
  image_url="http://yourserver.com/your_image.png"
  product_url="http://yourserver.com/woozle.html"
  price="1"
  >
  <modifiers>
    <grant_stat ikey="attack" value="10"/>
  </modifiers>
</fbshopitem>
~~~

## `ikey` attribute (string)
A unique identifier for the item

## `label` attribute (string)
A short description of the purchase

## `description` attribute (string)
A longer description of the purchase

## `image_url` attribute (string)
The image to display for the item.

## `product_url` attribute (string)
A link for further information about the item.

## `price` attribute (integer)
The cost in facebook credits for the item.

## `modifiers` element
This is a standard roar [modifier](../concepts/modifiers.md) element. It gets applied to the purchaser when the purchase is complete.
