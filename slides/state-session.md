### ampersand-state: session properties

<pre><code class="js" contenteditable>module.exports = AmpersandState.extend({
    // ...
    // properties that aren't intended to be stored across sessions
    session: {
        timesHappened: {
            type: 'number',
            default: 0
        },
        mostRecentlyHappened: {
            type: 'date',
            default: function () {
                return new Date();
            }
        }
    }
    // ...
});
</code></pre>
