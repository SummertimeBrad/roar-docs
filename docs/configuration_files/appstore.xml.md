# `appstore.xml`

This file controls the products available for purchase as in game purchases from the iOS App Store.

Note that it does not make the items available on the iOS store, merely controlling what happens when the app store purchase occurs. Further details on what is required to configure an App Store item can be found in our [App Store integration guide](../guides/AppStore-In-AppPayments.md).

A typical file will look like this:

~~~
<?xml version='1.0' encoding='UTF-8'?>
<chameleon_data>
  <appstore>
     <appstore_entry ...>...</appstore_entry>
     <appstore_entry ...>...</appstore_entry>
  </appstore>
</chameleon_data>
~~~

Each `appstore_entry` will look like this:

~~~
<appstore_entry product_identifier="com.yourapp.appstore_woozle" label="A Woozle from the App Store">
  <modifiers>
    <grant_stat ikey="attack" value="10"/>
  </modifiers>
</appstore_entry>
~~~

## `product_identifier` attribute (string)
This is the product identifier as it appears in your App Store portal.

## `label` attribute (string)
A short label describing the purchase

## `modifiers` element
This is a standard roar [modifier](../concepts/modifiers.md) element. It gets applied to the purchaser when the purchase is complete.
