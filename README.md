# Loggerr

A very simple logger with levels, thats it, no frills.

## Install

```
$ npm install --save loggerr
```

## Usage

```javascript
var Loggerr = require('loggerr');

var log = new Loggerr();
log.error(new Error('My error message'));
/*
output: Thu Apr 16 2015 22:05:27 GMT-0500 (CDT) [error] - {"msg":"Error: My error message\n<STACK TRACE>"}
*/
```

## Log Levels

Loggerr supports log levels.  Each log level can be directed to a different output stream or disabled entirely.  The supported levels are as follows:

- Emergency
- Alert
- Critical
- Error
- Warning
- Notice
- Info
- Debug

Constants are available for setting and referencing the levels and their streams.  These constants are the all uppercase version of the level.  Here is an example of setting the log level:

```javascript
var logger = new Loggerr({
	level: Loggerr.DEBUG
});

logger.debug('Foo');
/*
output: Thu Apr 16 2015 22:05:27 GMT-0500 (CDT) [debug] - {"msg":"Foo"}
*/
```

## Log Formatting

Loggerr supports formatting via formatter functions.  The default formatter outputs a timestamp, the log level and the messages formatted as json.  But you can provide a custom formatter function with the `formatter` options.  Formatter functions take three parameters: `date`, `level`, `data`.  Say we want to output the log message with a color based on the level:

```javascript
var Loggerr = require('loggerr'),
	chalk = require('chalk');

var logger = new Loggerr({
	formatter: function(date, level, data) {
		var color;
		switch(level) {
			case Loggerr.EMERGENCY:
			case Loggerr.ALERT:
			case Loggerr.CRITICAL:
			case Loggerr.ERROR:
				color = chalk.red;
				break;
			case Loggerr.WARNING:
			case Loggerr.NOTICE:
				color = chalk.yellow;
				break;
			case Loggerr.INFO:
			case Loggerr.DEBUG:
				color = chalk.white;
				break;
		}
		return color(data.msg);
	}
});
```

The only built in formatter right now is `default`, but I plan on adding some more of the basic ones, which I will document here.

## Output Streams

You can output each level to it's own stream.  The method is simple, just pass an array of streams corresponding to each level as the `streams` option.  The simplest way is to just map over `Loggerr.levels`, this is how we set the defaults:

```javascript
new Loggerr({
	streams: Loggerr.levels.map(function(level, i) {
		return i > Loggerr.WARNING ? process.stdin : process.stderr;
	})
});
```

The most useful reason to specify an output stream to to redirect logs to files.  Here is an example of how to do that:

```javascript
var logfile = fs.createWriteStream('./logs/stdout.log', {
	flags: 'a',
	encoding: 'utf8'
});

new Loggerr({
	streams: Loggerr.levels.map(function() {
		return logfile;
	})
});
```