# `chrome_webstore.xml`

This file controls the items available for sale through the google chrome webstore. Further details on integrating with the chrome web store can be found in our integration guide.

Note you configure your google chrome webstore credentials in [`keys.json`](keys.json.md).

More details about the google chrome web store integration can be found in our [guide](../guides/Google-Wallet-Chrome-Web-Store.md).


A typical file looks like this:

~~~
<?xml version="1.0" encoding="UTF-8"?>
<chameleon_data>
  <chrome_webstore>
    <chrome_webstore_entry ...>...</chrome_webstore_entry>
    <chrome_webstore_entry ...>...</chrome_webstore_entry>
  </chrome_webstore>
</chameleon_data>
~~~

and each `chrome_webstore_entry` element looks like this.

~~~
<chrome_webstore_entry
  ikey="woozle"
  label="A Woozle"
  description="Buy a cute little woozle."
  price="1.00"
  >
  <tag value="a_tag"/>
  <tag value="another_tag"/>
  <modifiers>
    <grant_stat ikey="energy" value="10"/>
  </modifiers>
</chrome_webstore_entry>
~~~

## `tag` elements
A `chrome_webstore_entry` can contain an arbitrary number of `tag` elements, each with its own value attribute. These are not used directly by the server, but can be used for filtering and grouping on the client.

## the `modifiers` element
This is a standard roar [modifier](../concepts/modifiers.md) element that is applied to the player when the purchase is completed successfully.

