# Introduction to browser events

## Summary

There are 3 ways to assign event handlers:

1. HTML attribute: `onclick="..."`.
2. DOM property: `elem.onclick = function`.
3. Methods: `elem.addEventListener(event, handler[, phase])` to add, `removeEventListener` to remove.

HTML attributes are used sparingly, because JavaScript in the middle of an HTML tag looks a little bit odd and alien. Also can’t write lots of code in there.

DOM properties are ok to use, but we can’t assign more than one handler of the particular event. In many cases that limitation is not pressing.

The last way is the most flexible, but it is also the longest to write. There are few events that only work with it, for instance `transitionend` and `DOMContentLoaded` (to be covered). Also addEventListener supports objects as event handlers. In that case the method `handleEvent` is called in case of the event.

No matter how you assign the handler – it gets an event object as the first argument. That object contains the details about what’s happened.

We’ll learn more about events in general and about different types of events in the next chapters.

# Bubbling and capturing

## Summary

When an event happens - the most nested element where it happens gets labeled as the "target element" (`event.target`).

- Then the event moves down from the document root to `event.target`, calling handlers assigned with `addEventListener(..., true)` on the way (`true` is a shorthand for `{capture: true}`).
- Then handlers are called on the target element itself.
- Then the event bubbles up from `event.target` to the root, calling hanlders assigned using `on<event>` and `addEventListener` without the 3rd argument or with the 3rd argument `false/{capture: false}`.

Each handler can access `event` object properties:

- `event.target` - the deepest element that originated the event.
- `event.currentTarget` (= `this`) - the current element that handles the event (the one that has the handler on it).
- `event.eventPhase` - the current phase (capturing=1, target=2, bubbling=3).

Any event handler can stop the event by calling `event.stopPropagation()`, but that's not recommended, because we can't really be sure we won't need it above, maybe for completely different things.

The capturing phase is used very rarely, usually we handle events on bubbling. And there's logic behind that.

Bubbling and capturing lay the foundation for "event delegation" - an extremely powerful event handling pattern that we study in the next chapter.

# Event delegation

## Summary

Event delegation is really cool! It's one of the most helpful patterns for DOM events.

It's often used to add the same handling for many similar elements, but not only for that.

The algorithm:
1. Put a single handler on the container.
2. In the handler - check the source element `event.target`.
3. If the event happened inside an element that interests us, then handle the event.

Benefits:
- Simplifies initialization and saves memory: no need to add many handlers.
- Less code: when adding or removing elements, no need to add/remove handlers.
- DOM modifications: we can mass add/remove elements with `innerHTML` and the like.

Limitations:
- First, the event must be bubbling. Some events do not bubble. Also, low-level handlers should not use `event.stopPropagation()`.
- Second, the delegation may add CPU load, because the container-level handler reacts on events in any place of the container, no matter whether they interest us or not. But usually the load is negligible, so we don't take it into account.

# Browser default actions

## Summary

There are many default browser actions:

- `mousedown` - starts the selection (move the mouse to select).
- `click` on `<input type="checkbox">` - checks/unchecks the `input`.
- `submit` - clicking an `<input type="submit">` or hitting Enter inside a form field causes this event to happen, and the browser submits the form after it.
- `keydown` - pressing a key may lead to adding a character into a field, or other actions.
- `contextmenu` - the event happens on a right-click, the action is to show the browser contextmenu.

All the default actions can be prevented if we want to handle the event exclusively by JavaScript.

To prevent a default action - use either `event.preventDefault()` or `return false`. The second method works only for handlers assigned with `on<event>`.

The `passive: true` option of `addEventListener` tells the browser that the action is not going to be prevented. That's useful for some mobile events, like `touchStart` and `touchMove`, to tell the browser that it should not wait for all handlers to finish before scrolling.

If the default action was prevented, the value of `event.defaultPrevented` becomes `true`, otherwise it's `false`.

