### Using ampersand-collection

<pre><code contenteditable>
var Things = require('./things');

var things = new Things([
    {name: 'thing 1'},
    {name: 'thing 2'},
    {name: 'thing 3'}
]);

// length property proxies the models array
console.log(things.length); // 3

things.on('add remove reset', function () {
   console.log('The collection changed!');
});

things.add({name: 'thing bazillion!'}); // triggers event

console.log(things.length); // 4

// assuming we have the id of some model already
var thing2 = things.get(thing2Id);

console.log(thing2.toJSON());

things.remove(thing2); // triggers event

console.log(things.length); // 3

// array functions proxied on models array
var numberThings = things.filter(function (thing) {
   return /\d+/.test(thing.name);
});
</code></pre>
