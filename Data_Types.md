# Methods of primitives

## Summary

- Primitives except `null` and `undefined` provide many helpful methods.
- Formally, these methods work via temporary objects, but JavaScript engines are well tuned to optimize that internally, so they are not expensive to call.

# Numbers

## Summary

To write numbers with many zeroes:

- Append `"e"` with the zeroes count to the number. Like: `123e6` is the same as `123` with 6 zeroes `123000000`.
- A negative number after `"e"` causes the number to be divided by 1 with given zeroes. E.g. `123e-6` means `0.000123` (`123` millionths).

For different numeral systems:

- Can write numbers directly in hex (`0x`), octal (`0o`) and binary (`0b`) systems.
- `parseInt(str, base)` parses the string `str` into an integer in numeral system with given `base`, `2 <= base <= 36`.
- `num.toString(base)` converts a number to a string in the numeral system with the given `base`.

For converting values like `12pt` and `100px` to a number:

- Use `parseInt/parseFloat` for the "soft" conversion, which reads a number from a string and then returns the value they could read before the error.

For fractions:

- Round using `Math.floor`, `Math.ceil`, `Math.trunc`, `Math.round`, or `num.toFixed(precision)`.
- Make sure to remember there's a loss of precision when working with fractions.

More mathematical functions:

- See the Math object when you need them. The library is very small, bu can cover basic needs.

# Strings

## Summary

- There are 3 types of quotes. Backticks allow a string to span multiple lines and embed expressions `${...}`.
- Strings in JavaScript are encoded using UTF-16.
- We can use special characters like `\n` and insert letters by their unicode using `\u...`.
- To get a character, use `[]`.
- To get a substring, use: `slice` or `substring`.
- To lowercase/uppercase a string, use: `toLowerCase/toUpperCase`.
- To look for a substring, use: `indexOf`, or `includes/startsWith/endsWith` for simple checks.
- To compare strings according to the language, use: `localeCompare`, otherwise they are compared by character codes.

There are several other helpful methods in strings:

- `str.trim()` - removes ("trims") spaces from the beginning and end of the string.
- `str.repeat(n)` - repeats the string `n` times.

String also have methods for doing search/replace with regular expression.

# Arrays

## Summary

Array is a special kind of object, suited to storing and managing ordered data items.

- The declaration:

    ```javascript
    // square brackets (usual)
    let arr = [item1, item2, ...];

    // new Array(exceptionally rare)
    let arr = new Array(item1, item2, ...);
    ```

    The call to `new Array(number)` creates an array with the given length, but without elements.

- The `length` property is the array length or, to be precise, its last numeric index plus one. It is auto-adjusted by array methods.
- If we shorten `length` manually, the array is truncated.

We can use an array as a deque with the following operations:

- `push(...items)` adds `items` to the end.
- `pop()` removes the element from the end and returns it.
- `shift()` rmoeves the element from the beginning and returns it.
- `unshift(...items)` adds `items` to the beginning.

To loop over the elements of the array:

- `for (let i = 0; i < arr.length; i++)` - works fastest, old-browser-compatible.
- `for (let item of arr)` - the modern syntax for items only.
- `for (let i in arr)` - never use.

To compare arrays, don't use the `==` operator (as well as `>`, `<`, and others), as they have no special treatment for arrays. They handle them as any objects, and it's not what we usually want.

Instead you can use `for...of` to compare arrays item-by-item.

# Array methods


