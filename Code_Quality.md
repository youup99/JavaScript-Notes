# Debugging in Chrome

## Summary

As we can see, there are three main way to pause a script:

1. A breakpoint.
2. The `debugger` statements.
3. An error (if dev tools are open and the enable button is "on").

When paused, we can debug - examine variables and trace the code to see hwere the execution goes wrong.

There are many more options in developer tools than covered here.

# Coding Style

- Pretty straightforward.

# Comments

## Summary

An important sign of a good developer is comments: their presence and even their absence.

Good comments allow us to maintain the code well, come back to it after a delay and use it more effectively.

**Comment this:**

- Overall architecture, high-level view.
- Function usage.
- Important solutions, especially when not immediately obvious

**Avoid comments:**

- That tell "how code works" and "what it does".
- Put them in only if it's impossible to make the code so simple and self-descriptive that it doesn't require them.

Comments are also used for auto-documenting tools like JSDoc3: they read them and generate HTML-docs (or docs in another format).