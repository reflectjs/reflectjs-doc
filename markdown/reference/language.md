# Language

Reflect.js is a declarative reactive language for implementing the Presenter in a [Model-view-presenter](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter) architecture, where the Model is usually provided by RESTful web services and the View is the browser DOM.

It extends HTML with [logic values](#values), [reactive JavaScript expressions](#expressions), [visibility scopes](#scopes) and [scope methods](#methods).

## Logic values {#values}

Logic values can be added to any HTML using `:`-prefixed attributes. These attributes won't appear in output pages and values will be added to page-specific code.

### String literals

```html
<html :msg="hello"
      :did-init="[[console.log(msg)]]">
</html>
```

The output page won't have the `:msg` and `:did-init` attributes, it will have its own JavaScript code that will handle them. Specifically, page root object &mdash; associated with page root tag, `<html>` &mdash; is given an `msg` property whose value is the `"hello"` string.

`did-init` is a [delegate method](runtime#did) that's executed when a tag's scope is initialized. In this example it simply logs the value of `msg`. This will happen in both the server at page delivery and in the client at page load.

### Non-string literals

```html
<html :count="[[1]]"

      :user="[[{
        name: 'Joe',
        age: 30
      }]]"

      :fruit="[[['apple', 'banana']]]"

      :msg="[['hello']]"

      :did-init="[[
        // display values and types in the log
        console.log('count', count);
        console.log('user', user);
        console.log('fruit', fruits);
        console.log('msg', msg);
      ]]">
</html>
```

Logic values are JavaScript object properties and, as such, their value can be other than just strings. Since what's inside a `[[...]]` clause is JavaScript code, `count` will be assigned a number, `user` will be assigned an object, `fruit` will be assigned an array and `msg` will still be assigned a string albeit using a different syntax than in the previous example.

> Reflect.js [extended syntax](markup#attribute-values) for attributes makes for a comfortable way of expressing code. You can space things out as you would in a JavaScript source, use unescaped `<` and `>` characters, and use JavaScript comments to document things.

## Reactive expressions {#expressions}

Reactive JavaScript expressions, marked with `[[` and `]]`, can be used anywhere in page text and attributes.

### Initial evaluation

```html
<html data-date="date: [[new Date().toDateString()]]">
  <body>
    It's [[new Date().toDateString()]]
  </body>
</html>
```

This example will display the current date in body text and will set it as the `data-date` attribute in the root tag.

When used in text, an expression result is converted to string, and `null` and `undefined` results are turned into empty strings.

This is true also when used for interpolating results into a literal string as in `data-date` above.

Logic attribute values initialized with a single expression and with no text outside the `[[...]]` clause, on the other hand, are not converted so we can assign any kind of value to them, functions included:

```html
<html data-date="[[getDate()]]"
      :getDate="[[function() {
        return new Date().toDateString();
      }]]">
  <body>
    It's [[getDate()]]
  </body>
</html>
```

In the examples above expressions are only evaluated once, at page initialization, in both the server and the client.

### Re-evaluation chain

Expressions are compiled to page-specific reactive code. This code evaluates them once at page initialization, and then automatically re-evaluates them when any of the values they reference changes. If their result changes, it's automatically reflected in the page:

```html
<html>
  <body :v1="[[0]]"
        :v2="[[v1 * 2]]"
        :did-init="[[setInterval(() => v1++, 1000)]]">
    Doubled seconds: [[v2]]
  </body>
</html>
```

In this example `<body>` has a value `v1` that's incremented every second. This triggers re-evaluation of `v2`, which in turn triggers re-evaluation of the `[[v2]]` expression in body text, whose result is then reflected in page text.

This re-evaluation chain is started periodically by the timer. This only happens in the client though, because timers don't work in the server as everything in the future is left to the client.

In actual applications, re-evaluation chains are mostly triggered by user interaction. Similarly, this only happens in the client as user events don't get triggered in the server.

## Visibility scopes {#scopes}

Tags with at least one logic value have their own scope, meaning they have their own JavaScript object behind the scenes. In addition, the standard tags `<html>`, `<head>` and `<body>` always have a scope regardless.

Scopes can be nested, since tags can. Expressions in a scope can see values declared in the scope itself as well as those declared in its outer scopes. Values in a scope [mask](https://en.wikipedia.org/wiki/Variable_shadowing) namesake values in outer scopes.

```html
<html :v1="[[10]]" :v2="[[20]]" :v3="[[30]]">
  <body :v2="[[21]]" :v3="[[31]]">
    <div :v3="[[32]]"
         :did-init="[[console.log(v1, v2, v3)]]">
    </div>
  </body>
</html>
```

`did-init` will log "10 21 32": it accesses `v1` in the root, `v2` in the body &mdash; which masks `v2` in the root &mdash; and `v3` in the div &mdash; which masks `v3` in the body and `v3` in the root.

Values in a scope have no declaration order, they can all access each other regardless of their position in the code.

Scopes can be given a name with the special `:aka` attribute, and standard tags `<html>`, `<head>` and `<body>` are named "page", "head" and "body" by default.

A scope's name exists as value in its outer scope, and can be used to access its values from outside. There's no way to directly access the values of an anonymous scope from outside.

```html
<html>
  <head :color="red">
    <style>
      body {
        color: [[color]];
      }
    </style>
  </head>
  <body :on-click="[[() => {
          head.color = (head.color === 'red' ? 'green' : 'red')
        }]]">
    Color is [[head.color]]
  </body>
</html>
```

In this example both the `<style>` and the `<body>` contents depend on `color`, declared in the `<head>` tag. `<style>` can directly see it because it's in one of its outer scopes. `<body>` can access it via `head.color` because it can see `head` in its outer scope `<html>` and can use it to access `color` via dot notation.

Thanks to reactivity, both automatically reflect changes in color value.

## Scope methods {#methods}

Expressions can be used to declare function values, either with the arrow or the classic syntax:

```html
<html>
  <body :log="[[(v) => console.log(v)]]"
        :on-click="[[() => log('click')]]">
  </body>
</html>
```

Function values are always called with `this` bound to their scope, i.e. they always act as scope methods:

```html
<html>
  <body :x="[[1]]" :getX="[[() => x]]">
    <span :x="[[2]]">
      [[getX()]]
    </span>
  </body>
</html>
```

This example will log `1`: even though `getX()` is called from within the `<span>` scope, it is executed in the context of the `<body>` scope. In other words, it is called with `this` bound to `<body>`'s Reflect.js object. 

> `this` is automatically added by the compiler, but can be still used explicitly if desired.
