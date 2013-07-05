# Logging

RoarEngine generates a lot of logs. And in debug mode it can generate a lot more. This is an attempt to document all the various logs and the methods used to configure them.

## Server Logs

### stdout & `log.txt`
These contain general information about the calls that hit the server layer.  Server restarts are logged here.

### `scriptlog.txt`
This contains errors or messages from the scripts.  Any lua script can output to this file by simply calling `print()`.

### events table
Many server events are logged into this table. The main events tracked by this are:

 * `use_item`
 * `buy`
 * `login`
 * `new_player`
 * `server_start`
 * `task_start`
 * `task_complete`
 * `fbbuy`

For each event the event type, the player id, a timestamp and some additional XML data is stored.

If you wish to add more logging of this nature, you will create a new class derived from `ILoggedEvent` and pass these to the `IEventLogger event_logger_` objects.

### other tables
There are several other tables which may be useful from a logging perspective.

* `appstore_log` which tracks all appstore purchases.
* `chrome_web_store_log` which tracks all chrome web store purchases.
* `transactions_facebook` which tracks all facebook credit transactions.


## Web layer logs

These are logged by the weblayer and are stored on the weblayer, which means
that if you're running multiple web nodes you will need to combine these
various logs. 

### Firehose log
So called because the amount of data it spews out is similar to a firehose.
The location for this file is configured in the `serverconfig.php` file.
It captures information about every call to the web layer - including arguments,
response, whether the request was an error, etc.

Here's an example with all lines truncated. Each line is formatted appropriately for
a syslog entry, and so can be used with many logging tools, the body of each request
is JSON and so can be used with many data-mining tools.

~~~
roar-firehose server {"request_id":"cc9616dae1b09783b ...
roar-firehose server {"request_id":"e6c642a50db51b9d9 ...
roar-firehose server {"request_id":"32edd0d48a7963278 ...
roar-firehose server {"request_id":"68ef90d80dbcbacfc ...
roar-firehose server {"request_id":"68ef90d80dbcbacfc ...
roar-firehose server {"request_id":"cb74a734769fad43a ...
~~~

Here's an expanded view of the example JSON

~~~javascript
{
  "request_id":"cc9616dae1b09783bcfd1b23d582868f",     // Every request gets a unique ID
  "type":"ok",                                         // Whether there was an error or not.
  "url":"http://localhost/server/admin/fb/",           // The URL for the request
  "method":"FacebookShopItemController::getShopItem",  // The sub-request to the server
  "params":["4772247128488054381","woozle"],           // Arguments passed to the server
  "error_code":"0",                                    // Error code
  "result":                                            // Server to web-layer response
  {
     "result":
     {
        "ikey":"woozle",
        "label":"A Woozle",
         ....
     }
  }
}
~~~

### Facebook credits log

Callbacks for facebook credits related calls get logged here. The file can be configured in `serverconfig.php`.


