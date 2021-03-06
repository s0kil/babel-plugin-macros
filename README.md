# Babel Macros

This is a [Babel](https://babeljs.io/) plugin adds support for hygienic, non-syntactic macros for JavaScript.

[![Build Status](https://travis-ci.org/codemix/babel-plugin-macros.svg)](https://travis-ci.org/codemix/babel-plugin-macros)

[![NPM](https://nodei.co/npm-dl/babel-plugin-macros.png?months=1)](https://nodei.co/npm/babel-plugin-macros/)

> Note: Now requires Babel 6.

# What?

Turns code like this:
```js
DEFINE_MACRO(MAP, (input, visitor) => {
  const length = input.length;
  const result = new Array(length);
  for (let i = 0; i < length; i++) {
    result[i] = visitor(input[i], i, input);
  }
  return result;
});

function demo () {
  return MAP([1,2,3,4], item => item + 1);
}
```
Into code like this:
```js
function demo() {
  var _input = [1, 2, 3, 4];
  var _map = undefined;

  var _length = _input.length;
  var _result = new Array(_length);
  for (var _i2 = 0; _i2 < _length; _i2++) {
    _result[_i2] = _input[_i2] + 1;
  }
  _map = _result;

  return _map;
}
```

Also you may use more native syntax to define macro
```js
macro: function MACRO() {
  console.log('MACRO called');
}
MACRO();
```

Macro calls can also be chained, so if you declare a new macro called, e.g. `FILTER`:

```js
DEFINE_MACRO(FILTER, (input, visitor) => {
  const filtered = [];
  for (let i = 0; i < input.length; i++) {
    if (visitor(input[i], i, input)) {
      filtered.push(input[i]);
    }
  }
  return filtered;
});
```

You'll then be able to combine the two macros in one statement, without needing to nest:
```js
MAP([1, 2, 3], item => item + 1).FILTER(item => item < 4).length; // 2
```

# Why?

Because macros are incredibly useful! Also because they make it easy to write extremely fast code without sacrificing readability. When compiled with [closure elimination](https://github.com/codemix/babel-plugin-closure-elimination) the above code is [10x faster](http://jsperf.com/macros-vs-functions) than native `Array.prototype.map()`.

# Note: This is super experimental, use at your own risk!

# Todo List

- [ ] Allow macros in macro definitions (currently causes infinite loop)
- [ ] Allow macros to be imported and exported across files.
- [ ] Add `DEFINE_TRANSFORM` which is similar to `DEFINE_MACRO` but allows direct AST manipulation, not merely replacement.

# Installation

First, install via [npm](https://npmjs.org/package/babel-plugin-macros).
```sh
npm install --save-dev babel-plugin-macros
```
Then, in your babel configuration (usually in your `.babelrc` file), add `"macros"` to your list of plugins:
```json
{
  "plugins": ["macros"]
}
```

# ChangeLog
- **0.0.1** base implementation
- **1.0.0** update for babel@6 API
- **1.0.1** fix npm package
- **1.0.2** fix crash when missed some arguments
- **1.0.3** fix behavior of same-name macros in different scopes.  
   before this change same-name macros are re-declared.  
   now macros in different scopes - are different macros.
- **1.0.4** fix wrong scoping for equal-named macros  
fix combining more than 2 macros
- **1.0.5** optimization. Replacement `traverse` to `get` to find the desired node
- **1.0.6** honest exception for infinite recursion in macros
- **1.0.7** add checking types in runtime
- **1.0.8** block using `this` and `arguments` in macros
- **1.0.9** fix multiple `return` & `return` without value
- **1.0.10** optimize performance of compile. x10 at tests 
- **1.0.11** depedency for lodash no more needed 
- **1.0.12** new syntax for define macro, using label 
- **1.0.13** Implement function inlining for macro arguments 
- **1.0.14** fix for different inlined function types in macro-argument 
- **1.0.15** fix crash for case if babel-core use same babel-traverse, but in own sub-directory (without npm dedupe) 

# License

Published by [codemix](http://codemix.com/) under a permissive MIT License, see [LICENSE.md](./LICENSE.md).

