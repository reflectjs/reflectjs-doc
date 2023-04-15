# Quick Start

> get up and running with Reflect.js

Reflect.js is an [Express](https://expressjs.com/) application for [Node.js](https://nodejs.org/) which processes and serves web pages, giving them the ability to execute unified client/server reactive logic.

## Installation

```sh
npm install -g reflectjs-core
```

Once installed, we can use the [`reflectjs`](reference/cli) command to start a [development server](reference/server) from our current directory.

## Hello World

```sh
mkdir myapp
cd myapp
reflectjs
# ... http://localhost:3001
```

We can add a simple page...

```html
<!-- index.html -->
<html>
  <body>
    Good [[
      new Date().getHours() <= 12
          ? 'morning'
          : 'evening'
    ]]!
  </body>
</html>
```

...and open [http://localhost:3001](http://localhost:3001/) to see it in action:

```
Good morning!
```

## Use in a project

Let's create a demo project...

```sh
mkdir myproject
cd myproject
npm init -y
npm install reflectjs-core
mkdir docroot
```

...and add an entry point with our configuration:

```js
// index.js
const reflectjs = require('reflectjs-core');
const path = require('path');

new reflectjs.Server({
  port: 3002,
  rootPath: path.join(__dirname, 'docroot'),
});
```

In TypeScript we can use imports instead:

```ts
// index.ts
import { Server } from 'reflectjs-core';
import path from 'path';

new Server({
  port: 3002,
  rootPath: path.join(__dirname, 'docroot'),
});
```

We can now create a page in `docroot/`...

```html
<!-- index.html -->
<html>
  <body>
    Good [[
      new Date().getHours() <= 12
          ? 'morning'
          : 'evening'
    ]]!
  <body>
</html>
```

...and run the project:

```sh
node index.js
# ... START http://localhost:3002
```

> <i class="bi-info-square-fill"></i> When using Reflect.js in a project we can customize it and add our own services and middleware to the server. All options are documented in the [Server Reference](reference/server).

## Next steps

* [Introduction](introduction) &mdash; get the gist of Reflect.js<!--- * [Examples](examples/reacivity) &mdash; see all the features in bite-sized examples -->
* [Tutorial](tutorial) &mdash; get a taste of Reflect.js development
* [Reference](reference/cli) &mdash; find all the details
