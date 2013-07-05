# Integrating App Store In App Purchases (IAPs) with RoarEngine

## App Store setup

1. Make sure your contracts are all in order

2. Create an AppID and make sure its OK for in app purchases
       * Means you must use a non-wildcard app id

3. Add the app to the appstore via itunes connect
       * Fill in all the metadata

4. Add an IAP to your application via itunes connect
       * Make it consumable
       * Note down the IAP key - you **must use the same key in the Roar Engine setup**

5. You don't need to upload anything to itunes connect except placeholder images to actually get to the testing phase

## Application setup

A very beta and minimal example of the application side of this code can be found here: [https://github.com/roarengine/roar-ios-iapdemo](https://github.com/roarengine/roar-ios-iapdemo) . Changes will at least need to be made in `RERoarEngine.m` to set the correct default server root.


1. You're going to need to get information to and from Roar Engine
   * For parsing Roar Engine responses you're going to need an XML reading library
       * libxml2 is a good choice because its already installed on iPhone but use whatever you have experience with e.g. NSXml (also part of the iPhone default libraries)
   * Build the POST responses
       * The actual coms is done using NSMutableURLRequest
       * It makes it easier if you split all your App-Roar coms code into a `RERoarEngine` and `REHttpDelegate` classes
2. You're going to need to get info to and from the app store
       * For this you're going to be using StoreKit
       * Put all this code in the main application delegate `iaptest01AppDelegate` (you might want to create your own class for this)
       * You can not test StoreKit code in the simulator - you have to use a real device
3. You also need to set up a test user, and sign out from the appstore/itunes on the device before testing
   * Do NOT sign in anywhere with your new test user - do not even click on the verification email you get - doing these things can invalidate the test user.

## Roar Engine setup

You need to create the IAP in the Roar Engine configuration. To do this you need to add an entry to the [appstore.xml](../configuration_files/appstore.xml.md).

You can call the item anything you like but you **must use the same key** that you used for that item in the Appstore Setup

## Application Flow

This assumes that the user is logged in and you have a valid `auth_token` for that user:

1. Get the list of IAPs from roar. `http://api.roar.io/game/appstore/shop_list/`
2. Get the detailed information (prices etc.) of the IAPs from the appstore. `SKProductsRequest initWithProductIdentifiers:`
3. Show these to the user
4. Let them buy what they select `SKPayement paymentWithProductIdentifier:`
5. Wait for App Store transaction response to delegate implementing `SKPaymentTransactionObserver paymentQueue: updatedTransactions:`
6. Forward the hex encoded receipt from the appstore transaction and `auth_token` to roarengine. `http://api.roar.io/game/appstore/buy/`
    * Roar should respond with `OK` and the list of changes
7. Remove the transaction : `[[SKPaymentQueue defaultQueue] finishTransaction: transaction]`
