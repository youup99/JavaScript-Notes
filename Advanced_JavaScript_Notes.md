## Super Important

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/33a88e56-3495-49f5-a2c5-5725269b022b/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/33a88e56-3495-49f5-a2c5-5725269b022b/Untitled.png)

- **Closures** are commonly used for data privacy, currying, and callbacks
    - Basically, a function in JS has access to its own variables, and that of its surrounding scope (by reference), even if its surrounding scope is a function that's returned. E.g. below:

    ```jsx
    var counter = (function() {
      var privateCounter = 0;
      function changeBy(val) {
        privateCounter += val;
      }
      return {
        increment: function() {
          changeBy(1);
        },
        decrement: function() {
          changeBy(-1);
        },
        value: function() {
          return privateCounter;
        }
      };
    })();

    console.log(counter.value()); // logs 0
    counter.increment();
    counter.increment();
    console.log(counter.value()); // logs 2
    counter.decrement();
    console.log(counter.value()); // logs 1
    ```

    - So, we can have an outer function **return inner function(s)**, and when we call those via the variable we set it to, those functions can access the lexical environment they were created in
    - Currying is the process of taking a function with multiple arguments and instead returning a series of functions that each take one argument and eventually resolve to a value, e.g.:

        ```jsx
        function volume1(length) {
          return function(width) {
            return function(height) { // this is also an example of a closure
              return height * width * length; // bc they use outer params
            }
          }
        }

        volume1(2)(3)(4); // 24
        // we curried volume(l, w, h)
        ```

        - Currying is a tool used in functional programming used to minimize side effects (single responsibility per function)
- **map vs forEach():**
    - Array.prototype.forEach() is an array iterator that executes a function (provided by you, the dev) once per each element within a given array.
        - return doesn't do anything when used in forEach (so, it's usually not used as a pure function, as if you use it to modify anything, it must be used by mutating state)
    - Array.prototype.map returns 1 value for every element in the array
    - For arrays we can use `for .. of` loops; for objects we use `for .. in` for KEYS
- **this keyword**
    - `this` in an object refers to the instance of the object that called the function where `this` is used
    - `this` is not bound to a specific object (unlike most languages).
        - E.g. `this` in a global function works, but is the global object/window
        - the value of `this` is evaluated at runtime, so a single function could be used across multiple objects, but this does introduce room for bugs
        - E.g. a function uses `this`, and you have it on an object `user` and an object `square`, well it'll work fine even though user and square are super different (as long as they both have whatever property the function references, say `[this.name](http://this.name)` or something
        - Bug example: `let hi = user.hi;` `hi()` , where `user.hi` uses `this`, fails bc when you call `hi()`, `this` is undefined because you're not calling it directly from an object
            - An example of when this matters is if you use an object method as an argument to say, an array function like `.map`, but for those you can specify a `this` usually
            - So, `this` only works when you directly call object methods, unless you `bind`
        - Arrow functions don't have a `this` keyword, so `this` comes from the `this` of the outer function (similarly, arrow functions don't have an `arguments` object, which means you can forward `arguments` really easily from an outer function
            - Useful for stuff like `this.values.filter( x=> x < this.max)` , `this` is the "correct" `this` (the outer `this`)
                - If you used a regular function inside, it would lose `this` and be set to window
            - This is due to JavaScript's lexical scoping with function scope, where the `this` variable is bound to the function, not the block - arrow functions are just considered a block
            - Note that this also applies to arrow functions used with closures, e.g.

                ```jsx
                const myObject = {
                  myArrowFunction: null,
                  myMethod: function () {
                    this.myArrowFunction = () => { console.log(this) };
                  }
                };
                const myArrowFunction = myObject.myArrowFunction;
                myArrowFunction() // this === myObject 
                ```

            - (myArrowFunction() inherits scope from when it was created, which was when `this` was in scope for myObject.myMethod)
