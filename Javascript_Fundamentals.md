# Hello, world!

- Inside HTML,

  ```html
  <script>
    alert('Hello, world!');
  </script>
  ```

- **If `src` is set, the script content is ignored**

## Summary

- We can use a `<script>` tag to add JavaScript code to a page.
- The `type` and `language` attributes are not required.
A script in an external file can be inserted with `<script src="path/to/script.js"></script>`

# Code structure

- Statements are syntax constructs and commands that perform actions.
- Put semicolons between statements.
- Comments `// Comment` or `/* Comment */`

# The modern mode, "use strict"

- "use strict", when located at the top of a script, the whole script works the "modern" way.

  ```js
  "use strict";
  // this code works the modern way
  ...
  ```

- For now, "use strict", is a welcome guest at the top of your scripts. Later, when your code is all in classes and modules, you may omit it.

# Variables

- Variables are used to store information. "Named storage" for data.
- Use the `let` keyword to create a variable.
- Name must contain only letters digits, or the symbols `$` and `_`.

## Constants

- Cannot be reassigned.
- Uppercase constants is widespread practice.

## Summary

- `let` - is a modern variable declaration.
- `var` - is an old-school variable declaration.
- `const` - is like `let`, but the value of the variable can't be changed.

# Data types

- Number (Integer and floating point)
  - Infinity
  - -Infinity
  - NaN
- BigInt (larger than 2^53 - 1 || less than -2^53 - 1)
- String
  1. Double quotes: `"Hello"`
  2. Single quotes: `'Hello'`
  3. Backticks: "extended functionality" quotes
      - You can put `Hello, ${name}!` with a variable or arithmetical expression.
- Boolean
- "null" value - represnts "nothing", "empty", or "value unknown"
- "undefined" value - "value is not assigned"
- Objects and Symbols
- typeof operator - returns the type of the argument
  1. As an operator: `typeof x`
  2. As a function: `typeof(x)`

## Summary

8 basic data types

