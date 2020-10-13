# Browser environment, specs

## Summary

Talking about standards, we have:

**DOM specification**

Describes the document structure, manipulations and events.

**CSSOM specification**

Describes stylesheets and style rules, manipulations with them and their binding to documents.

**HTML specification**

Describes the HTML language (e.g. tags) and also the BOM (browser object model) - various browser functions: `setTimeout`, `alert`, `location`, and so on. It takes DOM specification and extends it with many additional properties and methods.

Additionally, some classes are described seperately.

# DOM tree

## Summary

An HTML/XML document is represented inside the browser as the DOM tree.

- Tags become element nodes and form the structure.
- Text becomes text nodes.
- ...etc, everything in HTML has its place in DOM, even comments

We can use developer tools to inspect the DOM and modify it manually.

DOM nodes have properties and methods that allow us to travel between them, modify them, move around the page, and more.

# Walking the DOM

- `<html>` = `document.documentElement`
- `<body>` = `document.body`
- `<head>` = `document.head`

## Summary

Give a DOM node, we can go to its immediate neighbors using navigation properties.

There are two main sets of them:

- For all nodes, `parentNode`, `childNodes`, `firstChild`, `lastChild`, `previousSibling`, `nextSibling`.
- For element nodes only: `parentElement`, `children`, `firstElementChild`, `lastElementChild`, `previousElementSibling`, `nextElementSibling`.

Some types of DOM elements, e.g. tables, provide additional properties and collections to access their content.

# Searching: getElement*, querySelector*

## Summary

There are 6 main methods to search for nodes in DOM:

| Method | Searches by... | Can call on an element? | Live? |
| ------ | -------------- | ----------------------- | ----- |
| `querySelector` | CSS-selector | Yes | - |
| `querySelectorAll` | CSS-selector | Yes | - |
| `getElementById` | `id` | - | - |
| `getElementsByName` | `name` | - | Yes |
| `getElementsByTagName` | tag or `'*'` | Yes | Yes |
| `getElementsByClassName` | class | Yes | Yes |

By far the most used are `querySelector` and `querySelectorAll`, but `getElementBy*` can be sporadically helpful or found in the old scripts.

Besides that:

- There is `elem.matches(css)` to check if `elem` matches the given CSS selector.
- There is `elem.closest(css)` to look for the nearest ancestor that matches the given CSS-selector. The `elem` itself is also checked.

And let's mention one more method here to check for the child-parent relationship, as it's sometimes useful:

- `elemA.contains(elemB)` returns true if `elemB` is inside `elemA` (a descendant of `elemA`) or when `elemA == elemB`.


# Node properties: type, tag and contents

## Summary

Each DOM node belongs to a certain class. The classes form a hierarchy. The full set of properties and methods come as the result of inheritance.

Main DOM node properties are:

`nodeType`

We can use it to see if a node is a text or an element node. It has a numeric value: `1` for elements, `3` for text nodes, and a few other for other node types. Read-only.

`nodeName/tagName`

For elements, tag name (uppercased unless XML-mode). For non-element nodes `nodeName` describes what it is. Read-only

`innerHTML`

The HTML content of the element. Can be modified.

`outerHTML`

The full HTML of the element. A write operation into `elem.outerHTML` does not touch `elem` itself. Instead it gets replaced with the new HTML in the outer context.

`nodeValue/data`

The content of a non-element node (text, comment). These two are almost the same, usually we use `data`. Can be modified.

`textContent`

The text inside the element: HTML minus all `<tags>`. Writing into it puts the text inside the element, with all special characters and tags treated exactly as text. Can safely insert user-generated text and protect from unwanted HTML insertion.

`hidden`

When set to `true`, does the same as CSS `display:none`.

DOM nodes also have other properties depending on their class. For instance, `<input>` elmements (`HTMLInputElement`) support `value`, `type`, while `<a>` elements (`HTMLAnchorElement`) support `href`, etc. Most standard HTML attributes have a corresponding DOM property.

However, HTML attributes and DOM properties are not always the same, as we'll see in the next chapter.

# Attributes and properties

## Summary

- Attributes - is what's written in HTML.
- Properties - is what's in DOM objects.

A small comparison:

|     | Properties | Attributes |
| --- | ---------- | ---------- |
| Type | Any value, standard properties have type described in the spec | A string |
| Name | Name is case-sensitive | Name is not case-sensitive |

Methods to work with attributes are:

- `elem.hasAttribute(name)` - to check for existence.
- `elem.getAttribute(name)` - to get the value.
- `elem.setAttribute(name, value)` - to set the value.
- `elem.removeAttribute(name)` - to remove the attribute.
- `elem.attributes` is a collection of all attributes.

For most situations, using DOM properties is preferable. We should refer to attributes only when DOM properties do not suit us, when we need exactly attributes. For instance:

