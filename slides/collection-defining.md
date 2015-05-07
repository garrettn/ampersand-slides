### ampersand-collection

<pre><code contenteditable>
var AmpersandCollection = require('ampersand-collection');
var Thing = require('./thing');

module.exports = AmpersandCollection.extend({
   model: Thing,

   comparator: 'mostRecentlyHappened',

   mainIndex: 'id',

   indexes: [/* other props to find models by */]
});
</code></pre>