- `number` for numbers of any kind: integer or floating-point, integers are limited by `+-(2^53-1).
- `bigint` is for integer numbers of arbitrary length.
- `string` for strings. A string may have zero or more characters, there's no seperate single-character type.
- `boolean` for `true/false`.
- `null` for unknown values - a standalone type that has a single value `null`.
- `undefined` for unassigned values - a standalone type that has a single value `undefined`.
- `object` for more complex data structures.
- `symbol` for unique identifiers.

`typeof` operator

- Two forms: `typeof x` or `typeof(x)`
- Returns a string with the name of the type, like `"string"`.
- For `null` returns `"object"` - this is an error in the language, it's not actually an object.

# Interaction: alert, prompt, confirm

## Summary

- `alert` - shows a message
- `prompt` - shows a message asking the user to input text. It returns the text or, if Cancel button or ESC is clicked, `null`.
- `confirm` - shows a message and waits for the user to press "OK" or "Cancel". It returns `true` for OK and `false` for Cancel/ESC.

All these methods are modal: they pause script execution and don't allow the visitor to interact with the rest of the page until the window has been dismissed.

There are two limitations shared by all the methods above:

1. The exact location of the modal window is determined by the browser. Usually, it's in the center.
2. The exact look of the window also depends on the browser. We can't modify it.

- That is the price for simplicity. There are other ways to show nicer windows and richer interaction with the visitor, but if "bells and whistles" do not matter much, these methods work just fine.

# Type Conversions

## Summary

- String Conversion - Occurs when we output something. Can be performed with `String(value)`. The conversion to string is usually obvious for primitive values.
- Numeric Conversion - Occurs in math operations. Can be performed with `Number(value)`.
  | Value     | Becomes... |
  | -------   | ---------- |
  | undefined | NaN        |
  | null      | 0          |
  | true/false| 1/0        |
  | string    | The string is read "as is", whitespaces from both sides are ignored. An empty string becomes 0. An error gives NaN. |

- Boolean Conversion - Occurs in logical operations. Can be performed with `Boolean(Value)`.
  | Value   | Becomes... |
  | -----   | ---------- |
  | 0, null, undefined, NaN, "" | false |
  | any other value | true |

Most of these rules are easy to understand and memorize. The notable exceptions where people usually make mistakes are:

- `undefined` is `NaN` as a number, not `0`.
- `"0"` and space-only strings like `" "` are true as a boolean.

# Basic operators, maths

- Pretty straightforward.

# Comparisons

## Summary

- Comparison operators return a boolean value.
- Strings are compared letter-by-letter in the "dictionary" order.
- When values of different types are compared, they get converted to numbers (with the exclusion of a strict equality check).
- The values `null` and `undefined` equal `==` each other and do not equal any other value.
- Be careful when using comparisons like `>` or `<` with variables that can occasionally be `null/defined`. Checking for `null/undefined` seperately is a good idea.

# Conditional branching: if, '?'

- Pretty straightforward.

# Logical operators

- Pretty straightforward.

# Nullish coalescing operator '??'

## Summary

- The nullish coalescing operator `??` provides a short way to choose the first "defined" value from a list.
- It's used to assign default values to variables:

  ```javascript
  height = height ?? 100;
  ```

- The operator `??` has a very low precedence, only a bit higher than `?` and `=`, so consider adding parantheses when using it in an expression.
- It's forbidden to use it with `||` or `&&` without explicit parantheses.

# Loops: while and for

## Summary

- `while` - The condition is checked before each iteration.
- `do..while` - The condition is checked after each iteration.
- `for (;;)` - The condition is checked before each iteration, additional settings available.

To make an "infinite" loopm usually the `while(true)` constuct is used. Such a loop, just like any other, can be stopped with the `break` directive.

If we don't want to do anything in the current iteration and would like to move forward to the next one, we can use the `continue` directive.

`break/continue` support labels before the loop. A label is the only way for `break/continue` to espace a nested loop to go to an outer one.

# Switch

- Pretty straightforward.

# Functions

## Summary

A function declaration looks like this.

```javascript
function name(parameters, delimited, by, comma){
  /* code */
}
```

- Values passed to a function as parameters are copied to its local variables.
- A function may access outer variables. But it works only from inside out. The code outside of the function doesn't see its local variables.
- A function can return a value. If it doesn't, then its result is `undefined`.

To make the code clean and easy to understand, it's recommended to use mainly local variables and parameters in the function, not outer variables.

It is always easier to understand a function which gets parameters, works with them and returns a result than a function which gets no parameters, but modifies outer variables as a side-effect.

Function naming:

- A name should clearly describe what the function does. When we see a function call in the code, a good name instantly gives us an understanding of what it does and returns.
- A function is an action, so function names are usually verbal.
- There exist many well-know function prefixes like `create`, `show`, `get`, `check`, and so on. Use them to hint what a function does.

Functions are the main building blocks of scripts. Now we've covered the basics, so we actually can start creating and using them. But that's only the beginning of the path. We are going to return to them many times, going more deeply into their advanced features.

# Function expressions

## Summary

- Functions are values. They can be assigned, copied, or declared in any place of the code.
- If the function is declared as a seperate statement in the mian code flow, that's called a "Function Declaration".
- If the function is created as part of an expression, it's called a "Function Expression".
- Function Declarations are processed before the code block is executed. They are visible everywhere in the block.
- Function Expressions are created when the execution flow reaches them.

In most cases when we need to declare a function, a Function Declaration is more preferable, because it is visible prior to the declaration itself. That gives us more flexibility in code organization, and is usually more readable.

So we should use a Function Expression only when a Function Declaration is not for the task.

# Arrow functions, the basics

## Summary

Arrow functions are handy for one-liners. They come in two flavors:

1. Withour curly braces: `(...args) => expression` - the right side is an expression: the function evaluates it and returns the result.
2. With curly braces: `(...args) => {body}` - brackets allow us to write multiple statements inside the function, but we need an explicit `return` to return something.