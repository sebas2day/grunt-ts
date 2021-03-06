## DEPRECATED.
Please use [Transforms](https://github.com/grunt-ts/grunt-ts#transforms) in new projects.

### AMD / RequireJS support

When both `outDir` and `amdloader` options are specified a JavaScript requireJS loader file is created using the information available from `reference.ts`.

The file consists of three sections.:

* The initial ordered section.
* A middle order independent section loaded asynchronously.
* And a final ordered section.

E.g the following `reference` file:

```typescript
/// <reference path="classa.ts" />

//grunt-start
/// <reference path="deep/classb.ts" />
/// <reference path="deep/classc.ts" />
//grunt-end

/// <reference path="deep/deeper/classd.ts" />
/// <reference path="app.ts" />
```

This corresponds to an `amdloader` (edited for readability):

```typescript
// initial ordered files
define(function (require) {
  require(["./classa"],function () {
    // grunt-ts start
    require(["./deep/classb",
             "./deep/classc"],function () {
      // grunt-ts end
      // final ordered files
      require(["./deep/deeper/classd"],function () {  
        require(["./app"],function () {
          // final ordered file loaded
        });
      });
    });
  });
});
```

### Advantage of using amdloader option

The following combination of circumstances are the main use-case for amdloader compared to the original Compiler supported AMD:

* Use RequireJS since allows to debug "js" files instead of "ts" files. This is useful in some cases, the most common way is using AMD.
* Keep the ability to individually compile only changed files (for a faster dev-compile-run cycle)
* However, File order doesn't matter, even when there is a inter file depenendency (e.g. AngularJS runtime Dependency injection)

In such a case it is possible to either create a `loader.js` manually or have grunt create one.

**Further Explanation** When using `export class Foo{}` at the root level of the file the only way to use the type information of Foo in another file is via an import statement: `import foo = require('./potentially/long/path/to/Foo');`.

The ordering implied by this isn't necessary when using a runtime Dependency Injection framework like AngularJS.

Having a loader gives the js debugging (+ async) advantages of RequireJS without the overhead of constantly requesting via `import` to get the TypeScript type inference and worrying about file paths when they are not relevant.

Note: the individual file source-map will continue to work so it is possible to debug individual "JS" or "TS" files :)
