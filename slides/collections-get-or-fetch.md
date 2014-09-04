###  Get or fetch

Fetch from the server if necessary

```javascript
var person3;

people.getOrFetch(12, {all: true}, function (err, person) {
  if (err) {
    console.log('Error!');
  } else {
    person3 = person;
  }
});
```
