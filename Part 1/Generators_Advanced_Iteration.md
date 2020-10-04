# Generators

## Summary

- Generators are created by generator functions `function* f(...) {...}`.
- Inside generators (only) there exists a `yield` operator.
- The outer code and the generator may exchange results via `next/yield` calls.

In modern JavaScript, generators are rarely used. But sometimes they come in handy, because the ability of a function to exchange data with the calling code during the execution is quite unique. And, surely, they are great for making iterable objects.

# Asyn iteration and generators

## Summary

Regular iterators and generators work fine with the data that doesn't take time to generate.

When we expect the data to come asynchronously, with delays, their async counterparts can be used, and `for await... of` instead of `for...of`.

Syntax differences between async and regular iterators:

|       | Iterable | Async Iterable |
| ----- | -------- | -------------  |
| Method to provide iterator | `Symbol.iterator` | `Symbol.asyncIterator` |
| `next()` return value is | `{value:..., done: true/false}` | `Promise` that resolves to `{value:..., done: true/false}` |

Syntax differences between async and regular generators:

|       | Generators | Async generators |
| ----- | --------   | ---------------  |
| Declaration | `function*` | `async function*` |
| `next()` return value is | `{value:..., done: true/false}` | `Promise` that resolves to `{value:..., done: true/false}` |