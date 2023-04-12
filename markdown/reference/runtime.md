# Runtime

The Reflect.js runtime reserves special treatment to the following [logic values](language#values).

## Special values

These are declared in HTML as any other logic value, e.g. `<div :hidden="[[false]]">`, but they have a special meaning.

### aka

TBD

### hidden

TBD

### data

TBD

### dataOffset

TBD

### dataLength

TBD

### nestFor

TBD

### nestIn

TBD

## Special prefixes

Values using special prefixes have special meanings based on the prefix itself. We can specify them in HTML code using the prefix followed by `-` (dash), and we can access them from JavaScript using the prefix followed by `_` (underscore).

### attr

Normal HTML attribute that have an [expression](language#reactive-expressions) in their value are available with this prefix in tag's [scope](language#visibility-scopes). E.g.:

```html
<html lang="[['en']]">
```

will add a value `attr_lang` in the root scope. The reason is, because they can change, we may want to know what value they have in our JavaScript code.

### on

These values contain event listeners for DOM events. E.g.:

```html
<body :on-click="[[(ev) => ...]]">
```

will assign the listener function to value on_click in tag's [scope](language#visibility-scopes), and will register it with its underlying DOM element.

Using `:on-` attributes for registering DOM event handlers is recommended, so scopes can be added and removed as needed, with their event listener properly registered and unregistered, when [replicating](#data) scopes or when using [conditional blocks](stdlib).

### handle

These values contain handler expressions which are executed when and only when some other value changes. E.g.:

```html
<body :count="[[0]]"
      :mode="increment"
      :handle-count="[[
        console.log(count, mode);
      ]]">
</body>
```

The handler will only be executed only when `count` changes (including when it's initialized). Changes of the mode value won't execute it.

This is in contrast with how reactive expressions normally work: they are re-evaluated when any of the values they reference changes.

### class

These values let us add and remove class names in a scope's underlying DOM element.

TBD

### style

TBD

### did

TBD

### will

TBD

## Implementation values

TBD

### __id

### __dom

### __scope

### __outer

### __elementIndex

### __isLastElement

### __mixColors

### __regexMap
