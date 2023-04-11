# Server

TBD

## Development mode

The `reflectjs` command and Servers initialized with the `devmode = true` start a local Reflect.js server in development mode, which means  will:

* automatically recompile pages whose overall source code is modified, including `<:import>`-ed and `<:include>`-d sources, recursively
* automatically update route mapping based on the `:URLPATH` directives in modified pages
* deliver page-specific code as a separated `.js` file with sourcemap
* optionally, inject autorefresh code
* add support for `__noclient` and `__noserver` HTTP parameters

## Server values

### URLPATH
