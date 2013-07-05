You can integrate roar with the Google Wallet Chrome Web Store.

# Setup your google chrome web store account

Follow the ["Step One" section here](https://developers.google.com/in-app-payments/docs/tutorial).

You'll also need to set your callback to the correct URL for your game, which will be `https://<YOURSERVER>/<YOURGAME>/chrome_web_store/callback/`.

# Let roar know about your settings

You need to add  your app id and app secret to the roar configuration in your [`keys.json`](../configuration_files/keys.json.md) file.

# Add items to the shop

Add your items to the [`chrome_webstore.xml`](../configuration_files/chrome_webstore.xml.md) configuration file.

# Integrate into your client

Simply call `http://<YOURSERVER>/<YOURGAME>/chrome_web_store/list/` to get a list of all purchasable items. These entries come with a jwt field that should be passed to the google javascript webstore API.

The javascript looks something like this:

~~~javascript
goog.payments.inapp.buy({
  "parameters": {},
  "jwt": jwt,
  "success": function (result) { ...},
  "failure": function (result) { ... }
  });
~~~

When this function is fired it will pull up a frame or new window asking for the users details:

![cws_google_popup.png](https://roar.tenderapp.com/help/assets/02c7f6facc8ca6a78caabe62f0f8d1458eeb14ef/cws_google_popup_normal.png)

# Check your reports

Go to https://checkout.google.com/sell/orders to get a list of orders

![checkout_transaction_list.png](https://roar.tenderapp.com/help/assets/2c97643ecc84b9274e9b33c7a0e245d7b792e818/checkout_transaction_list_normal.png)

You can then view the details for each transaction by clicking on the Order Number

![checkout_transaction_detail.png](https://roar.tenderapp.com/help/assets/510876e96f9bd233ad3acb77acd3bde8d6b06361/checkout_transaction_detail_normal.png)
