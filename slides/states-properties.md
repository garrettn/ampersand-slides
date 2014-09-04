### Defining properties

```javascript
var State = require('ampersand-state');

module.exports = State.extend({
  props: {
    id: 'string',
    name: ['string', true, '']
    preference: {
      type: 'string',
      required: true,
      values: ['cats', 'dogs', 'neither'],
      default: 'neither',
      setOnce: true
    }
  },
  extraProperties: 'ignore'
});
```
