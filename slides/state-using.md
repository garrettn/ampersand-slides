### Using ampersand-state

<pre><code contenteditable>
var Thing = require('./states/thing');

var thing = new Thing({name: 'This thing happened'});

thing.on('change:name', function () {
   console.log('Name changed to ' + thing.name);
});

thing.name = 'It was actually this other thing'; // triggers event

// derived properties are calculated for you
console.log('This thing ' + (thing.happenedToday ? 'happened' : 'did not happen') + ' today.');

// create a shallow copy of props (not session or derived)
console.log(thing.toJSON());
</code></pre>
