### Indexes

Model finding made easy.

```javascript
var Collection = require('ampersand-rest-collection');
var Person = require('./person');

var People = Collection.extend({
  model: Person,
  mainIndex: '_id',
  indexes: ['name']
});

var people = new People();
people.fetch();

// find a person with `_id` of 2
var person1 = people.get(2);

// find a person with `name` of 'Joseph'
var person2 = people.get('Joseph', 'name');
```
