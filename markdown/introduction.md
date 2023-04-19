# Introduction

> get the gist of Reflect.js

Reflect.js turns HTML into a [reactive language](#reactivity) for creating dynamic web sites and web apps, still [fully indexable](#indexability) out of the box, that can easily be based on [reusable components](#reusability).

We'll get straight to some actual code to show what this looks like in practice. We'll put our examples in a common `intro/` directory and we'll start a Reflect.js development server from within that dir.

As shown in [Quick Start](quick-start), you can do that quick and easy:

```sh
npm install -g reflectjs-core
mkdir intro
cd intro
reflectjs
# ... http://localhost:3001/
```

## It's a reactive language {#reactivity}

Reflect.js lets you add logic values to any tag (with `:`-prefixed attributes) and reactive JavaScript expressions in attributes and text (wrapped in `[[` and `]]`).

Let's start with a classic of reactive frameworks, the Seconds Counter:

```html
<!-- intro/seconds.html -->
<html>
  <body :count="[[0]]"
        :did-init="[[
            setInterval(() => count++, 1000)
        ]]">
    seconds: [[count]]
  </body>
</html>
```

<iframe src="/examples/intro/seconds"/>

When it serves a page, Reflect.js turns it into standard HTML with added code that starts executing in the server itself and then continues in the browser.

By opening [http://localhost:3001/seconds](http://localhost:3001/seconds) you'll get a live seconds counter, and in the page source you can see it was initially output with "seconds: 0" and then regularly updated in the client.

This is how it works:

* the `count` value, initially set to `0`, is added to the body tag
* the `did-init` code is executed when a tag is created: this happens both in the server at page delivery and in the client at page load
* in both cases `did-init` starts a timer to increment count every second
* in the server, though, everything in the future is not executed and left to the client
* for this reason, `count` remains `0` when the page is delivered
* in the client the same logic is applied
* this time, of course, the timer does work, `count` is periodically incremented and, thanks to reactivity, it's automatically reflected in page text

Since we're using a [development server](reference/server), requests with the `__noclient` parameter can be used to see what the page looks like to clients with no support for JavaScript, like search engine crawlers, so opening [http://localhost:3001/seconds?__noclient](http://localhost:3001/seconds?__noclient) you'll get a static page saying "seconds: 0" and no client-side code added.

> <i class="bi-info-square-fill"></i> You can learn more about Reflect.js [language](reference/language)<!--- and see more [reactivity examples](examples/reactivity)-->.

## It produces indexable web apps {#indexability}

Reflect.js was designed with both the server and the client in mind. Thanks to its holistic approach, it makes it easy to create web projects which behave both as fully indexable web sites and as modern, dynamic web apps at the same time.

In our example we'll create an app with its own dir in `intro/app/` and a page like this:

```html
<!-- intro/app/index.html -->
<html :URLPATH="/">
  <body>
    <div>
      <a href="index">[home]</a> | <a href="products">[products]</a>
    </div>

    <:on-off :on="[[head.router.name === 'index']]">
      <div>Home</div>
    </:on-off>

    <:on-off :on="[[head.router.name === 'products']]">
      <div>Products</div>
    </:on-off>
  </body>
</html>
```

This page is delivered for all paths inside its own directory, thanks to the [`:URLPATH`](reference/server#urlpath) directive. It can know what name it was requested with by using the [`head.router`](reference/stdlib#head-router) standard component.

Based on that, it can decide what actual content is displayed using the [`<:on-off>`](reference/stdlib#on-off) standard component.

[http://localhost:3001/app](http://localhost:3001/app/):

<iframe src="/examples/intro/app"/>

The `<:on-off>` component is implemented using a [`<template>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) tag, meaning its content is effectively removed from the DOM when `:on` is false.

> In a real app you'll probably want to keep different page contents in different page fragments and include them in the main file using the [`<:include>`](reference/preprocessor) directive.
<!---
> <i class="bi-info-square-fill"></i> You can learn more about making indexable web apps in the [indexability examples](examples/indexability).-->

## It supports reusability {#reusability}

In HTML pages there's usually a number of markup blocks that are replicated with minimal changes:

```html
<div class="products">
  <div class="product">
    <span>Gadget<span>
    <span class="price">€1</span>
  </div>
  <div class="product">
    <span>Widget<span>
    <span class="price">€2</span>
  </div>
</div>
```

Part of Reflect.js support for reusability is the [`<:define>`](reference/preprocessor#define) directive, which lets you declare your own custom tags:

```html
<!-- custom tag definition -->
<:define tag="app-product" class="product">
  <span>[[name]]<span>
  <span class="price">€[[price]]</span>
</:define>
```

```html
<!-- custom tag usage -->
<div class="products">
  <app-product :name="Gadget" :price="1"/>
  <app-product :name="Widget" :price="2"/>
</div>
```

We now have a much simpler markup that clearly specifies what it represents &mdash; an application product &mdash; and what's specific to each instance &mdash; name and price &mdash; greatly improving readability and maintainability.

Custom tag definitions are usually collected in page fragments (with an `.htm` extension so the server won't deliver them):

```html
<!-- intro/products-lib.htm -->
<lib>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link href="index.css" rel="stylesheet">

  <:define tag="app-product" class="product">
    <span>[[name]]</span>
    <span class="price">€[[price]]</span>
  </:define>
</lib>
```

...and can be [`<:import>`](reference/preprocessor#import)-ed where they're needed, normally inside the `<head>` tag:

```html
<!-- intro/products.htm -->
<html>
  <head>
    <:import src="products-lib.htm"/>
  </head>
  <body>
    <div class="products">
      <app-product :name="Gadget" :price="1"/>
      <app-product :name="Widget" :price="2"/>
    </div>
  </body>
</html>
```

<!--- <iframe src="/examples/intro/products"/> -->

> Page fragments must have an arbitrary root tag, like `<lib>` here.
<!---
> <i class="bi-info-square-fill"></i> You can learn more in the [reusability examples](examples/reusability).-->
> <i class="bi-info-square-fill"></i> You can learn more in [Preprocessor](reference/preprocessor).

## Next steps {#next-steps}
<!---
* [Examples](examples) &mdash; see all the features in bite-sized examples-->
* [Tutorial](tutorial) &mdash; get a taste of Reflect.js development
* [Reference](reference/cli) &mdash; find all the details
