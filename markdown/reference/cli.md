# CLI

Once installed Reflect.js provides the `reflectjs` command:

```sh
npm install -g reflectjs-core
mkdir myapp
cd myapp
echo "<html><body>it's [[new Date()]]</body></html>" > index.html
reflectjs
# ... http://localhost:3001
```

It currently has no options and simply launches a [development server](server#development-mode) serving pages from the current directory, as an easy way to start experimenting with Reflect.js pages.

> <i class="bi-info-square-fill"></i> The `reflectjs` command is only intended as a development tool.
