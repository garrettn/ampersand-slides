###  Child models & collections

```javascript
var Model = require('ampersand-model');
var Teacher = require('./teacher');
var Students = require('./students');

module.exports = Model.extend({
  children: {
    teacher: Teacher
  },
  collections: {
    students: Students
  }
});
```

Children receive a `parent` property.