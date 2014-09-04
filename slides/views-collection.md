### Rendering collections

Automatically track when the collection changes

```javascript
//...
render: function () {
  this.renderWithTemplate();
  this.renderCollection(this.model.people, PersonView, this.queryByHook('people'));

  this.cacheElements({
    nameField: '[data-hook~=new-person-name]'
  });

  return this;
}
//...
```