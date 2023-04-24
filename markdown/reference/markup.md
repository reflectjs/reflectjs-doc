# Markup

The Reflect.js language is based on HTML, so it has to define how it handles its quirks and how it tries to make our life easier where possible.

## Incomplete markup

Missing tags are automatically inserted and a well formed document is always generated.

```html
<html>
</html>
```

Because the server loads pages in simulated DOM, it fills in the blanks like browsers do. The output markup will therefore include `<!DOCTYPE html>` and the missing `<head>` and `<body>` tags.

## Auto-closed tags

Any tag can be self-closing, e.g. `<br/>` and `<div/>` are both valid and will generate `<br>` and `<div></div>`, respectively.

```html
<html>
<body>
    <br/>
    <div/>
</body>
</html>
```

Basically this extends the common habit of adding `/>` (with or without leading space) at the end of [void tags](https://developer.mozilla.org/en-US/docs/Glossary/Void_element) to any kind of tag, void or not. Both kinds will be generated correctly in the output page.

## Private comments

Comments starting with `<!---` (three dashes) are stripped from the page.

```html
<html>
<body>
  <!-- this comment will appear in output HTML -->
  <!--- this one will not -->
<body>
</html>
```

Sometimes we want our comments to stay private. Plus, by enforcing this rule, Reflect.js can use its own `<!---` comments in generated markup as markers for its internal working, knowing they cannot conflict with ours.

## Attribute names

Attribute names starting with `:` are valid (they're used to declare [logic values](language)) although they won't directly appear in output pages.

```html
<html :count=[[1]]>
</html>
```

## Attribute values

Attribute values can contain multiline text and unescaped `<` and `>` characters, so it's easy to put JavaScript code inside them. They can be quoted with `'`, `"`, and the `[[` + `]]` special markers. Unquoted attribute values are not supported. In the output all attributes will be standards-compliant of course.

```html
<html id='root'
      lang="en"
      hidden
      data-url=[[window.location.href]]>
</html>
```

> Note that the `[[...]]` construct removes the need to escape either `'` or `"` in attribute values, which is convenient when writing JavaScript code.
> Code editors don't natively support it though, so you may choose to use `"[[...]]"` or `'[[...]]'` and avoid using the quotes inside the expressions, or escape them as per HTML specs, with `&quot;` and `&apos`.
