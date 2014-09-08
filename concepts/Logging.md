# Logging

###Overview

Sails comes with a simple, built-in logger called captains-log. It's usage is purposely very similar to Node's console.log, but with a handful of extra features; namely support for multiple log levels with colorized, prefixed console output.

###Configuration

The Sails logger's configuration is located in sails.config.log, for which a conventional configuration file (config/log.js) is bundled in new Sails projects out of the box.
When configured at a given log level, Sails will output log messages that are output at a level at or above the currently configured level. This log level is normalized and also applied to the generated output from socket.io, Waterline, and other dependencies. The hierarchy of log levels and their relative priorities is summarized by the chart below:

|Priority|level|fns visible|
|---|---|---|		 
|0|silent|N/A|
|1|error|.error()|
|2|warn|.warn(), .error()|
|3|debug|.debug(), .warn(), .error()|
|4|info|.info(), .debug(), .warn(), .error()|
|5|verbose|	.verbose(), .info(), .debug(), .warn(), .error()|
|6|silly|.silly(), .verbose(), .info(), .debug(), .warn(), .error()|

###Notes

* The default log level is "info". When your app's log level is set to "info", Sails logs limited information about the server/app's status.
* When the log level is set to "silly", Sails outputs internal information on which routes are being bound and other detailed framework lifecycle information, diagnostics, and implementation details.
* When the log level is set to "verbose", Sails logs Grunt output, as well as much more detailed information on the routes, models, hooks, etc. that were loaded.

##sails.log()
###Overview

Each of the methods below accepts an infinite number of arguments of any data type, seperated by commas. Like console.log, data passed as arguments to the Sails logger is automatically prettified for readability using Node's util.inspect(). Consequently, standard Node.js conventions apply- i.e. if you log an object with an inspect() method, it will be run automatically, and the string that it returns will be written to the console. Similarly, objects, dates, arrays, and most other data types are pretty-printed using the built-in logic in util.inspect() (e.g. you see { pet: { name: 'Hamlet' } } instead of [object Object].)

###sails.log()

The default log function, which writes console output to stderr at the "debug" log level.

```
sails.log('hello');
// -> debug: hello.
```

###sails.log.error()

Writes log output to stderr at the "error" log level.

```
sails.log.error('Unexpected error occurred.');
// -> error: Unexpected error occurred.
```

###sails.log.warn()

Writes log output to stderr at the "warn" log level.

```
sails.log.warn('File upload quota exceeded for user','request aborted.');
// -> warn: File upload quota exceeded for user- request aborted.
```

###sails.log.debug()

Alias for sails.log()

###sails.log.info()

Writes log output to stdout at the "info" log level.

```
sails.log.info('A new user (', 'mike@foobar.com', ') just signed up!');
// -> info: A new user ( mike@foobar.com ) just signed up!
```

###sails.log.verbose()

Writes log output to stdout at the "verbose" log level. Useful for capturing detailed information about your app that you might only want to enable on rare occasions.

```
sails.log.verbose('A user initiated an account transfer...')
// -> verbose: A user initiated an account transfer...
```

###sails.log.silly()

Writes log output to stdout at the "silly" log level. Useful for capturing utterly ridiculous information about your app you only need on rare occasions.

```
sails.log.silly('A user probably clicked on something..?');
// -> silly: A user probably clicked on something..?
```
