### Element queries

Who needs jQuery?

```javascript
var View = require('ampersand-view');
var templates = require('../templates');

module.exports = View.extend({
  template: templates.pages.products,
  render: function () {
    this.renderWithTemplate();

    // query the selector within `this.el`
    this.form = this.query('#newProductForm');

    // query `[data-hook~=action-save]`
    this.saveButton = this.queryByHook('action-save');

    return this;
  }
});
```
