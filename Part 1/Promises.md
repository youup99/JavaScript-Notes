# Introduction: callbacks

- Callback hell

# Promise

| Promises | Callbacks |
| -------- | --------- |
| Promises allow use to do things in the natural order. First, we run `loadScript(script)`, and `.then` we write what to do with the result. | We must have a `callback` function at our disposal when calling `loadScript(script, callback)`. In other words, we must know what to do with the result *before* `loadScript` is called. |
| We can call `.then` on a Promise as many times as we want. Each time, we're adding a new "fan", a new subscribing function, to the "subscription list". | There can be only one callback. |

- Promises gives us better code flow and flexibility.

# Promise chaining

If a `.then` (or `catch/finally`, doesn't matter) handler returns a Promise, the rest of the chain waits until it settles. When it does, its result (or error) is passed further.

# Error handling with Promises

## Summary

- `.catch` handles errors in promises of all kinds: be it a `reject()` call, or an error thrown in a handler.
- We should place `.catch` exactly in places where we want to handle errors and know how to handle them. The handler should analyze errors (custom error classes help) and rethrow unknown ones (maybe they are programming mistakes).
- It's ok not to use `.catch` at all, if there's no way to recover from an error.
- In any case, we should have the `unhandledrejection` event handerl (for browsers, and analogs for other environments) to track unhandled errors and inform the user (and probably our server) about them, so that our app never "just dies".

# Promise API

## Summary

There are 5 static methods of `Promise` class:

1. `Promise.all(promises)` - waits for all promises to resolve and returns an array of their results. If any of the given promises rejects, it becomes the error of `Promise.all` and all other results are ignored.
2. `Promise.allSettled(promises)` (recently added method) - waits for all promises to settle and returns their results as an array of objects with:
    - `status`: `"fulfilled"` or `"reject"`
    - `value` (if fulfilled) or `reason` (if rejected),
3. `Promise.race(promises)` - waits for the first promise to settle, and its result/error becomes the outcome.
4. `Promise.resolve(value)` - makes a resolved promise with the given value.
5. `Promise.reject(value)` - makes a rejected promise with the given error.

Of these five, `Promise.all` is probably the most common in practice.

# Promisification

# Microtasks

## Summary

Promise handling is always asynchronous, as all promise actions pass through the internal "promise jobs" queue, also called "microtask queue".

So `.then/catch/finally` handlers are always called after the current code is finished.

If we need to guarantee that a piece of code is executed after `.then/catch/finally`, we can add it into a chained `.then` call.

# Async/await

## Summary

The `async` keyword before a function has two effects:

1. Makes it always return a promise.
2. Allows `await` to be used in it.

The `await` keyword before a Promise makes JavaScript wait until that promise settles, and then:

1. If it's an error, the exception is generated - same as if `throw error` were called at that very place.
2. Otherwise, it returns the result.

Together, they provide a great framework to write asynchronous code that is easy to both read and write.

With `async/await`, we rarely need to write `promise.then/catch`, but we still shouldn't forget that they are based on Promises, because sometimes we have to use these methods. Also, `Promise.all` is nice when we are waiting for many tasks simultaneously.