- **Lexical Environment/Scope**:
    - Every script, function and code block have a lexical environment, accessed through the property `[[Environment]]` (created upon function creation) containing their own local variables (as object properties of the *Environment Record)* and a reference to the outer lexical environment
    - `this` and `var` get function scope (scope of its function's lexical environment)
    - `let` and `const` get block scope
    - **This is why all functions in JavaScript are closures - they all remember their outer variables and can access them.**
    - **let vs var:**
        - let is scoped to block, var is scoped to function
        - let avoids the reference issue when it comes to closures (var is bound to the function, so you end up modifying the same var variable when using closures in a loop; let is bound to the block - the loop, so there's a new one every time the loop runs)
        - var declaration (not assignment) is hoisted, let is not
        - global vars become property of the global object/window
- Nested **setTimeout** is more flexible than setInterval, bc you could say, use a condition to determine whether or how long to set the delay for
    - It's also more precise, in that setInterval will run perfectly on the dot of its interval, but it doesn't take into account execution time of the function. Nested setTimeout will only set the timeout after the function finishes, so it will actually be the right amount of time between the end of one function and the start of another
    - Zero-delay setTimeout runs as soon as possible, but only after the currently executing script is complete - e.g.

    ```jsx
    setTimeout(() => alert("World")); // alerts second
    alert("Hello"); // alerts first
    ```

    - The in-browser timer is not always accurate, and can get slowed down (e.g. if CPU is overloaded or it can't always run as much as it needs)
- Decorators and Forwarding, **call/apply**
    - Decorator functions take in another function, and alter/wrap their behaviour
    - E.g. we could create a function cachingDecorator that checks if the input of its given function can be found in its `Map`, and if yes, returns that instead of bothering to call
    - This way, we avoid modifying or adding extra behaviour to a given function, but still get to extend that original behaviour
    - However, this has problems if we try to use `this`, because we won't be calling it directly from the object anymore (assume the argument to the decorator is like `user.func`)
    - The solution is to use `Function.prototype.call(context, arg1, arg2, ...)`

        ```jsx
        function say(phrase) {
          alert(this.name + ': ' + phrase);
        }

        let user = { name: "John" };

        say.call( user, "Hello" ); // John: Hello
        ```

    - Could also have `say` as a method of `user`, and do `user.say.call(user, "Hello")`
    - Now, `call()` takes in exactly the arguments you want, so if you have a variable number of arguments, you'll need to either spread the list of arguments, or use `apply()` which already uses a list as its parameters (`Function.prototype.apply(context, args)`)
        - **Difference is that `call(...args)` works for all iterables, but `apply(args)` expects an array-like argument**
        - Call and apply are examples of *call-forwarding*, because we pass along context and args
- **When you pass object methods as callbacks, you lose "this"**, because the thing calling the callback calls the argument you passed, not the object method directly (e.g. it calls the argument `func`, not `[user.foo](http://user.foo)` which is what you passed to it.
    - Now, you could fix that by wrapping the function and call it directly inside that one, like `setTimeout(() => user.sayHi(), 1000);`, but what if `user` changes value at some point?
    - So, we can use `bind` to bind an instance of a function to context, like `setTimeout(() => user.sayHi.bind(user), 1000);` or `let sayHi = user.sayHi.bind(user)` basically binding a reference to the function, to the correct `this` it otherwise wouldn't get.
    - You can also bind arguments, like `bind(context, arg1, arg2...)` (basically like setting default values for those arguments, but not with the original function)

        ```jsx
        function mul(a, b) {
          return a * b;
        }
        let double = mul.bind(null, 2);

        alert( double(3) ); // = mul(2, 3) = 6
        ```

    - This is called partial function application - we create a new function by fixing params of an existing function - useful for extending/modifying behaviour w/o changing original
        - maybe you want multiple variations of a function
- Maps allow keys of any type, most notably objects
    - (objects can't use objects as keys, they would convert them via toString(), which is usually inherited from `Object.prototype`, which converts them to [object Object])
    - Using an object as a key means that you have to use the same object (can be any reference tho) to look it up
    - We can create Maps from iterables that are of [key, value] pairs (e.g. `Object.entries(obj)`
    - Sets are like Maps but we only store values, and they're unique
    - **WeakMap and WeakSet** are like Map and Set, but keys for Map and entries for Set must be objects, because the Weak versions ALLOW for garbage collection (the weak data structure does not count as reachability, that's why it's weak)
        - The weak versions don't allow for iteration or sizem, bc shit might have gotten gc'd
        - Weak data structures are good for when we want to hold an object, but only while some other code controls its lifecycle; also good for caching where if we don't need it anymore, we let it get garbagecollected
    - Map keys are ordered by insertion, whereas objects are ordered with integer indicies in ascending order, then all other string keys in insertion order
- Prototypal Inheritance:
    - **When we want to read a property from an object, and it’s missing, JavaScript automatically takes it from its prototype. This is prototypal inheritance.**
    - **Prototypes form the prototype chain, a linked list of inheritance**
    - However, we can't have circular references for prototypes - that throws an error.
    - `[[Prototype]]`is the hidden property for this, accessed using `Object.getPrototypeOf` and `Object.setPrototypeOf`, or the older __proto__ property.
        - Also `Object.create(proto, [descriptors])`

        ```jsx
        let animal = {
          eats: true
        };

        // create a new object with animal as a prototype
        // and the "jumps" property
        let rabbit = Object.create(animal, {
          jumps: {
            value: true
          }
        });

        alert(rabbit.eats); // true
        alert(rabbit.jumps); // true

        alert(Object.getPrototypeOf(rabbit) === animal); // true
        ```

    - Prototypal inheritance doesn't affect `this` - if `admin` inherits from `user`, and we call `admin.inheritedMethod`, `this` is `admin`
    - `for..in` iterates over inherited properties too
    - Can be used with constructor functions too, like this:

    ```jsx
    let animal = {
      eats: true
    };

    function Rabbit(name) {
      this.name = name;
    }

    Rabbit.prototype = animal; // when new (only ones after this line) 
    													 // Rabbits are created, 
    													 // their prototype will be animal

    let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal

    alert( rabbit.eats ); // true
    ```

    - So, function `prototype` is good for object-oriented programming in JS.
    - A function's `prototype` property is NOT the same as an Object's `[[Prototype]]`! We clearly see this in the example directly above.
    - To add class methods, you can do `Rabbit.prototype.foo = function()...`
        - Note that this means one function is shared across ALL `Rabbit` instances (unlike defining the function in the constructor)
        - Don't use arrow functions for this, we want a regular function that binds `this` properly from the call, e.g. `rabbit.foo()` sets`rabbit` as this, which is what you want, and you need a regular function for that
    - `Object.prototype` is an example of a constructor function's prototype being used. (`Object` is a constructor function)
- Prototypal vs Class Inheritance:
    - A prototype is a working object instance. Objects inherit directly from other objects.
    - With class inheritance, blueprints (classes) inherit from other blueprints
    - **This means that with prototypal inheritance in JS, inheritance can change on-the-fly at runtime and is more flexible**
        - Prototypes can change at runtime - both in the sense of modifying the existing prototype, as well as just changing what object one inherits from.
    - It's simpler in the sense that we only have to work with prototypes, less abstractions -
        - Classes are an abstraction of an object, with is already an abstraction of a real-life thing we're trying to model.
        - In JavaScript, the prototypal inheritance model means that objects are abstractions of a real-life thing, without classes (ES6 Classes are syntactical sugar on prototypal inheritance).
- ES6 Classes
    - The constructor is really just a regular constructor function (let's call it `Foo`), and then the class methods and properties are stored in `Foo.prototype`

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/aa195c5f-8a05-4ff6-ae1d-9d8b71b70cec/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/aa195c5f-8a05-4ff6-ae1d-9d8b71b70cec/Untitled.png)

        Remember that the default function prototype refers to itself as `constructor`

    - W/ classes, calling the class constructor `User()` without `new` gives an error unlike regular function constructors which just return w/e the constructor returns (typically `undefined`)
    - **I'd be comfortable using the older Function constructor + prototype method, as well as the newer class syntax. It depends on the standards set by the team, although I do prefer the class syntax as it's more concise - it's easier to do everything in the class rather than make one thing for the function constructor and another to add methods and such to the prototype.**
    - JavaScript class inheritance is really just a wrapper on prototype inheritance - `Rabbit extends Animal` means `Rabbit.prototype.[[Prototype]] = Animal.prototype`
        - So, given `const rabbit = new Rabbit()`, accessing properties of `rabbit` first checks `rabbit`, then its prototype, so `Rabbit.prototype`, then `Rabbit`'s prototype so `Animal.prototype`.
    - You don't just have to extend classes in Javascript, you can even extend functions and variables (since `extends` just sets the function prototype in JS)
    - Access parent using `super`, like `super()` for constructor and `super.method()` for methods
        - First line of constructor in child classes must be `super(parentParams)`
        - `super.method()` is useful in overriding methods to get parent behaviour
    - Arrow functions have no `super`, so they use the outer function one (like `this`)
    - Note that methods for classes are `method()` not `method: function()` bc it's not an object literal
    - You can have static methods and properties, similar to other languages
        - Just define them in the class, outside of non-static methods
        - The way they work is instead of like doing `[MyClass.proto.property](http://myclass.proto.property)` it's `[MyClass.property](http://myclass.property)` (kinda like attaching properties to a function, which normally doesn't do anything but here it does
        - Remember don't try to access static stuff from an object, use a class
    - `obj instanceof Class` works if obj is an instance of `Class`, or if obj's class inherits from `Class` (works with constructor functions too)
        - We can customize this at the Class level with `static [Symbol.hasInstance](obj) {...}`
            - Without this symbol (so usually), it'll check whether `Class.prototype` is in `obj`'s prototype chain.
- **Promises and async/await**
    - Promises arose because callbacks get really messy, as sometimes you have to have callbacks nested several layers deep.
    - Explaining it to a five year old - come up with some task that will take some time, and you want to notify people when it's done - say, you're baking cookies and you want your friends to know when they're ready, right away, so they don't get cold. A promise is a tool in JavaScript that lets you (the producer) do some task (like baking) (the executor), and notify your friends (or whoever is waiting) (the consumer) once the task is finished. Even if the cookies burn because you made a mistake, your friends will know - they definitely won't miss the cookies, and **they won't have to keep checking and asking if they're done yet.**

    ```jsx
    let promise = new Promise(function(resolve, reject) {
      if (success) {
    		resolve(value);
    	} else {
    		reject(new Error("BLAH"));
    	}
    });
    ```

    - There can only be one call to resolve OR reject btw (others get ignored)
    - Internally, a promise object has state (pending, fulfilled or rejected), and a result (value or error)
        - value gets passed to then's callback argument, error gets passed to catch's
        - `.then`'s second argument is actually a catching function, so we could use that instead of catch (`.catch` is like `.then(null, catchingFunc)`)
            - .catch at the very end of the chain is usually preferable, since it can handle errors anywhere in the chain
    - Consumers work by using `.then`, `.catch`, and `.finally` (all async) which take in callbacks
        - .finally's handler takes no arguments - results and errors are passed to the next handler
        - If the result is there before we set the consumer handler, the consumer still gets it
            - (unlike the baking analogy, they can subscribe to the timer "late" and still get notified)
        - Since they're async, these always get called after the current code (whatever that is, however long that is) is finished
    - Promises follow natural order better than callbacks (the easier to follow "do this then that" flow)
        - We can chain `.then`'s to each other, so that when we `return` inside of a `.then` handler, that gets passed to the next one in the chain
        - If we add several `.then`'s at the top level to the promise, that's just multiple handlers
    - We can return new promises inside of `.then`, making this cleaner than nested callbacks
    - If you throw inside of `.catch`, it goes to the next error handler (either in the chain or outside), if you don't, then any `.then` after the `.catch` can proceed like normal (it assumes the error was handled fine if there was one)  (`.then` gets `.catch`'s return val)
    - **async/await**: define async functions as async, which causes them to always return a promise (will wrap non-promises in it)
        - You can `await` inside async functions, which waits for promises/async functions
        - Since you can't `await` in top-level code, `.then` is used at the top level
        - if what you `await` rejects, it counts as a throw, so you can use `try..catch` inside the async function to deal with that.
- == vs ===: == converts both sides to numbers, so `false == 0` is true, because `false` gets converted to `0`

## Need to know, but I probably won't be like quizzed on it

- Function declarations vs Function expressions (anonymous functions) (e.g. `let foo = function(){...}`):
    - Global function declarations are hoisted, but function expressions are not, they're created when execution reaches it
    - Function declarations are generally preferable, except for when you have a specific reason to use a function expression, e.g. for conditions like `let welcome = condition ? function() {...} : function() {...}` (this works bc welcome is a variable, can't do that w/ declarations)
    - You can have non-anonymous function expressions by giving the function an "inner name", like `let foo = function bar() {...}` and the function could call itself w/ `bar()` OR `foo()`
- Creating a pre-filled array is `const arr = Array(size).fill(value)` in ES6, in ES5 use push
    - A 2D array (matrix) would be done like either:
        - `Array(rows).fill().map(() => Array(cols).fill(false))`
            - Need the .fill() stage bc while Array(rows) has length, it doesn't have indices until you fill it (with null) bc the values at each index are considered "empty"
        - `Array.from(Array(board.length), () => Array(board[0].length).fill(false))`
            - Array.from generates an array from an argument, with an optional map function built in as second arg, e.g. `Array.from([1, 2, 3], x => x + x)` gives `[2, 4, 6]`
- Objects
    - Created using literals or constructor syntax (`new`) keyword
        - If you're creating an object by using a function that RETURNS a new object, you don't need `new` , e.g. if `foo()` returns `{blah: blah}`, you can do `let obj = foo()` bc it's just a function return value
    - We can use square brackets in an object literal. That’s called computed properties.
        - E.g. `let bag = { [fruit]: '5' }` name of property comes from variable `fruit`
    - `Object.assign(dest, src1, src2, ...)` makes a deep copy `dest` with all properties from `src1`, `src2`, and so on (unlimited number of arguments)
        - Object.assign FAILS to deep copy object references though, so you have to do that manually by checking if each key in `src` is an object and then deep cloning that recursively (lodash library has this, but we can implement it ourselves too)
    - Constructor functions let us create functions based on a formula

        ```jsx
        function User(name) {
        	this.name = name;
        	this.isAdmin = false;
        }

        let user = new User("Oscar");
        ```

        - It creates an empty object, assigns it to `this`, then runs the function and returns `this`
        - If you return an Object in a constructor, it returns that instead of `this`, but if a primitive, it ignores it
        - Note that with constructor functions, I don't think you can use the shorthand for functions (with literals you can do `obj.foo() {...}`, here you have to use the longer `obj.foo = function() {...}`
    - Object properties actually have flags - `writable`, `enumerable`, and `configurable` (can be changed, can be looped over, and can be deleted, respectively)
        - Retrieve by using `let descriptor = Object.getOwnPropertyDescriptor(obj, propertyName);`
        - Writing to a `writable=false` property throws an error in strict mode
        - `enumerable=false` means it won't shop up in `for..in` loops
        - `configurable=false` means a property can't be deleted, and once it's non-configurable, it's stuck that way.
        - To set these:

            ```jsx
            Object.defineProperty(user, "name", {
              value: "John",
              // for new properties we need to explicitly list what's true
              enumerable: true,
              configurable: true
            });
            ```

        - There are also ways to seal and freeze entire objects
- Garbage Collection: non-reachable values (globals, locals in current function, locals in functions deeper in the recursive call stack, and some other internal variables, are called roots, roots and everything they can reach, are called reachable) get garbage collected over time
    - algorithm is "mark and sweep" - GC marks roots, and references to them, and what they reference, so we can mark all reachable values. Then, everything not marked gets removed.
    - there are some optimizations that happen, like not checking commonly used objects often
- Symbol types can be used as object keys (in addition to the standard string types)
    - `let id = Symbol("id"); // id is a symbol with the description "id" (optional param)`
    - Symbol descriptions are just descriptions, not unique identifiers (can be same across many)
    - Symbols don't auto-convert to string, need toString() (bc they're not like Strings at all)
    - Symbols, since they are unique, allow us to create **"safe" hidden properties**
        - E.g. given some object `user` that belongs to another code, we can add a key `id` where `id = Symbol("id")` and it's safe bc `user[id]` is tied to a unique symbol that we added, not some possible existing string key that was already there (even if the creator of `user` added their own symbol called id, it wouldn't be the same as ours bc unique
            - And, no one but the place where our `id` symbol is in scope could use it.
    - If we want to use a symbol in an object literal, we need square brackets, e.g. `{ [id]: 2 }`
        - Bc a symbol is the variable id, not "id".
    - Symbols are ignored by `for..in` loops and `Object.keys(obj)`, but not `Object.assign()`
    - We can have global symbols (same name = same symbol), by using `Symbol.for(key)` instead of `Symbol()`
        - `Symbol.for(key)` reads the global symbol with that key, or creates it if it can't find one
        - `Symbol.keyFor(sym)` takes in a GLOBAL symbol, and returns its key (or undefined)

            ```jsx
            let sym = Symbol.for("name")
            console.log(Symbol.keyFor(sym)); // prints "name"
            ```

        - key = description for globals, e.g. `sym.description` above would return "name"
        - There are system symbols, listed in the well-known symbols table like Symbol.toPrimitive used to describe object to primitive conversion, and we can use them to alter built-in behaviours
- Iterables: to make our own objects iterable with `for..of`, we add a `Symbol.iterator` property to it, which is a function that returns an object containing initial values for`current` and `last` as well as a function `next()`
    - You can use Array.from(arrayLike, optionalMapFunction) to create arrays from iterables
- Accessor properties are for getting and setting - we can do something like

    ```jsx
    let user = {
      name: "John",
      surname: "Smith",

      get fullName() {
        return `${this.name} ${this.surname}`;
      }
    };

    alert(user.fullName); // John Smith
    ```

    - From the outside, it's used like a data property, and that's the point.
    - You can use these in classes as well, and in function constructors too. With function constructors, you'll just modify `Function.prototype` (so that these methods are added to the prototype of objects created by the constructor) rather than doing it in the function itself, by  using say, `Object.defineProperty(Function.prototype, 'fullName', {get() {}...}`
- **Mixins**: a mixin provides methods that implement a certain behavior, but we do not use it alone, we use it to add the behavior to other classes.
    - Basically, rather than full-on multiple inheritance, we pick methods from other classes

    ```jsx
    // mixin
    let sayHiMixin = {
      sayHi() {
        alert(`Hello ${this.name}`);
      },
      sayBye() {
        alert(`Bye ${this.name}`);
      }
    };

    // usage:
    class User {
      constructor(name) {
        this.name = name;
      }
    }

    // copy the methods
    Object.assign(User.prototype, sayHiMixin);
    ```

    - Basically we add mixin methods to an object's prototype - no inheritance, just method (shallow) copying
    - Mixins may become a point of conflict if they accidentally overwrite existing class methods. So generally one should think well about the naming methods of a mixin, to minimize the probability of that happening.
        - This is why they're not liked in React!
    - Imo, prefer Mixins (a way to use composition) over inheritance bc inheritance chains can get fragile (low-depth inheritance isn't the point of contention, usually)
    - You can use mixins with Classes/Function Constructors (since they're so similar) as well as regular objects, by assigning to the objects individually rather than to their prototypes
- `try..catch` - synchronous (so it won't work if you `try` a setTimeout), only applies to runtime errors
    - catch(err) - `err` is an `Error`, with a name, a message, and a stack (call stack)
    - Example: (if `json` is fucked then otherwise the code would break)

    ```jsx
    try {
      let user = JSON.parse(json); // <-- when an error occurs...
      alert( user.name ); // doesn't work

    } catch (e) {
      // ...the execution jumps here
      alert( e.name );
      alert( e.message );
    }
    ```

    - `throw new Error(message)`
    - Catch should only handle errors that it knows, otherwise it can rethrow to an outer block (or kill the script lol)
    - There's also `finally`, the last part, which like other languages will execute always, even if `try` returns - it runs before the return.
    - In browsers there's also a `window.onerror` property you can set to a function for a global catch
    - You can also extend `Error` and throw custom errors
- **DOM** Stuff
    - The Document Object Model (DOM) represents the HTML source document in a way that it can be manipulated, by a language such as JavaScript.
        - This is exposed in standard JS methods like `document.getElementsByTagName("p")`, but has been extended/changed in libraries like React and jQuery.
        - All of the properties, events and methods are organized into objects.
    - DOM Trees: every HTML tag is an object, and nested tags are children of their enclosing tags. Text inside a tag is an object as well (a text node, rather than an element node)
        - Each node can have multiple objects (matching its HTML)
        - There are `document`, element, text, and comment nodes. (there are more but we only really work with these 4)

        ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/671313a2-5090-4531-bd82-0df18403f093/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/671313a2-5090-4531-bd82-0df18403f093/Untitled.png)

        - Everything in the HTML becomes part of the DOM, even say `<!DOCTYPE...` (even though it's not drawn since we don't typically show it
        - You can navigate b/w nodes using `parentNode`. `childNodes[index]`, `firstChild`, `lastChild`, `nextSibling`, `previousSibling`
            - Remember, element nodes don't contain text! They may contain a text node, though.
        - `document.body` and `document.documentElement` let you access the body/full document
        - `nodeName` gets the name of a node (uppercase tag name)
        - `nodeValue` is null for element nodes, text for text nodes
        - `nodeType` is read-only, tells you the type of node (ELEMENT_NODE is 1, TEXT_NODE is 3, COMMENT_NODE is 8)
        - `appendChild` example:

            ```jsx
            var para = document.createElement("p");
            var node = document.createTextNode("This is new.");
            para.appendChild(node);

            var element = document.getElementById("div1");
            element.appendChild(para);
            ```

        - There's also stuff like `element.remove()` `parent.replaceChild()`, `parent.insertBefore(newElement, otherChild)`
        - HTML Collections (DOM Collections) are arraylike, but aren't arrays (no pop() and such)
- Event delegation: instead of assigning a handler to each element with a similar handler, assign to a parent and then decide where to operate
    - E.g. instead of having a handler on each cell in a grid, have a handler on the grid and then determine which cell the event occurred at