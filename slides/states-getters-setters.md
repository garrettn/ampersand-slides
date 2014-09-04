###  Getters & setters

Thank you, `Object.defineProperty`!

```
var person1 = new BackbonePerson();

// cumbersome
console.log(person1.get('name'));
person1.set('name', 'Henry'); // so the `change` event is triggered

var person2 = new AmpersandPerson();

// this is more like it!
console.log(person2.name);
person2.name = 'June'; // `change` event is still triggered!
```
