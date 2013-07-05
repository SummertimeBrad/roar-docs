## Facebook Integration

Roar provides several integration points with Facebook:

- **Login and Signup**: handled using the Facebook OAuth interface
- **List friends**: Listing a user's Facebook friends
- **Facebook Credits**: Using Credits in your Roar game

See also, related tutorial video, Roar Engine Player Login Using Facebook OAuth: http://youtu.be/NeG5aQmudlw.

## Setting up a game on Facebook & Roar


### Roar Configuration

Add the following Facebook application data to your Roar configuration, these are added to your [`keys.json`](../configuration_files/keys.json.md) file.
The three pieces of information you will need are:

- **Application ID** - example: 1247716671236712
- **Application Secret** - example: 9210c793a017ed24165523aef237b
- **Site URL** - where are is your client hosted, e.g. http://yourhost/yourgame/

**Note**: these values must be *exactly* what they are in Facebook - be extremely aware of any trailing slashes or spaces. A single extra character will cause Facebook to FAIL in a way that is difficult to debug and could cost you hours. Triple check these values.

There are two different approaches to Facebook authentication possible with Roar Engine - Signed Requests or OAuth.

## Signed Request Workflow
This will only work if you have a signed request verifying the current user. You will automatically get this from Facebook as a POST parameter if you are running an **iframe app** *within Facebook*.

1. Get the signed request. This will be passed to your application as a POST parameter, e.g. `$POST['signed_request']` (for PHP).

2. To **create** a new user, two POST parameters are passed to `/facebook/create_signed`:
    - `signed_request` : the signed request from Facebook
    - `name` : a unique username/id for your user

3. To **login** a user using the signed request pass the `signed_request` POST parameter to `/facebook/login_signed`.

Super simple!

##  OAuth based workflow

The more extended (but more flexible) workflow for a Roar Facebook app is as follows, or see tutorial video: http://youtu.be/NeG5aQmudlw. (**Note**: This will work for both Canvas and Facebook Connect applications):

1. **Check for code**: is the parameter: `code` in the URL that Facebook has loaded your game? If yes, skip ahead and /facebook/fetch_oauth_token. Otherwise, move on to step 2.

2. **Direct**: Your app must direct the browser to the `authorize` URL in the Facebook Graph API: `https://graph.facebook.com/oauth/authorize?client_id=YOUR_APPLICATION_ID&redirect_uri=YOUR_SITE_URL`

3. **FB check**: If the player has not authorised your game yet, Facebook will present a standard Authorisation Page, where the user will be asked to give your app permissions to access their data. This is out of your hands.

4. **Get code**: If a player is authorised, Facebook will redirect to YOUR_SITE_URL with a URL parameter `code`. For example: `
http://YOUR_SITE_URL?code=2.mYPvig__.3600.12918oZyPB-pA...etc...`

