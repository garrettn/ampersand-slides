### ampersand-state

<pre><code contenteditable>
var AmpersandState = require('ampersand-state');
var uuid = require('uuid');
var Entries = require('./entries');

module.exports = AmpersandState.extend({
    // properties of the state object
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
    },

    // properties that aren't intended to be stored across sessions
    session: {
        timesHappened: {
            type: 'number',
            default: o
        },
        mostRecentlyHappened: {
            type: 'date',
            default: function () {
                return new Date();
            }
        }
    },

    // properties that are calculated based on other properties
    derived: {
        happenedToday: {
            deps: ['mostRecentlyHappened'],
            fn: function () {
                return this.mostRecentlyHappened.toDateString === (new Date()).toDateString();
            }
        }
    },

    // child collections
    collections: {
        entries: Entries
    },

    initialize: function () {
        // logic to be executed on instantiation
    }

});
</code></pre>
