### ampersand-state: child models and collections

<pre><code contenteditable>module.exports = AmpersandState.extend({
    // ...
    // child models
    children: {
        creatingUser: User
    },

    // child collections
    collections: {
        entries: Entries
    }
    // ...
});
</code></pre>
