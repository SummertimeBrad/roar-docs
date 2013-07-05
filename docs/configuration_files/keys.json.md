# `keys.json`
This file is used for configuring how the web layer interfaces with extrernal services.

Here's an anotated version of the file (note - this is NOT valid JSON and will not work as a `keys.json` file unless you strip out all the comments).

Note that you will need to create this file yourself, by copying and editing the `config/default/keys.template.json` file.

~~~javascript
{
  "game_db":"RoarTest", // The name of the Postgres database used by the server

  // Facebook integration settings
  "facebook":
  {
    "api_key":"ae0101010101",   // These used to be different but now should be the same.
    "secret":"ae0101010101",
    "in_app_currency":"cash",   // Facebook credits transactions needs to know what your premium in game currency is
    "app_id":"bdbdbdbd1231231", // Your application id as found in the developer section on facebook
    "site_url":"http://someawesomesite.pqr/mygame", // Callback URL exactly as in developer section on facebook
  },

  // Google authentication settings
  "google":
  {
    // Location that users are redirected back to after logging in.
    // Must match that set in your application configuration.
    "redirect_uri":"http://someawesomesite.pqr/mygame",
    "client_id":"asd64sd2345hasdj",     // App Id as given by google auth.
    "client_secret":"123bak2vhjas23]"   // App secret as given by google auth
  },

  // Google Chrome Web Store settings
  "chrome_web_store":
  {
    "seller_id":"123abc",     // [Google chrome web store seller id]
    "seller_secret":"321def", //[Google chrome web store seller secret]
    // Google chrome web store prefix.
    // All purchases in your google store logs will be prefixed with this.
    // This can aid in sorting purchases if multiple games are using the same
    // google web store account.
    "name_prefix":"mygame"
  },
}
~~~
