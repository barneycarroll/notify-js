notify-js [![build status](https://secure.travis-ci.org/WebReflection/notify-js.svg)](http://travis-ci.org/WebReflection/notify-js)
=========
A simplified notification channel for global, or local, interaction.

In case of doubts, please read the [related blog entry](https://www.webreflection.co.uk/blog/2015/08/14/the-line-between-events-and-promises).


### API
There are 4 methods, described as such:

  * `notify.when(type, callback)` to add a listener associated to a specific type/event. If such type was already resolved, it will synchronously invoke the callback.
  * `notify.that(type, any1[, any2[, ...]])` aliased as `notify.about(type, any1[, any2[, ...]])` resolves a type or returns a callback used to resolve the `type` with received arguments, once executed ( see examples )
  * `notify.drop(type, callback)` in case something hasn't happened yet and we changed our mind about waiting for the event, we can still remove it!
  * `notify.new()` create a new `notify`-like object. By default, `notify` is a global communication channel, but it brings this simple method that will create a new object for private communication purpose, if needed.

In order to use private channels, feel free to create unique IDs as type, or simply use a `Symbol`, whenever available.


#### Examples
```js
// assuming "data" event hasn't happened yet
notify.when("data", function (data) {
  console.log(data);
});

// whenever it will happen, resolve via {any:'value'}
notify.that("data", {any:'value'});
// all listeners waiting for it, will be triggered


// what if you add a listener after the `.that` call?
notify.when("data", function (data) {
  console.log('yep, instantly called!', data);
  // data will be exactly {any:'value'}
});


// what if we redefine data ?
notify.that("data", {another:'value'});
// from now on, whoever will ask `.when` data
// the value will be the updated one
// but every listener already fired and satisfied
// will be simply ignored


// what if I want to be sure the channel is private?
// feel free to use a Symbol as channel
var myPrivateSymbol = Symbol();
notify.when(myPrivateSymbol, ...);

// otherwise create a new notify like variable
var privateNotify = notify.new();
```

It is also possible to use `.that(type)` in order to generate a callback
that once invoked will resolve the type.

Example
```js
var fs = require('fs');
var notify = require('notify-js');


// before or after, it doesn't matter
notify.when('config-available', function (err, content) {
  if (err) console.warn('damn it');
  else console.log(content);
});


// at any time in the past, present, or future
fs.readFile(
  'config.json',
  notify.that('config-available')
);
```

Whenever the last `notify.that` will be executed, all listeners waiting for it will be triggered.


## Which file ?
Browsers could use [the minified version](https://github.com/WebReflection/notify-js/blob/master/build/notify-js.js), otherwise there is a [node module](https://github.com/WebReflection/notify-js/blob/master/build/notify-js.node.js)
which is also available via npm as `npm install notify-js`.



### Compatibility
Notify has been created in a full cross platform, browser, engine way, and it's compatible down to IE6 on Desktop, and every mobile browser I could test.

If it wasn't for [this Espruino bug](https://github.com/espruino/Espruino/issues/561) it would have worked in there too.

There is a [live test page](http://webreflection.github.io/notify-js/test/) which, if green, would indicate everything is fine.