- We need a non-standard attribute. But if it starts with `data-`, then we should use `dataset`.
- We want to read the value "as written" in HTML. The value of the DOM property may be different, for instance, the `href` property is always a full URL, and we may want to get the "original" value.

# Modifying the document

## Summary

Methods to create new nodes:

- `document.createElement(tag)` – creates an element with the given tag,
- `document.createTextNode(value)` – creates a text node (rarely used),
- `elem.cloneNode(deep)` – clones the element, if `deep==true` then with all descendants.

Insertion and removal:

- `node.append(...nodes or strings)` – insert into `node`, at the end,
- `node.prepend(...nodes or strings)` – insert into `node`, at the beginning,
- `node.before(...nodes or strings)` - insert right before `node`,
- `node.after(...nodes or strings)` - insert right after `node`,
- `node.replaceWith(...nodes or strings)` - replace `node`.
- `node.remove()` - remove the `node.

Text strings are inserted “as text”.

There are also “old school” methods:

- `parent.appendChild(node)`
- `parent.insertBefore(node, nextSibling)`
- `parent.removeChild(node)`
- `parent.replaceChild(newElem, node)`

All these methods return `node`.

Given some HTML in `html`, `elem.insertAdjacentHTML(where, html)` inserts it depending on the value of where:

- `"beforebegin"` – insert `html` right before `elem`,
- `"afterbegin"` – insert `html` into `elem`, at the beginning,
- `"beforeend"` – insert `html` into `elem`, at the end,
- `"afterend"` – insert `html` right after `elem`.

Also there are similar methods, `elem.insertAdjacentText` and `elem.insertAdjacentElement`, that insert text strings and elements, but they are rarely used.

To append HTML to the page before it has finished loading:

- `document.write(html)`

After the page is loaded such a call erases the document. Mostly seen in old scripts.

# Styles and classes

## Summary

To manage classes, there are two DOM properties:

- `className` - the string value, good to manage the whole set of classes.
- `classList` - the object with methods `add/remove/toggle/contains`, good for individual classes.

To change the styles:

- The `style` property is an object with camelCased styles. Reading and writing to it has the same meaning as modifying individual properties in the `"style"` attribute. To see how to apply `important` and other rare stuff - there's a list of methods at MDN.
- The `style.cssText` property corresponds to the whole `"style"` attribute, the full string of styles

To read the resolved styles (with respect to al lclasses, after all CSS is applied and final values are calculated):

- The `getComputedStyle(elem, [pseudo])` returns the style-like object with them. Read-only.

# Element size and scrolling 

Elements have the following geometry properties:

- `offsetParent` – is the nearest positioned ancestor or `td`, `th`, `table`, `body`.
- `offsetLeft/offsetTop` – coordinates relative to the upper-left edge of `offsetParent`.
- `offsetWidth/offsetHeight` – `“outer”` width/height of an element including borders.
- `clientLeft/clientTop` – the distances from the upper-left outer corner to the upper-left inner (content + padding) corner. For left-to-right OS they are always the widths of left/top borders. For right-to-left OS the vertical scrollbar is on the left so `clientLeft` includes its width too.
- `clientWidth/clientHeight` – the width/height of the content including paddings, but without the scrollbar.
- `scrollWidth/scrollHeight` – the width/height of the content, just like `clientWidth/clientHeight`, but also include scrolled-out, invisible part of the element.
- `scrollLeft/scrollTop` – width/height of the scrolled out upper part of the element, starting from its upper-left corner.

All properties are read-only except `scrollLeft/scrollTop` that make the browser scroll the element if changed.

# Window sizes and scrolling

## Summary

Geometry:

- Width/height of the visible part of the document (content area width/height): `document.documentElement.clientWidth/clientHeight`
- Width/height of the whole document, with the scrolled out part:

    ```javascript
    let scrollHeight = Math.max(document.body.scrollHeight, document.documentElement.scrollHeight, document.body.offsetHeigh, document.documentElement.offsetHeight, document.body.clientHeight, document.documentElement.clientHeight);
    ```

Scrolling:

- Read the current scroll: `window.pageYOffset/pageXOffset`.
- Change the current scroll:
    - `window.scrollTo(pageX, pageY)` - absolute coordinates.
    - `window.scrollBy(x, y)` - scroll relative to the current place.
    - `elem.scrollIntoView(top)` - scroll to make `elem` visible (align with the top/bottom of the window).

# Coordinates

## Summary

Any point on the page has coordinates:
1. Relative to the window - `elem.getBoundingClientRect()`.
2. Relative to the document - `elem.getBoundingClientRec()` plus the current page scroll.

Window coordinates are great to use with `position:fixed` and document coordinates do well with `position: absolute`.

Both coordinate systems have their pros and cons; there are times we need one or the other one, just like CSS `position`, `absolute` and `fixed`.