###  Bindings!

```javascript
//...
bindings: {
  'model.name': {
    type: 'text',
    selector: '.group-name'
  },
  'model.enoughPeople': {
    type: 'toggle',
    hook: 'team-generator'
  },
  'model.teamSize': {
    type: 'value',
    hook: 'team-size'
  },
  'model.teamGeneratorUrl': {
    type: 'attribute',
    hook: 'team-generator-url',
    name: 'href'
  }
}
//...
```

Automatically applied in `renderWithTemplate()`
