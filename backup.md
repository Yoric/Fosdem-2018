# Backup demo slides!

Juuuuust in case this fancy computer cannot connect to the projector.

---

## Source

```js
/*!
 * jQuery JavaScript Library v3.3.1
 * https://jquery.com/
 *
 * Includes Sizzle.js
 * https://sizzlejs.com/
 *
 * Copyright JS Foundation and other contributors
 * Released under the MIT license
 * https://jquery.org/license
 *
 * Date: 2018-01-20T17:24Z
 */
( function( global, factory ) {

        "use strict";

        if ( typeof module === "object" && typeof module.exports === "object" ) {

                // For CommonJS and CommonJS-like environments where a proper `window`
                // is present, execute the factory and get jQuery.
                // For environments that do not have a `window` with a `document`
                // (such as Node.js), expose a factory as module.exports.
                // This accentuates the need for the creation of a real `window`.
                // e.g. var jQuery = require("jquery")(window);
                // See ticket #14549 for more info.
                module.exports = global.document ?
                        factory( global, true ) :
                        function( w ) {
                                if ( !w.document ) {
                                        throw new Error( "jQuery requires a window with a document" );
                                }
                                return factory( w );
                        };
        } else {
                factory( global );
        }

```

---

## Compressing

```sh
$ ./target/debug/examples/encode --sections br --strings br --tree br --in tests/data/jquery-3.3.1.js --out /tmp/jquery-3.3.1.binjs
Treating tests/data/jquery-3.3.1.js
Output: /tmp/jquery-3.3.1.binjs
Parsing.
Annotating.
Encoding.
Writing.
Successfully compressed 271751 bytes => 44938 bytes
```

---

## Back to source

```js
(function(global, factory) {
  if (typeof module === "object" && typeof module.exports === "object") {
    module.exports = global.document ? factory(global, true) : function(w) {
      if (!w.document) {
        throw new Error("jQuery requires a window with a document")
      }
      return factory(w)
    }
  } else {
    factory(global)
  }
}(typeof window !== "undefined" ? window : this, function(window, noGlobal) {
  var arr = [];
  var document = window.document;
  var getProto = Object.getPrototypeOf;
  var slice = arr.slice;
  var concat = arr.concat;
  var push = arr.push;
  var indexOf = arr.indexOf;
  var class2type = {};
  var toString = class2type.toString;
  var hasOwn = class2type.hasOwnProperty;
  var fnToString = hasOwn.toString;
  var ObjectFunctionString = fnToString.call(Object);
  var support = {};
  var isFunction = function isFunction(obj) {
    return typeof obj === "function" && typeof obj.nodeType !== "number"
  };
  var isWindow = function isWindow(obj) {
    return obj != null && obj === obj.window
  };
  var preservedScriptAttributes = {
    type: true,
    src: true,
    noModule: true
  };
  function DOMEval(code, doc, node) {
    doc = doc || document;var i,
```