### ampersand-state: derived properties

<pre><code class="js" contenteditable>module.exports = AmpersandState.extend({
    // ...
    // properties that are calculated based on other properties
    derived: {
        happenedToday: {
            deps: ['mostRecentlyHappened'],
            fn: function () {
                return this.mostRecentlyHappened.toDateString === (new Date()).toDateString();
            }
        }
    }
    // ...
});
</code></pre>
