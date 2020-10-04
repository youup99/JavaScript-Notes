# Class basic syntax

## Summary

The basic class syntax looks like this:

```javascript
class MyClass {
    prop = value; // property

    constructor(...){ // constructor
        // ...
    }

    method(...) {} // method

    get something(...) {} // getter method
    set something(...) {} // setter method

    [Symbol.iterator]() {} // method with computed name (symbol here)
}
```

`MyClass` is technically a function (the one that we provide as `constructor`), while methods, getters and setters are written to `MyClass.prototype`.

# Class inheritance

## Summary

1. To extend a class: `class Child extends Parent`:
    - That means `Child.prototype.__proto__` will be `Parent.prototype`, so methods are inherited.
2. When overriding a constructor:
    - We must call parent constructor as `super()` in `Child` constructor before using `this`.
3. When overriding another method:
    - We can use `super.method()` in a `Child` method to call `Parent` method.
4. Internals:
    - Methods remember their class/object in the internal `[[HomeObject]]` property. That's how `super` resolves parent methods.
    - So it's not safe to copy a method with `super` from one object to another.

Also:

- Arrow functions don't have their own `this` or `super`, so they transparently fit into the surrounding context.

# Static properties and methods

## Summary

Static methods are used for the functionality that belongs to the class "as a whole". It doesn't relate to a concrete class instance.

For example, a method for comparison `Article.compare(article1, article2)` or a factory method `Article.createTodays()`.

They are labeled by the word `static` in class declaration.

Static properties are used when we'd like to store class-level data, also not bound to an instance.

The syntax is:

```javascript
class MyClass {
    static property = ...;

    static method() {
        ...
    }
}
```

Technically, static declaration is the same as assigning to the class itself:

```javascript
MyClass.property = ...
MyClass.method = ...
```

Static properties and methods are inherited.

For `class B extends A`, the prototype of the class `B` itself point to `A`: `B.[[Prototype]] = A`. So if a field is not found in `B`, the search continues in `A`.

# Private and protected properties and methods

In terms of OOP, delimiting of the internal interface from the external one is called encapsulation.

It gives the following benefits:

**Protection for users, so that they don’t shoot themselves in the foot**

Imagine, there’s a team of developers using a coffee machine. It was made by the “Best CoffeeMachine” company, and works fine, but a protective cover was removed. So the internal interface is exposed.

All developers are civilized – they use the coffee machine as intended. But one of them, John, decided that he’s the smartest one, and made some tweaks in the coffee machine internals. So the coffee machine failed two days later.

That’s surely not John’s fault, but rather the person who removed the protective cover and let John do his manipulations.

The same in programming. If a user of a class will change things not intended to be changed from the outside – the consequences are unpredictable.

**Supportable**

The situation in programming is more complex than with a real-life coffee machine, because we don’t just buy it once. The code constantly undergoes development and improvement.

If we strictly delimit the internal interface, then the developer of the class can freely change its internal properties and methods, even without informing the users.

If you’re a developer of such class, it’s great to know that private methods can be safely renamed, their parameters can be changed, and even removed, because no external code depends on them.

For users, when a new version comes out, it may be a total overhaul internally, but still simple to upgrade if the external interface is the same.

**Hiding complexity**

People adore using things that are simple. At least from outside. What’s inside is a different thing.

Programmers are not an exception.

**It’s always convenient when implementation details are hidden, and a simple, well-documented external interface is available.**

To hide an internal interface we use either protected or private properties:

- Protected fields start with `_`. That’s a well-known convention, not enforced at the language level. Programmers should only access a field starting with `_` from its class and classes inheriting from it.
- Private fields start with `#`. JavaScript makes sure we can only access those from inside the class.

Right now, private fields are not well-supported among browsers, but can be polyfilled.

# Extending built-in classes

- Pretty straightforward

# Class checking: "instanceof"

|    | works for | returns |
| ----| -----| ----|
| `typeof` | primitives | string |
| `{}.toString` | primitives, built-in objects, objects with `Symbol.toStringTag` | string |
| `instanceof` | objects | true/false |

As we can see, `{}.toString` is technically a "more advanced" `typeof`.

And `instanceof` operator really shines when we are working with a class hierarchy and want to check for the class taking into account inheritance.

# Mixins

## Summary

*Mixin* - is a generic object-oriented programming term: a class that contains methods for other classes.

Some other languages allow multiple inheritance. JavaScript does not support multiple inheritance, but mixins can be implemented by copying methods into prototype.

We can use mixins as a way to augment a class by adding multiple behaviours, like event-handling as we have seen above.

Mixins may become a point of conflic if they accidentally overwrite existing class methods. So generally one should think well about the naming methods of a mixin, to minimize the probability of that happening.