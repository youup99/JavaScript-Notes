# Prototypal inheritance

## Summary

- In JavaScript, all objects have a hidden `[[Prototype]]` property that's either another object or `null`.
- We can use `obj.__proto__` to access it (a historical getter/setter, there are other ways, to be covered soon).
- The object referenced by `[[Prototype]]` is called a "prototype".
- If we want to read a property of `obj` or call a method, and it doesn't exists, then JavaScript tries to find it in the prototype.
- Write/delete operations act directly on the object, they don't use the prototype (assuming it's a data property, not a setter).
- If we call `obj.method()`, and the `method` is taken from the prototype, `this` still references `obj`. So methods always work with the current object even if they are inherited.
- The `for...in` loop iterates over both its own and its inherited properties. All other key/value-getting methods only operate on the object itself.

# F.prototype

## Summary

In this chapter, we briefly discussed the way of setting a [[Prototype]] for objects created via a constructor function. Later we'll see more advanced programming patterns that rely on it.

Everything is quite simple, just a few notes to make things clear:

- The `F.prototype` property (don't mistake it for `[[Prototype]]`) sets `[[Prototype]]` of new objects when `new F()` is called.
- The value of `F.prototype` should be either an object or `null`: other values won't work.
- The `"prototype"` property only has such a special effect when set on a constructor function, and invoked with `new`.

On regular objects, the `prototype` is nothing special:

By default, all functions have `F.prototype = {constructor: F}`, so we can get the constructor of an object by accessing its `constructor` property.

# Native prototypes

## Summary

- All built-in objects follow the same pattern:
    - The methods are stored in the prototype (`Array.prototype`, `Object.prototype`, `Date.prototype`, etc).
    - The object itself stores only the data (array items, objects properties, the date)
- Primitives also store methods in prototypes of wrapper objects: `Number.prototype`, `String.prototype`, and `Boolean.prototype`. Only `undefined` and `null` do not have wrapper objects.
- Built-in prototypes can be modified or populated with new methods. But it's not recommended to change them/ The only allowable case is probably when we add-in a new standard, but it
s not yet supported by the JavaScript engine.

# Prototype methods, objects without __proto__

## Summary

Modern methods to set up and directly access the prototype are:

- Object.create(proto, [descriptors]) - creates an empty object with a given `proto` as `[[Prototype]]` (can be `null`) and optional property descriptors.
- Object.getPrototypeOf(obj) - returns the `[[Prototype]]` of `obj` (same as `__proto__` getter).
- Object.setPrototypeOf(obj, proto) - sets the `[[Prototype]]` of `obj` to `proto` (same as `__proto__` setter).

The built-in `__proto__` getter/setter is unsafe if we'd want to put user-generated keys into an object. Just because a user may enter `"__proto__"` as the key, and there'll be an error, with hopefully light, but generally unpredictable consequences.

So we can either use `Object.create(null)` to create a "very plain" object without `__proto__`, or stick to `Map` objects for that.

Also, `Object.create` provides an easy way to shallow-copy an object with all descriptors.

```javascript
let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
```

We also made it clear that `__proto__` is a getter/setter for `[[Prototype]]` and resides in `Object.prototype`, just like other methods.

We can create an object without a prototype by `Object.create(null`). Such objects are used as "pure dictionaries", they have no issues with `""__proto__"` as the key.

Other methods:

- Object.keys(obj) / Object.values(obj) / Object.entries(obj) – returns an array of enumerable own string property names/values/key-value pairs.
- Object.getOwnPropertySymbols(obj) – returns an array of all own symbolic keys.
- Object.getOwnPropertyNames(obj) – returns an array of all own string keys.
- Reflect.ownKeys(obj) – returns an array of all own keys.
- obj.hasOwnProperty(key): returns true if obj has its own (not inherited) key named key.

All methods that return object properties (like Object.keys and others) – return “own” properties. If we want inherited ones, we can use for..in.