This `code` parameter in the URL is the input parameter for the Roar API call to [/facebook/fetch_oauth_token/](http://roarengine.github.io/webapi/#facebookfetch_oauth_token). The `code` you provide to Roar based on the above URL is everything *after* the `?code=`, so the `2.mYPvig__.36...etc` is what you'll send.

You can strip the `code` parameter from the URL using a custom JavaScript function like [ParseURI](http://blog.stevenlevithan.com/archives/parseuri).



### Fetching an OAuth token

Now that you have the `code`, your application needs to call:

**[/facebook/fetch_oauth_token/](http://roarengine.github.io/webapi/#facebookfetch_oauth_token)** - Inputs:

- **code**: The `code` that was returned from the authorize Facebook graph API

For example, sending `/facebook/fetch_oauth_token/` with data `{code: 2.mYPv...}` would return:

~~~xml
<roar tick="127455369786">
 <facebook>
    <fetch_oauth_token status="ok">
         <oauth_token>104271466587092|1.mYu275YylcGHf6vC ... hxrk63ouytUiBdBc</oauth_token>
    </fetch_oauth_token>
 </facebook>
</roar>
~~~



### Process Summary

1. Direct player to https://graph.facebook.com/oauth/authorize?etc..etc..
2. Returns a `code` as part of the return URL. Grab this and store it.
3. Make a call to Roar to fetch_oauth_token (the input parameter is the `code`)
4. Roar returns with `<oauth_token`>` - use this oauth_token to:
5. Call Roar to login the player using /facebook/login_oauth/ {input: oauth_token}
6. If that fails, it means the user doesn't exist, so call /facebook/create_oauth/ {input: oauth_token}
7. On success, retry the /facebook/login_oauth/ {send oauth_token}
8. The login functions will return a **Roar** `auth_token`. This is the token you need to make calls to Roar for this player.

There are some known cases where Facebook just does "the wrong thing" when validating tokens. Our recommendation is to parse out the following error in your error handler: `Error validating access token`, and then:

1. Wait a few seconds and retry the request (login or create)
2. If that fails, you'll need to revalidate the token (ie. fetch a new OAuth token from FB (either yourself or using the /fetch_oauth_token/ call)
3. If that fails, you will need to notify your player to have them relogin entirely. We know this sucks, but Facebook is in control of this.


### Login and Signup

#### Log player in using OAuth token

**[/facebook/login_oauth/](http://roarengine.github.io/webapi/#facebooklogin_oauth)** - Inputs:

- **oauth_token**: Retrieved from the fetch_oauth_token method described above

Returns the Roar `auth_token` that is used for all subsequent calls for that player


#### Create a new player (register/signup)

**[/facebook/create_oauth/](http://roarengine.github.io/webapi/#facebookcreate_oauth)** - Inputs:

- **oauth_token**: Retrieved from the fetch_oauth_token method described above

Returns `status="ok"` on success. User will now need to be logged in with the login_oauth method above.



## List Friends

Listing Facebook friends can be done by calling:

**[/facebook/friends/](http://roarengine.github.io/webapi/#facebookfriends)** - Inputs

* **auth_token**: Roar auth_token obtained on login [/facebook/login_oauth/](http://roarengine.github.io/webapi/#facebooklogin_oauth)

This will return an XML block similar to the following:

~~~xml
<roar tick="128888053531">
  <facebook>
     <friends status="ok">
         <!-- "Mashton" is already playing this game and has an 'id' we can use -->
         <friend fb_name="Mashton Groober" fb_id="51151277315" id="7877788777"/>
         <!-- These other two friends are not playing this game (no 'id') -->
         <friend fb_name="Jumpy Maxton" fb_id="529465555"/>
         <friend fb_name="Ami Jones" fb_id="523055555"/>
    </friends>
  </facebook>
</roar>
~~~

**Note**: Facebook friends who are already playing the game will return with an `id` which corresponds to their Roar user id that can be used to make other calls to that player (e.g. mail, gifts, etc.)


### In game friends

Roar maintains a separate "in-game" list of friends that can be directly listed by calling:

**[/friends/list/](http://roarengine.github.io/webapi/#friendslist)** - Inputs:

* **auth_token**: Roar auth_token obtained on login [/facebook/login_oauth/](http://roarengine.github.io/webapi/#facebooklogin_oauth)

Returns a list of friends that have been invited and accepted in the game (different from a players 'global' friends in Facebook).


<a name="fb-credits"></a>
## Facebook Credits

You can enable your players to pay for in-game Roar items using Facebook Credits. The process has three steps 1) Tell Facebook where to send its callback once a payment is made, 2) Setup FB Shop items inside of Roar (which get awarded to the player when a Credits purchase is made on Facebook, and 3) Setup your game client to interface with Facebook to initiate purchases - see http://developers.facebook.com/docs/payments/build/

### 1. In your Facebook Application (on Facebook)
Set up the facebook credits callback url to be:

`http://api.roar.io/ [gametag] /admin/fb/`

**Remember the trailing slash on this address /**

### 2. In Roar Engine configuration editor
You can now create purchasable collections using the FB Shops area. You can add any combination of items, currencies, attributes and resources to these shop elements. Many games simply make an in-game virtual currency available for purchase with FB Credits.

To create an FB Shop item, fill in the following fields:

* **ikey** - the unique key that FB passes to purchase this shop element
* **description** - used by FB to describe your product to the user
* **image URL** - The URL for the image to display to the user
* **product URL** - A permalink to the URL where you display the product to the user (can be the same as the image URL)
* **price in FB credits** - how much will this cost a player

To retrieve a list of your available shop items in Roar for purchase with FB credits use:
`http://api.roar.io/ yourgametag /facebook/shop_list/`

### 3. In your game client
Implementation in your game client follows almost exactly with Facebook's recommendations for the pay call as found http://developers.facebook.com/docs/creditsapi/ with one exception:

You *must* pass in the FB shop item ikey (created in the Roar config editor - see below) and the players roar_id packed as a json string via the order_info parameter.

Following is an example showing the change required in the sample code provided by Facebook

~~~javascript
function placeOrder() {

  // Assign an internal ID that points to a database record
  var order_info = {'item':'abc123','roar_id':'43234543'};

  // calling the API ...
  var obj = {
    method: 'pay',
    order_info: order_info,
    purchase_type: 'item'
  };

  FB.ui(obj, callback);
}
~~~

For more information from Facebook about how to integrate your game client with Credits, check here: http://developers.facebook.com/docs/payments/build/

(Note: Unity developers may use this Asset store package for "out of the box" support: http://u3d.as/content/grumpy-ferret-limited/unity-facebook-credits/1Xk )


<a name="fb_offerwall"></a>
## Facebook in-app currency offerwall
To integrate the facebook in-app currency offer wall into your application you must follow these steps:

 * Set the currency that you wish to use in the facebook configuration UI. And set the facebook credits callback to `api.roar.io/gamename/admin/fb/` (Using the correct game name and server for your game).

 * Serve a currency icon and a currency info file from one of your servers. The content of the info file should look like this:

     ~~~html
     <html>
     <head prefix="og: http://ogp.me/ns#
       fb: http://ogp.me/ns/fb#
       fbpayment:http://ogp.me/ns/fb/fbpayment#">
       <meta property="fb:app_id"      content="[YOUR APP ID]" >
       <meta property="og:type"        content="fbpayment:currency" >
       <meta property="og:url"         content="[URL OF THIS FILE]" >
       <meta property="og:title"       content="[NAME OF YOUR CURRENCY]" >
       <meta property="og:description" content="[DESCRIPTION OF THIS CURRENCY]" >
       <meta property="og:image"       content="[URL TO AN IMAGE]" >
       <meta property="fbpayment:rate" content="[CONVERSION RATE OF FB INTO YOUR CURRENCY]" >
     </head>
     </html>
     ~~~

 * Display the offerwall from your app, like this: (this example uses jQuery)

     ~~~javascript
     $('#YOUR_BUTTON').click( function(){
       var obj = {
         method: 'pay',
         action: 'earn_currency',
         product: '[URL OF CURRENCY INFO PAGE]'
       };

       FB.ui(obj, function(data) { /* handle response */ } );
     });
     ~~~

That's it.
