---
layout: default
title: Node.js
parent: Backend Dev
nav_order: 30
---

Work in progress
{: .label .label-blue }

Node.js started off as a bit of a toy language - a demonstration that Javascript didn't just belong in the browser. Nowadays, the language support that has some with ES6 in particular has made server-side Node.js quite pleasant to write. 

The use-case that it fits into for me is scripting. While I have worked with a number of web frameworks and libraries, I have found that there's nothing that quite gives me the same productivity as Rails and Phoenix do. Instead, I reserve Node.js for smaller applications, serverless functions and other standalone code, which it is perfect for.

### Async/await and promises make Node.js easier to reason with

One of the things that put me off Node.js in the past was the complexity of writing asynchrous code that was easy to follow and understand. At that stage, I just didn't have the experience to handle callbacks, errors, and the mixture of sync/async APIs that Node.js delivers, and come out of it with workable code.

[Wes Bos](https://wesbos.com/) created a really good [ES6 course](https://es6.io/) that I worked through some time ago to bring my JS knowledge up to date - and I am super glad that I did, since this course taught me about `async`/`await` and promise-native code. 

Simply put, promise-native code means writing code that uses promises and is promise-aware out of the box. Promises are not needed for all functions, but can be really useful for pipelines or workflows where there is a sequential series of functions to pipe values through. I have found that starting with these functions able to use promises has immediately led to me creating cleaner, more maintainable and more reusable code.

Promises are fantastic, but they don't entirely remove the nesting of logic. Typically, I'll see callback functions that are passed to handlers instead replaced with `.then` statements. To clean this up further, there is `async`/`await`. `async`/`await` is basically a language-level statement that informs the VM to _await_ the promise that follows the statement to resolve before continuing. Functions that use `await` need to be marked as `async` so that the runtime knows to expect to wait for promises to resolve - otherwise it would carry on before the promises have a chance to complete. Using `async`/`await` doesn't slow down the runtime, since the thread is able to go and do other work while waiting for the promises to resolve with `await`. 

With `async`/`await`, the Node.js code I write is much cleaner, since I can wait for async operations inline without nesting callbacks or chaining `then`. Something I do continue to watch out for is scenarios where a normal Promise may be more readable - typically this will be a chain or workflow of functions that I need to pass a result through. In this case, multiple `then` callback functions make more sense, since this represents the processing chain. `async`/`await` is more appropriate for single async operations. Tagging _all_ functions as `async` is a bit of a red flag that I am overusing this functionality.

* Don't disregard tools that solve Node.js problems - e.g. Next.js
* Change one thing at a time
* Format with prettier
* Lint with eslint
* Decide on an approach for testing
