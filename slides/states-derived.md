###  Derived properties

```javascript
module.exports = State.extend({
  props: {
    firstName: 'string',
    lastName: 'string'
  },
  derived: {
    fullName: {
      deps: ['firstName', 'lastName'],
      fn: function () {
        return this.firstName + ' ' + this.lastName;
      }
    }
  }
});
```