# Recursion and stack

## Summary

Terms:

- *Recursion* is a programming term that means calling a function from itself. Recursive functions can be used to solve tasks in elegant ways.
    
    When a function calls itsself, that's called a *recursion step*. The *basis* of recursion is function arguments that make the task so simple that the function does not make further calls.

- A recursively-defined data structure is a data structure that can be defined using itself.

    For instance, the linked list can be defined as a data structure consisting of an object referencing a list (or null).

    ```javascript
    list = { value, next -> list}
    ```

    Trees like HTML elements tree or the department tree from this chapter are also naturally recursive: they branch and every branch can have other branches.

    Recursive functions can be used to walk them as we've seen in the `sumSalary` example.

Any recursive function can be rewritten into an iterative one. And that's sometimes required to optimize stuff. But for many tasks a recursive solution is fast enough and easier to write and support.

# Rest parameters and spread syntax

## Summary

When we see `"..."` in the code, it is either rest parameters or the spread syntax.

There's an easy way to distinguish between them:

- When `...`  is at the end of function parameters, it's "rest parameters" and gathers the rest of the list of arguments into an array.
- When `...` occurs in a function call or alike, it's called "spread syntax" and expands an array into a list.

Use patterns:

- Rest parameters are used to create functions that accept any number of arguments.
- The spread syntax is used to pass an array to functions that normally require a list of many arguments.

Together they help to travel between a list and an array of parameters with ease.

All arguments of a function call are also available in "old-style" `arguments`: array-like iterable object.

# Variable scope, closure

## Summary

Lexical Environment:

1. *Environment Record* - an object that stores all local variables as its properties (and some other information like the value of `this`).
2. A reference to the *outer lexical environment*, the one associated with the outer code.

    A variable is just a property of the Environment Record.

- Closure - all functions are naturally closures, they automatically remember where they were created using a hidden `[[Environment]]` property, and then their code can access outer variables.

# The old "var"

## Summary

There are two main differences of `var` compared to `let/const`:

1. `var` variables have no block scope; their visibility is scoped to current function, or global, if declared outside function.
2. `var` declarations are processed at function start (script start for globals).

These differences make `var` worse than `let` most of the time. Block-level variables is such a great thing. That's why `let` was introduced in the standard long ago, and is now a major way (along with `const`) to declare a variable.

# Global object

## Summary

- The global objects hold variables that should be available everywhere.

    That includes JavaScript built-ins, such as `Array` and environment-specific values, such as `window.innerHeight` - the window height in the browser.

- The global object has a universal name `globalThis`.

    ...But more often is referred by "old-school" environment-specific names, such as `window` (browser) and `global` (Node.js).

- We should store values in the global object if they're truly global for our project. And keep their number at minimum.
- In-browser, unless we're using modules, global functions and variables declared with `var` become a property of the global object.
- To make our code future-proof and easier to understand, we should access properties of the global object directly, as `window.x`.

# Function object, NFE

## Summary

Functions are objects.

Here we covered their properties:

- `name` - the function name. Usually taken from the function definition, but if there's none, JavaScript tries to guess it from context (e.g. an assignment).
- `length` - the number of arguments in the function definition. Rest parameters are not counted.

If the function is declared as a Function Expression (not in the main code flow), and it carries the name, then it is called a Named Function Expression. The can be used inside to reference itself, for recursive calls or such.

Also, functions may carry additional properties. Many well-know JavaScript libraries make great use of this feature.

# The "new Function" syntax

## Summary

The syntax:

```javascript
let func = new Function ([arg1, arg2, ...argN], functionBody);
```

For historical reasons, arguments can also be given as a comma-seperated list.

These three declarations mean the same:

```javascript
new Function('a', 'b', 'return a + b'); // basic syntax
new Function('a,b', 'return a + b'); // comma-seperated
new Function('a, b', 'return a + b'); // comma-seperated with spaces
```

Functions created with `new Function`, have `[[Environemnt]]` referencing the global Lexical Environment, not the outer one. Hence, they cannot use outer variables. But that's actually good, because it insures us from errors. Passing parameters explicitly is a much better method architecturally and causes no problems with minifiers.

# Scheduling: setTimeout and setInterval

## Summary

- Methods `setTimeout(func, delay, ...args)` and `setInterval(func, delay, ...args)` allow us to run the `func` once/regularly after `delay` milliseconds.
- To cancel the execution, we should call `clearTimeout/clearInterval` with the value returned by `setTimeout/setInterval`.
- Nested `setTimeout` calls are a more flexible alternative to `setInterval`, allowing us to set the time between executions more precisely.
- Zero delay scheduling with `setTimeout(func, 0)` (the same as `setTimeout(func)`) is used to schedule the call "as soon as-possible, but after the current script is complete".
- The browser limits the minial delay for five or more nested call of `setTimeout` or for `setInterval` (after 5th call) to 4ms. That's for historical reasons.

Please note that all scheduling methods do not *guarantee* the exact delay.

For example, the in-browser timer may slow for alot of reasons:

- The CPU is overloaded.
- The browser tab is in the background mode.
- The laptop is on battery.

All that may increase the minimal timer resolution (the minimal delay) to 300ms or even 1000ms depending on the browser and OS-level performance settings.

# Decorators and forwarding, call/apply

## Summary

*Decorator* is a wrapper around a function that alters its behavior. The main job is still carried out by the function.

Decorators can be seen as "features" or "aspects" that can be added to a function. We can add one or add many. And all this without changing its code!

To implement `cachingDecorator`, we studied methos:

- func.call(context, arg1, arg2...) - calls `func` with given context and arguments.
- func.apply(context, args) - calls `func` passing `context` as `this` and array-like `args` into a list of arguments.

The generic *call forwarding* is usually done with `apply`:

```javascript
let wrapper = function() {
    return original.apply(this, arguments);
};
```

We also saw an example of *method borrowing* when we take a method from an object and `call` it in the context of another object. It is quite common to take array methods and apply them to `arguments`. The alternative is to use rest parameters objects that is a real array.

# Function binding

## Summary

Method `func.bind(context, ...args)` returns a "bound variant" of function `func` that fixes the context `this` and first arguments if given.

Usually we apply `bind` to fix `this` for an object method, so that we can pass it somewhere. For example, to `setTimeout`.

When we fix some arguments of an existing function, the resulting (less universal) function is called *partially applied* or *partial*.

Partials are convenient when we don't want to repeat the same argument over and over again. Like if we have a `send(from, to)` function, and `from` should always be the same for our task, we can get a partial and go on with it.

# Arrow functions revisited

## Summary

Arrow functions:

- Do not have `this`
- Do not have `arguments`
- Can't be called with `new`
- They also don't have `super`, but we didn't study it yet.

That's because they are meant for short pieces of code that do not have their own "context", but rather work in the current one. And they really shine in that use case.