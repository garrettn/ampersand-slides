### ampersand-state: props

<pre><code contenteditable>var AmpersandState = require('ampersand-state');
var uuid = require('uuid');
var Entries = require('./entries');

module.exports = AmpersandState.extend({
    props: {
        id: {
            type: 'string',
            default: uuid.v4
        },
        name: 'string',
        description: 'string',
        created: {
            type: 'date',
            default: function () {
                return new Date();
            }
        }
    }
});
</code></pre>
