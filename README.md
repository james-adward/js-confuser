# JS Confuser

JS-Confuser is a JavaScript obfuscation tool to make your programs _impossible_ to read. [Try the web version](https://hungry-shannon-c1ce6b.netlify.app/).

## Key features

- Variable renaming
- Control Flow obfuscation
- String concealing
- Function obfuscation
- Locks (domainLock, date)
- [Detect changes to source code](https://github.com/MichaelXF/js-confuser/blob/master/Integrity.md)

## Presets

JS-Confuser comes with three presets built into the obfuscator.
| Preset | Transforms | Performance Reduction | Sample |
| --- | --- | --- | --- |
| High | 21/22 | 98% | [Sample](https://github.com/MichaelXF/js-confuser/blob/master/samples/high.js) |
| Medium | 15/22 | 52% | [Sample](https://github.com/MichaelXF/js-confuser/blob/master/samples/medium.js) |
| Low | 10/22 | 30% | [Sample](https://github.com/MichaelXF/js-confuser/blob/master/samples/low.js) |

You can extend each preset or all go without them entirely.

## Installation

```bash
$ npm install js-confuser
```

## Programmatic Usage

```js
var JsConfuser = require("js-confuser");

JsConfuser.obfuscate("console.log(1)", {
  target: "node",
  preset: "high",
  stringEncoding: false, // <- Normally enabled
}).then((obfuscated) => {
  console.log(obfuscated);
});
```

## CLI Usage

```shell
<coming soon>
```

## Options

### `target`

The execution context for your output. _Required_

1. `"node"`
2. `"browser"`

### `compact`

Remove's whitespace from the final output. (`true/false`)

### `minify`

Minifies redundant code (`true/false`)

### `es5`

Converts output to ES5-compatible code (`true/false`)

### `renameVariables`

Determines if variables should be renamed. (`true/false`)

- Potency High
- Resilience High
- Cost Medium

### `renameGlobals`

Renames top-level variables, keep this off for web-related scripts. (`true/false`)

### `identifierGenerator`

Determines how variables are renamed.
Modes:

- **`hexadecimal`** - \_0xa8db5
- **`randomized`** - w$Tsu4G
- **`zeroWidth`** - U+200D
- **`mangled`** - a, b, c
- **`number`** - var_1, var_2

```js
// Custom implementation
JsConfuser.obfuscate(code, {
  target: "node",
  renameVariables: true,
  identifierGenerator: function () {
    return "$" + Math.random().toString(36).substring(7);
  },
});

// Numbered variables
var counter = 0;
JsConfuser.obfuscate(code, {
  target: "node",
  renameVariables: true,
  identifierGenerator: function () {
    return "_NAME_" + (counter++);
  },
});
```

JSConfuser tries to re-use names when possible, creating very potent code.

### `controlFlowFlattening`

[Control-flow Flattening](https://docs.jscrambler.com/code-integrity/documentation/transformations/control-flow-flattening) obfuscates the program's control-flow by
adding opaque predicates; flattening the control-flow; and adding irrelevant code clones. (`true/false`)

- Potency High
- Resilience High
- Cost High

### `globalConcealing`

Global Concealing hides global variables being accessed. (`true/false`)

- Potency Medium
- Resilience High
- Cost Low

### `stringConcealing`

[String Concealing](https://docs.jscrambler.com/code-integrity/documentation/transformations/string-concealing) involves encoding strings to conceal plain-text values. This is useful for both automated tools and reverse engineers. (`true/false`)
   
- Potency High
- Resilience Medium
- Cost Medium

### `stringEncoding`

[String Encoding](https://docs.jscrambler.com/code-integrity/documentation/transformations/string-encoding) transforms a string into an encoded representation. (`true/false`)

- Potency Low
- Resilience Low
- Cost Low

### `stringSplitting`

[String Splitting](https://docs.jscrambler.com/code-integrity/documentation/transformations/string-splitting) splits your strings into multiple expressions. (`true/false`)

- Potency Medium
- Resilience Medium
- Cost Medium

### `duplicateLiteralsRemoval`

[Duplicate Literals Removal](https://docs.jscrambler.com/code-integrity/documentation/transformations/duplicate-literals-removal) replaces duplicate literals with a single variable name. (`true/false`)

- Potency Medium
- Resilience Low
- Cost Medium

### `dispatcher`

Creates a dispatcher function to process function calls. This can conceal the flow of your program. (`true/false`)

- Potency Medium
- Resilience Medium
- Cost Medium

### `eval`

#### **`Security Warning`**

From [MDN](<(https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/eval)**>): Executing JavaScript from a string is an enormous security risk. It is far too easy
for a bad actor to run arbitrary code when you use eval(). Never use eval()!

Wraps defined functions within eval statements.

- **`false`** - Avoids using the `eval` function. _Default_.
- **`true`** - Wraps function's code into an `eval` statement.

```js
// Output.js
var Q4r1__ = {
  Oo$Oz8t: eval(
    "(function(YjVpAp){var gniSBq6=kHmsJrhOO;switch(gniSBq6){case'RW11Hj5x':return console;}});"
  ),
};
Q4r1__.Oo$Oz8t("RW11Hj5x");
```

### `rgf`

RGF (Runtime-Generated-Functions) uses the `new Function(code...)` syntax to construct executable code from strings.
Only changes top-level functions and has to pass a reference array around.

**This can break your code. This is also as dangerous as `eval` (eval is evil!)**

```js
// Input
function log(x) {
  console.log(x);
}
log("hi");
// Output
var refs = [new Function('refs', 'x', 'console.log(x);')];
(function () {
  return refs[0](refs, ...arguments);
}('hi'));
```

### `objectExtraction`

Extracts object properties into separate variables. (`true/false`)

- Potency Low
- Resilience Low
- Cost Low

```js
// Input
var utils = {
  isString: x=>typeof x === "string",
  isBoolean: x=>typeof x === "boolean"
}
if ( utils.isString("Hello") ) {
  // ...
}

// Output
var utils_isString = x=>typeof x === "string";
var utils_isBoolean = x=>typeof x === "boolean"
if ( utils_isString("Hello") ) {
  // ...
}
```

### `flatten`

Brings independent declarations to the highest scope. (`true/false`)

- Potency Medium
- Resilience Medium
- Cost Low


### `calculator`

Creates a calculator function to handle arithmetic and logical expressions. (`true/false`)

- Potency Medium
- Resilience Medium
- Cost Low

### `lock.nativeFunctions`

Set of global functions that are native. Such as `require`, `fetch`. If these variables are modified the program crashes.

- Set to `true` to use the default native functions. (`string[]/true/false`)

- Potency Low
- Resilience Medium
- Cost Medium

### `lock.startDate`

When the program is first able to be used. (`number` or `Date`)

- Potency Low
- Resilience Medium
- Cost Medium

### `lock.endDate`

When the program is no longer able to be used. (`number` or `Date`)

- Potency Low
- Resilience Medium
- Cost Medium

### `lock.domainLock`

Array of regex strings that the `window.location.href` must follow. (`Regex[]` or `string[]`)

- Potency Low
- Resilience Medium
- Cost Medium

### `lock.integrity`

Integrity ensures the source code is unchanged. (`true/false`)
[Learn more here](https://github.com/MichaelXF/js-confuser/blob/master/Integrity.md).

- Potency Medium
- Resilience High
- Cost High

### `lock.countermeasures`

If the client is caught missing permissions (wrong date, bad domain), this will crash the current tab/process.

- `true` - Crash the browser
- `"string"` - Function name to call (pre obfuscated)

### `movedDeclarations`

Moves variable declarations to the top of the context. (`true`/`false`)

- Potency Medium
- Resilience Medium
- Cost Low

### `opaquePredicates`

An [Opaque Predicate](https://en.wikipedia.org/wiki/Opaque_predicate) is a predicate(true/false) that is evaluated at runtime, this can confuse reverse engineers
understanding your code. (`true/false`)

- Potency Medium
- Resilience Medium
- Cost Low

### `shuffle`

Shuffles the initial order of arrays. The order is brought back to the original during runtime. (`true/false`)

- Potency Medium
- Resilience Low
- Cost Low

## High preset
```js
{
  target: "node",
  preset: "high",

  // heavy
  dispatcher: true,
  controlFlowFlattening: 0.75,

  // extract
  objectExtraction: true,
  movedDeclarations: true,

  // variables
  renameVariables: true,
  identifierGenerator: "randomized",

  // Simple
  calculator: true,
  deadCode: 0.25,
  flatten: true,

  minify: true,
  opaquePredicates: 0.75,

  // data
  duplicateLiteralsRemoval: 0.75,
  globalConcealing: true,
  stringConcealing: true,
  stringEncoding: true,
  stringSplitting: 0.75,
}
```

## Medium preset
```js
{
  target: "node",
  preset: "medium",

  // heavy
  dispatcher: 0.75,
  controlFlowFlattening: 0.5,

  // extract
  objectExtraction: true,
  movedDeclarations: true,

  // variables
  renameVariables: true,
  identifierGenerator: "randomized",

  // Simple
  calculator: true,
  deadCode: 0.05,
  flatten: true,

  minify: true,
  opaquePredicates: 0.5,

  duplicateLiteralsRemoval: 0.5,
  globalConcealing: true,
  stringConcealing: true,
}
```

## Low Preset

```js
{
  target: "node",
  preset: "low",

  // heavy
  dispatcher: 0.5,
  controlFlowFlattening: 0.25,

  // extract
  objectExtraction: true,
  movedDeclarations: true,

  // variables
  renameVariables: true,
  identifierGenerator: "randomized",

  // Simple
  calculator: true,

  minify: true,
  opaquePredicates: 0.1,

  duplicateLiteralsRemoval: true,
  globalConcealing: true,
}
```

## Locks

You must enable locks yourself, and configure them to your needs.

```js
{
  target: "node",
  lock: {
    integrity: true,
    domainLock: ["mywebsite.com"],
    startDate: new Date("Feb 1 2021"),
    endDate: new Date("Mar 1 2021"),
    antiDebug: true,
    nativeFunctions: true,

    // crashes browser
    countermeasures: true

    // or custom callback (pre-obfuscated name)
    countermeasures: "onLockDetected"
  }
}
```

## Optional features

These features are experimental or a security concern.

```js
{
  target: "node",
  eval: true, // (security concern)
  rgf: true, // (security concern)

  // can break web-related scripts,
  // OK for NodeJS scripts
  renameGlobals: true,

  // experimental
  identifierGenerator: function(){
    return "_CUSTOM_VAR_" + (counter++);
  }
}
```

## Potential Issues

- 1.  String Encoding can corrupt files. Disabled `stringEncoding` manually if this happens.
- 2.  Dead Code can bloat file size. Reduce or disable `deadCode`.

## Bug report

Please open an issue with the code and config used.

## Feature request

Please open an issue and be descriptive what you want. Don't submit any PRs until approved.

## JsConfuser vs. Javascript-obfuscator

Javascript-obfuscator (obfuscator.io) is the popular choice for JS obfuscation. This means more attackers are aware of their strategies. JSConfuser offers unique features such as Integrity, locks, and RGF.

Automated deobfuscators are aware of obfuscator.io's techniques:

https://www.youtube.com/watch?v=_UIqhaYyCMI

However, the dev is quick to fix these. The one above no longer works.

Alternatively, you could go the paid-route with [JScrambler.com (enterprise only)](https://jscrambler.com/) or [PreEmptive.com](https://www.preemptive.com/products/jsdefender/online-javascript-obfuscator-demo)

I've included several alternative obfuscators in the `samples/` folder. They are all derived from the input.js file.

## Debugging

Enable logs to view the obfuscators state.

```js
{
  target: "node",
  verbose: true
}
```

## About the internals

This obfuscator depends on two libraries to work: `acorn` and `escodegen`

- `acorn` is responsible for parsing source code into an AST.
- `escodegen` is responsible for generating source from modified AST.

The tree is modified by transformations, which each step the traverse the entire tree.
Properties starting with `$` are for saving information (typically circular data),
these properties are deleted before output.
