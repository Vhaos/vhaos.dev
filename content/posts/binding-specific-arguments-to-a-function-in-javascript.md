+++
title = "Binding Specific Arguments to a Function in Javascript"
date = "2020-08-02T04:42:45+01:00"
author = "Kareem" 
authorTwitter = "kareemdagg" #do not include @
cover = "https://process.filestackapi.com/cache=expiry:max/resize=width:1050/Bx6aeyzaSLCsLh4u0xKL" #cover image to show for this post
tags = ["javascript"] #what tags (in the tags page) to list this post under
keywords = ["javascript"] #keywords to set for SEO
description = "The `Function.prototype.bind()` method not only allows us to override the `this` context of functions and objects, but also pass in a given sequence of arguments to a function as well! What happens however when we wish to bind specific arguments rather than have them passed in sequentially? 🤔" #text that shows in the post list view (if showFullContent) is false. Also set as description for SEO
showFullContent = false #whether to show all post content in list view (overrides description field if true)
weight = 0 #priority to set post in list and search views. 0 is default priority, 1 pins post, lower weight = higher priority. 
+++

# Typical Function Binding
---

The `Function.prototype.bind()` method not only allows us to override the `this` context of functions and objects, but also pass in a given sequence of arguments to a function as well!

For example, given a simple function that takes two numbers and returns the sum:

```js
function add(a, b) {
   return a + b;
};

add(1, 3); //returns 4
```
We can bind the 1st argument to the `add()` function to a specific value. The resulting _bound_ function `addThree` now needs only one argument (the value 3 has been bound to the first argument `a`).
```js
const addThree = add.bind(null, 3);  //this = null.  a = 3
addThree(4); // b = 4
// → 7
```

However, suppose we wish to preset specific arguments i.e. binding the second argument of a function (or third/fourth/fifth etc). Unfortunately we can't do this with just the _bind()_ method, we'll need to be a bit more creative.

# Decorators using the spread operator
---

We can use a function decorator! The ES6 spread operator (`...`) helps make our code more compact:

```js
// Bind arguments starting after however many are passed in.
function bind_trailing_args(fn, ...bound_args) {
    return function(...args) {
        return fn(...args, ...bound_args);
    };
}
```
If you'd prefer to specify the position at which binding starts:

```js
// Bind arguments starting with argument number "n".
function bind_args_from_n(fn, n, ...bound_args) {
    return function(...args) {
        return fn(...args.slice(0, n-1), ...bound_args);
    };
}
```

In the context of our example above:

```js
const addThree = bind_trailing_args(add, 3);
addThree(1) // calls add(1, 3)
```

What our function `bind_trailing_args` does is essentially wrap the function we wish to bind (`add`) within an anonymous function. Thanks to the spread operator (`...`), all the arguments we wish to bind are picked up by the expression `...bound_args` and set to trail at the end of our bounded function `add`. When `addThree(1)` is called, the argument `1` is passed into our anonymous function, which in turn passes it into our bound function `add` as `...args`.

# Implementations in Libraries
---

A few library functions for specific argument binding already exist. A few notable examples can be found below:

## 1. Lodash - [bind](https://lodash.com/docs/4.17.15#bind)

If you're already using [lodash](https://lodash.com/), You can use the lodash `_.bind` method to bind specific arguments too.

```js
import _, { bind } from 'lodash';

function add(a, b) {
  return a + b;
};

// Bind to first parameter (Nothing special here)
const bound = _.bind(add, null, 3);
bound(4);
// → 7

// Bind to second parameter by skipping the first one with "_"
var bound = _.bind(add, null, _, 4);
bound(3);
// → 7
```

## 2. Ramda - [curry](https://ramdajs.com/docs/#partialRight) and [__](https://ramdajs.com/docs/#__)

Combining the Ramda.js `curry` function with the placeholder `__`, allows us to bind specific arguments rather succinctly. 

```js
const add = (a, b, c) => a + b + c;

const addFive = R.curry(add)(__, 5, __);

addFive(1, 2); // => calls add(1, 5, 2)
```

Alternatively, if you're only binding trailing args, then the `partialRight` function would work too.

> Takes a function `f` and a list of arguments, and returns a function `g`. When applied, `g` returns the result of applying `f` to the arguments provided to `g` followed by the arguments provided initially.

```js
const add = (a, b, c, d) => a + b + c + d;

const addOne = partialRight(add, [2,3,4]);

addOne(1); //=> '1 + 2 + 3 + 4'
```

## 3. Underscore - [partial](https://underscorejs.org/#partial)

Very similar to the lodash `_.bind` method, and can be used in pretty much the same way.

> Partially apply a function by filling in any number of its arguments, without changing its dynamic this value. A close cousin of [bind](https://underscorejs.org/#bind). You may pass _ in your list of arguments to specify an argument that should not be pre-filled, but left open to supply at call-time.

```js
function subtract(a, b) { 
  return b - a;
};

const sub5 = _.partial(subtract, 5);
sub5(20);
=> 15

// Using a placeholder
const subFrom20 = _.partial(subtract, _, 20);
subFrom20(5);
=> 15
```

---
**Sources**
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind
- https://stackoverflow.com/questions/27699493/javascript-partially-applied-function-how-to-bind-only-the-2nd-parameter
- https://lodash.com/docs/4.17.15
- https://ramdajs.com/
- https://underscorejs.org/