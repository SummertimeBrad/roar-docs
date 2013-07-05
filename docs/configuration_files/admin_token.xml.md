# `admin_token.xml`

This file configures the *admin token*, which is used in all admin level RoarEngine requests.

A typical file looks like this :

~~~
<chameleon_data>
  <admin>
    <token value="12345" />
  </admin>
</chameleon_data>
~~~

Here the tokens value is required to be an integer of your choosing. Keep this value secret, and do not embed it into your client code as any user with this token can make admin calls.
