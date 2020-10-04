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

To add/remove elements:

- push(...items) – adds items to the end,
- pop() – extracts an item from the end,
- shift() – extracts an item from the beginning,
- unshift(...items) – adds items to the beginning.
- splice(pos, deleteCount, ...items) – at index pos delete deleteCount elements and insert items.
- slice(start, end) – creates a new array, copies elements from index start till end (not inclusive) into it.
- concat(...items) – returns a new array: copies all members of the current one and adds items to it. If any of items is an array, then its elements are taken.

To search among elements:

- indexOf/lastIndexOf(item, pos) – look for item starting from position pos, return the index or -1 if not found.
- includes(value) – returns true if the array has value, otherwise false.
- find/filter(func) – filter elements through the function, return first/all values that make it return true.
findIndex is like find, but returns the index instead of a value.

To iterate over elements:

- forEach(func) – calls func for every element, does not return anything.

To transform the array:

- map(func) – creates a new array from results of calling func for every element.
- sort(func) – sorts the array in-place, then returns it.
- reverse() – reverses the array in-place, then returns it.
split/join – convert a string to array and back.
- reduce(func, initial) – calculate a single value over the array by calling func for each element and passing an intermediate result between the calls.

Additionally:

- Array.isArray(arr) checks arr for being an array.

Please note that methods sort, reverse and splice modify the array itself.

These methods are the most used ones, they cover 99% of use cases. But there are few others:

- arr.some(fn)/arr.every(fn) checks the array.

    The function fn is called on each element of the array similar to map. If any/all results are true, returns true, otherwise false.

    We can use every to compare arrays:

    ```javascript
    function arraysEqual(arr1, arr2) {
        return arr1.length === arr2.length && arr1.every((value, index) => value === arr2[index]);
    }

    alert( arraysEqual([1, 2], [1, 2])); // true
    ```

- arr.fill(value, start, end) – fills the array with repeating value from index start to end.

- arr.copyWithin(target, start, end) – copies its elements from position start till position end into itself, at position target (overwrites existing).

# Iterables

## Summary

Objects that can be used in `for...of` are called *iterable*.

- Technically, iterables must implement the method named `Symbol.iterator`.
    - The result of `obj[Symbol.iterator]()` is called an *iterator*. It handles the further iteration process.
    - An iterator must have the method named `next()` that returns an object `{done: Boolean, value: any}`, here `done: true` denotes the end of the iteration process, otherwise the `value` is the next value.
- The `Symbol.iterator` method is called automatically by `for...of`, but we can also do it directly.
- Built-in iterables likes trings or arrays, also implement `Symbol.iterator`.
- String iterator knows aobut surrogate pairs.

Objects that have indexed properties and `length` are called *array-like*. Such objects may also have other properties and methods, but lack the build-in methods of array.s

If we look inside the specification - we'll see that most built-in methods assume that they work with iterables or array-likes of "real" arrays, because that's more abstract.

`Array.from(obj,[, mapFn, thisArg])` makes a real `Array` from an iterable or array-like `obj`,and we can then use array methods on it. The optional arguments `mapFn` and `thisArg` allow us to apply a function to each item.

# Map and Set

## Summary

`Map` – is a collection of keyed values.

Methods and properties:

- `new Map([iterable])` – creates the map, with optional `iterable` (e.g. array) of `[key,value]` pairs for initialization.
- `map.set(key, value)` – stores the value by the key.
- `map.get(key)` – returns the value by the key, `undefined` if `key` doesn’t exist in map.
- `map.has(key)` – returns `true` if the `key` exists, `false` otherwise.
- `map.delete(key)` – removes the value by the key.
- `map.clear()` – removes everything from the map.
- `map.size` – returns the current element count.

The differences from a regular `Object`:

- Any keys, objects can be keys.
- Additional convenient methods, the `size` property.

`Set` – is a collection of unique values.

Methods and properties:

- `new Set([iterable])` – creates the set, with optional `iterable` (e.g. array) of values for initialization.
- `set.add(value)` – adds a value (does nothing if `value` exists), returns the set itself.
- `set.delete(value)` – removes the value, returns `true` if `value` existed at the moment of the call, otherwise `false`.
- `set.has(value)` – returns `true` if the value exists in the set, otherwise false.
- `set.clear()` – removes everything from the set.
- `set.size` – is the elements count.

Iteration over `Map` and `Set` is always in the insertion order, so we can’t say that these collections are unordered, but we can’t reorder elements or directly get an element by its number.

# WeakMap and Weakset

## Summary

- `WeakMap` is `Map`-like collection that allows only objects as keys and removes them together with associated value once they become inaccessible by other means.
- `WeakSet` is `Set`-like collection that stores only objects and removes them once they become inaccessible by other means.
- Both of them do not support methods and properties that refer to all keys or their count. Only individual operations are allowed.
- `WeakMap` and `WeakSet` are used as "secondary" data structures in addition to the `main` object storage. Once the object is removed from the main storage, if it is only found as the key of `WeakMap` or in a `WeakSet`, it will be cleaned up automatically.

# Object.keys, values, entries

- Pretty straightforward

# Destructuring assignment

## Summary

- Destructuring assignment allows for instantly mapping an object or array onto many variables.
- The full object syntax:

    ```javascript
    let {prop: varName = default, ...rest} = object
    ```

    This means that property `prop` should go into the variable `varName` and, if no such property exists, then the `default` value should be used.

    Object properties that have no mapping are copied to the `rest` object.

- The full array syntax:
    
    ```javascript
    let [item1 = default, item2, ...rest] = array
    ```

    The first item goes to `item1`; the second goes into `item2`; all the rest makes the array `rest`.

- It's possible to extract data from nested arrays/objects, for that the left side must have the same structure as the right one.

# Date and time

## Summary

- Date and time in JavaScript are represented with the Date object. We can't create "only date" or "only time": `Date` objects always carry both.
- Months are counted from zero (yes, January is a zero month).
- Days of week in `getDay()` are also counted from zero (that's Sunday).
- `Date auto-corrects itself when out-of-range components are set. Good for adding/subtracting days/months/hours.
- Dates can be subtracted, giving their difference in milliseconds. That's because `Date` becomes the timestamp when converted to a number.
- Use `Date.now()` to get the current timestamp fast.

Note that unliked many other systems, timestamps in JavaScript are in milliseconds, not in seconds.

Sometimes we need more precise time measurements. JavaScript itself does not have a way to measure time in microseconds (1 millionth of a second), but most environments provide it. For instance, browser has performance.now() that gives the number of milliseconds from the start of page loading with microsecond precision (3 digits after the point):

# JSON methods, toJSON

## Summary

- JSON is a data format that has its own independent standard and libraries for most programming languages.
- JSON supports plain objects, arrays, strings, numbers, booleans, and `null`.
- JavaScript provides methods JSON.stringify to serialize into JSON and JSON.parse to read from JSON.
- Both methods support transformer functions for smart reading/writing.
- If an object has `toJSON`, then it is called by `JSON.stringify`.

