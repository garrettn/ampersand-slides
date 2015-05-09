### ampersand-state: initialization logic

<pre><code class="js" contenteditable>module.exports = AmpersandState.extend({
    // ...
    // logic to be executed on instantiation

    initialize: function () {
        this.on('change:name', this.nameChanged, this);
    },

    // custom function
    nameChanged: function (name) {
        // do something with name
    }
    // ...
});
</code></pre>
