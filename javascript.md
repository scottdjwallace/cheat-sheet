# javascript

A cheatsheet containing ES2015 [ES6] tips, tricks, best practices and code
snippet examples for your day to day workflow.

## Table of Contents

- [var versus let / const](#var-versus-let--const)
- [Destructuring](#destructuring)
- [Strings](#strings)
- [Classes](#classes)
- [Generator Functions](#generator-functions)
- [Arrow Functions](#arrow-functions)
- [Modules](#modules)
- [Async Functions](#async-functions)

## var versus let / const

> Besides `var`, we now have access to two new identifiers for storing values
—`let` and `const`. Unlike `var`, `let` and `const` statements are not hoisted
to the top of their enclosing scope.

An example of using `var`:

```javascript
var snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        var snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // undefined
```

However, observe what happens when we replace `var` using `let`:

```javascript
let snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        let snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // 'Meow Mix'
```

This change in behavior highlights that we need to be careful when refactoring
legacy code which uses `var`. Blindly replacing instances of `var` with `let`
may lead to unexpected behavior.

> **Note**: `let` and `const` are block scoped. Therefore, referencing
block-scoped identifiers before they are defined will produce
a `ReferenceError`.

```javascript
console.log(x); // ReferenceError: x is not defined

let x = 'hi';
```

> **Best Practice**: Leave `var` declarations inside of legacy code to denote
that it needs to be carefully refactored. When working on a new codebase, use
`const` first, and then change to `let` if the variable needs to be reassigned.

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Destructuring

An example of destructuring an object: 
```javascript
let foo = {
  bar: 1,
  baz: 2
};

var { bar, baz } = foo; // now have two variables bar, baz
console.log(bar); // = 1
console.log(baz); // = 2
```

An example of destructuring an array: 
```javascript
let foo = [ 1, 2, 3 ];

var [ bar, baz ] = foo;

console.log(bar); // = 1
console.log(baz); // = 2
```

> When would this ever be useful? This can be quite useful when dealing with a function with lots of parameters, we can use this to allow for a random order of parameters given, and any of them being optional. This is a good way of making the function easy to use and less error prone.

Here is an example of a function that is tightly coupled to the order of the parameters: 

```javascript
function calcBmi(weight, height, max, callback) {
  var bmi = weight / Math.pow(height, 2);
  if (bmi > max) {
    console.log("hit the gym");
  }
  if (callback) {
    callback(bmi);
  }
}

calcBmi(weight, height, null, function() {});
```

Here is an example that uses destructuring to avoid this: 

```javascript
// Note: we can give max a default value now, all parameters can be optional, given in any order and we can rename the parameters as well
function calcBmi({ weight: w, height: h, max = 25, callback }) {
  const bmi = w / Math.pow(h, 2);
  if (bmi > max) {
    console.log("overweight");
  }
  if (callback) {
    callback(bmi);
  }
}

// no need to give a max, no order required
calcBmi({ weight, height, callback: function() {} });
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Strings
> This is one of the best parts of ES6. No longer need the long + " strings " + soundGood + "?";

Before: 
```javascript
var name = "Scott";
var action = "code";
var greet = "hi, my name is " + name +
            "\n and I like to" + action + "!";
```

After:
```javascript
var name = "Scott";
var action = "code";
var greet = `hi, my name ${name} and I like to ${action}!`;
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Classes

> JavaScript, now with Classes...

```javascript
//  Properties and static Properties were added in ES7
class Parent() {
  age = 34;
  constructor() {}
  static foo() {}
  bar() {}
}

var parent = new Parent();
console.log(parent.age); // = 34

class Child extends Parent {
  constructor() {
    super()
  }
  baz() {}
}

var child = new Child();
child.baz();
child.bar();
child.foo(); // doesn't work, since foo is static
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Generator Functions
> What is a generator function? Its a pausable / iterable function

Let's start with a basic example of a generator function:

```javascript
var myGen = function*() {       // Note the * is what lets us know it is a generator function
  var one = yield 1;            // the 1 gets passed to the first gen.next() call
  var two = yield 2;
  var three = yield 3;
  console.log(one, two, three); // 1,2,3
};
var gen = myGen();              // get the generator ready to run

console.log(gen.next());        // {value: 1, done: false}
console.log(gen.next(1));       // {value: 2, done: false}
console.log(gen.next(2));       // {value: 3, done: false}
console.log(gen.next(3));       // {value: undefined, done: true}
console.log(gen.next());        // error, cant call next() on an closed generator
```

> Now why would we want to use this? Well we can use this to create Async code, which can be quite useful when working on a NodeJS app

```javascript
var myGen = function*() {
  var one = yield $.get('api/friends'); // returns 'scott'
  var two = yield $.get('api/profile'); 
  var three = yield $.get('api/tweets');
  console.log(one, two, three); // 1,2,3
};

function smartCode(generator) {

  var gen = generator();
  //get first yieledValue
  var yieldedValue = gen.next();
  // if its a promise, wait for it to fulfill and pass the value back into the generator
  if (yieldedValue.then) {
    yieldedValue.then(gen.next);
  }
}
```

Well we can't be expected to a smartCode() function to every promise right? That would be a waste of my precious dev time.
You're right! Instead we can use one of the many pre-existing libraries in JS. Let's look at some options.

Bluebird - client side
co - NodeJS
q - Angular

Let's look at using Bluebird:

```javascript
Promise.coroutine(function* () {
  var tweets = yield $.get('tweets.json');
  var tweets = yield $.get('profile.json');
  console.log(tweets, profile);
})

// coroutine does the same as smartCode function above
// bluebird can yield objects or arrays
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Arrow Functions

> Arrow functions are a new short form that can be quite useful when interacting with underscore.js

Take a look at this ES5 implementation:

```javascript
var foo = function(a,b) {
  return a + b;
}
```

Now take a look at what the same code with arrow notation:

```javascript
var foo = (a,b) => {
  return a + b;
}
```

> Not very useful in that example, but more complicated examples it does become clear of the benefit.

This can be shortened even further: 

```javascript
do.something((a, b) => { return a + b; });
```

And even further for one-liners:

```javascript
// implicit returns
do.something((a,b) => a + b);

// only with one parameter
do.something(a => a++);
```

Now that we've gotten the basic use the syntax out of the way, we can look at an example with underscore.js. An arrow function can be used to really shorten the code.

```javascript
var foo = [0,1,2].map(val => val++);

console.log(foo); // = [1, 2, 3]
```

Let's look at another example, this time the focus will be on the scope and the use of "this".

Before:
```javascript
var module = {
  age: 30,
  foo: function() {
    setTimeout(function() {
      console.log(this.age);
    }.bind(this), 100);
  }
};
```

See how the .bind(this) is required to get the code to work? Now look at the same code with an arrow function:
```javascript
var module = {
  age: 30,
  foo: function() {
    setTimeout(() => {
      console.log(this.age);
    }, 100);
  }
};
```

> It's beautiful

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Modules
> ES6 introduces some nice ways to import modules now

myModule.js

```javascript
module.exports.foo = function() {};
module.exports.bar = function() {};

export var foo = 3;
export function bar() {}
export default {};
```

app.js

```javascript
// before
var myModule = require("myModule");
var foo = myModule.foo;

//after
import myModule from "myModule";
import { foo, bar } from "myModule"; // just the functions you need
import { foo as foolish, bar } from "myModule"; // can also rename functions too
console.log(foolish); // = 3
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Async Functions
> We now have async functions too, which returns the whole function as a promise that is then-able. Similar to generator functions, the special keyword here is "await".

```javascript
async function() {
  var friends = await $.get("api/friends"); // somePromise
  console.log(friends);
}
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Sources
* [DrkSephy/es6-cheatsheet](https://github.com/DrkSephy/es6-cheatsheet/blob/master/README.md)
* [LearnCode.academy YouTube Videos](https://www.youtube.com/watch?v=AfWYO8t7ed4)
