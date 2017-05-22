## Fork

This is a fork of the node-irc module that correctly handles `encoding` option of the client,
meaning that any inbound messages would be forcefully encoded using Iconv module (e.g. `Windows-1251` -> `UTF-8`) and any outbound messages would be encoded as well (e.g. `UTF-8` -> `Windows-1251`). In the original code this feature is broken because:

1. ICU autodetection provided by `node-icu-charset-detector` detects charsets wrongly (e.g. detects `ISO-8859-2` instead of `Windows-1251`);
2. Encoding works not as expected (converts autodetected encoding into specified encoding for inbound messages, I don't know how this is even useful);
3. Doesn't handle outbound messages at all.

See diffs for details.

[![Travis](https://img.shields.io/travis/martynsmith/node-irc.svg?style=flat)](https://travis-ci.org/martynsmith/node-irc)
[![npm](https://img.shields.io/npm/v/irc.svg?style=flat)](https://www.npmjs.com/package/irc)
[![Dependency Status](https://img.shields.io/david/martynsmith/node-irc.svg?style=flat)](https://david-dm.org/martynsmith/node-irc#info=Dependencies)
[![devDependency Status](https://img.shields.io/david/dev/martynsmith/node-irc.svg?style=flat)](https://david-dm.org/martynsmith/node-irc#info=devDependencies)
[![License](https://img.shields.io/badge/license-GPLv3-blue.svg?style=flat)](http://opensource.org/licenses/GPL-3.0)
[![Join the chat at https://gitter.im/martynsmith/node-irc](https://badges.gitter.im/martynsmith/node-irc.svg)](https://gitter.im/martynsmith/node-irc?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)


[node-irc](http://node-irc.readthedocs.org/) is an IRC client library written in [JavaScript](http://en.wikipedia.org/wiki/JavaScript) for [Node](http://nodejs.org/).

You can access more detailed documentation for this module at [Read the Docs](http://readthedocs.org/docs/node-irc/en/latest/)


## Installation

The easiest way to get it is via [npm](http://github.com/isaacs/npm):

```
npm install irc
```

If you want to run the latest version (i.e. later than the version available via
[npm](http://github.com/isaacs/npm)) you can clone this repo, then use [npm](http://github.com/isaacs/npm) to link-install it:

```
    npm link /path/to/your/clone
```

Of course, you can just clone this, and manually point at the library itself,
but we really recommend using [npm](http://github.com/isaacs/npm)!

## Basic Usage

This library provides basic IRC client functionality. In the simplest case you
can connect to an IRC server like so:

```js
var irc = require('irc');
var client = new irc.Client('irc.yourserver.com', 'myNick', {
    channels: ['#channel'],
    encoding: 'windows-1251'
});
```

Of course it's not much use once it's connected if that's all you have!

The client emits a large number of events that correlate to things you'd
normally see in your favorite IRC client. Most likely the first one you'll want
to use is:

```js
client.addListener('message', function (from, to, message) {
    console.log(from + ' => ' + to + ': ' + message);
});
```

or if you're only interested in messages to the bot itself:

```js
client.addListener('pm', function (from, message) {
    console.log(from + ' => ME: ' + message);
});
```

or to a particular channel:

```js
client.addListener('message#yourchannel', function (from, message) {
    console.log(from + ' => #yourchannel: ' + message);
});
```

At the moment there are functions for joining:

```js
client.join('#yourchannel yourpass');
```

parting:

```js
client.part('#yourchannel');
```

talking:

```js
client.say('#yourchannel', "I'm a bot!");
client.say('nonbeliever', "SRSLY, I AM!");
```

and many others. Check out the API documentation for a complete reference.

For any commands that there aren't methods for you can use the send() method
which sends raw messages to the server:

```js
client.send('MODE', '#yourchannel', '+o', 'yournick');
```

## Help! - it keeps crashing!

When the client receives errors from the IRC network, it emits an "error"
event. As stated in the [Node JS EventEmitter documentation](http://nodejs.org/api/events.html#events_class_events_eventemitter) if you don't bind
something to this error, it will cause a fatal stack trace.

The upshot of this is basically that if you bind an error handler to your
client, errors will be sent there instead of crashing your program.:

```js
client.addListener('error', function(message) {
    console.log('error: ', message);
});
```


## Further Support

Further documentation (including a complete API reference) is available in
reStructuredText format in the docs/ folder of this project, or online at [Read the Docs](http://readthedocs.org/docs/node-irc/en/latest/).

If you find any issues with the documentation (or the module) please send a pull
request or file an issue and we'll do our best to accommodate.

You can also visit us on ##node-irc on freenode to discuss issues you're having
with the library, pull requests, or anything else related to node-irc.
