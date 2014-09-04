### Session properties

For the session only; not persisted

```javascript
var Model = require('ampersand-model');

module.exports = Model.extend({
  props: {
    id: 'string'
  },
  session: {
    selected: 'boolean'
  }
});
```
