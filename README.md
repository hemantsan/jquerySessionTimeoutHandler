# sessionTimeout

## Description

Given a certain amount of time before the session expires, a dialog is shown to the user with the option to either log out now, or stay connected.

If log out now is selected, the page is redirected to a logout URL. If stay connected is selected, a keep-alive URL is requested through AJAX. If no option is selected before the session expires, the page is automatically redirected to a timeout URL.

The time left of the session is retrieved from the server through ajax in json format or through configuration

This implementation with its default values works nicely with multiple tabs (as is the server who controls the time left the session has)

The example uses PHP. Open default.php to view.

## Usage

1. Include jQuery
2. Include jQuery UI (for dialog)
3. Include jquery.sessionTimeout.js
4. Call `$.sessionTimeout();` after document ready

## Options

- **title**

Title of the dialog window shown to the user.
_Default:_ 'Your session is about to expire.'

- **message**

Text of the dialog window shown to the user.
_Default:_ 'Your session is about to expire.'

- **keepAliveUrl**

URL to call through AJAX to keep session alive. This request resets the session timeout.
_Default:_ 'keepAlive.php'

- **retrieveTimeLeftUrl**

URL to call through AJAX to get how much time left this session has. This request doesn´t update the session timeout
_Default:_ 'keepAlive.php'

- **redirUrl**

URL to take browser to if no action is take before session times out
_Default:_ 'timedOut.php'

- **logoutUrl**

URL to take browser to if user clicks "Log Out Now"
_Default:_ 'logout.php'

- **defaultSessionTime**

Default session timeTime in milliseconds.

If set to false, the time will be retrieved from the server via retrieveTimeLeftUrl.

_Default:_ false

- **warnWhenLeft**

Time in milliseconds before the session expires to open the dialog
_Default:_ 300000 (5 minutes)

- **checkTimeBeforeRedirect**

If true, before issuing a redirect to redirUrl, will ask the server if the session has time left. If it has, will reset the timers accordingly. This way different tabs will be synchronized

_Default:_ true

- **errorGettingSessionTimeLeft**

Message to alert the user if there was an error retrieving the time left from the server

_Default:_ There was an error retrieving session information from the server. Session could be timed out

- **modalId**

Id of the modal window generated

_Default:_ sessionTimeout-dialog

## Examples:

**Default** - Get Time left from server. Send Keep Alive. Check if session still active before redirect

```javascript
$('body').sessionTimeout();
```


**Just timer** - Don't send keepAlive to server - Don't bring time left from server

```javascript
$('body').sessionTimeout({
    redirUrl :               'timedOut.php',
    logoutUrl :              'logout.php',
    warnWhenLeft :            10000,
    defaultSessionTime:       20000,
    checkTimeBeforeRedirect:  false,

    getSessionTimeLeft: function ( event, data ){
    	data.callback.call(data.plugin, data.plugin.options.defaultSessionTime);
    }
});
```

**Keep Alive** - But don't bring time left from server
```javascript
$('body').sessionTimeout({
    keepAliveUrl :           'keepAlive.php',
    redirUrl :               'timedOut.php',
    logoutUrl :              'logout.php',
    defaultSessionTime:       20000,
    warnWhenLeft :            10000,
    checkTimeBeforeRedirect:  false,

    getSessionTimeLeft: function ( event, data ){
        $.ajax({
            context: data.plugin,
            dataType: "json",
            url: data.plugin.options.keepAliveUrl,
            success: function(){
        		data.callback.call(data.plugin,
        			data.plugin.options.defaultSessionTime);
            },
            error: function(){
                alert(data.plugin.options.errorGettingSessionTimeLeft);
            }
        });
    }
});

```