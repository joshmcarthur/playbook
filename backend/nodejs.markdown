---
layout: default 
title: Node.js
parent: Backend Dev 
nav_order: 30
---

Node.js started off as a bit of a toy language - a demonstration that Javascript
didn't just belong in the browser. Nowadays, the language support that has some
with ES6 in particular has made server-side Node.js quite pleasant to write. 

The use-case that it fits into for me is scripting. While I have worked with a
number of web frameworks and libraries, I have found that there's nothing that
quite gives me the same productivity as Rails and Phoenix do. Instead, I reserve
Node.js for smaller applications, serverless functions and other standalone
code, which it is perfect for.

### Async/await and promises make Node.js easier to reason with

One of the things that put me off Node.js in the past was the complexity of
writing asynchrous code that was easy to follow and understand. At that stage, I
just didn't have the experience to handle callbacks, errors, and the mixture of
sync/async APIs that Node.js delivers, and come out of it with workable code.

[Wes Bos](https://wesbos.com/) created a really good [ES6
course](https://es6.io/) that I worked through some time ago to bring my JS
knowledge up to date - and I am super glad that I did, since this course taught
me about `async`/`await` and promise-native code. 

Simply put, promise-native code means writing code that uses promises and is
promise-aware out of the box. Promises are not needed for all functions, but can
be really useful for pipelines or workflows where there is a sequential series
of functions to pipe values through. I have found that starting with these
functions able to use promises has immediately led to me creating cleaner, more
maintainable and more reusable code.

Promises are fantastic, but they don't entirely remove the nesting of logic.
Typically, I'll see callback functions that are passed to handlers instead
replaced with `.then` statements. To clean this up further, there is
`async`/`await`. `async`/`await` is basically a language-level statement that
informs the VM to _await_ the promise that follows the statement to resolve
before continuing. Functions that use `await` need to be marked as `async` so
that the runtime knows to expect to wait for promises to resolve - otherwise it
would carry on before the promises have a chance to complete. Using
`async`/`await` doesn't slow down the runtime, since the thread is able to go
and do other work while waiting for the promises to resolve with `await`. 

With `async`/`await`, the Node.js code I write is much cleaner, since I can wait
for async operations inline without nesting callbacks or chaining `then`.
Something I do continue to watch out for is scenarios where a normal Promise may
be more readable - typically this will be a chain or workflow of functions that
I need to pass a result through. In this case, multiple `then` callback
functions make more sense, since this represents the processing chain.
`async`/`await` is more appropriate for single async operations. Tagging _all_
functions as `async` is a bit of a red flag that I am overusing this
functionality.

### Introduce one thing at a time

Javascript has had a lot of tooling build up around it. There is babel,
coffeescript, typescript (probably other language variants I am missing), and a
myriad of libraries that are intended to be used as extensions to the core
language. 

All of these can have value, but introducing all of them because they look
useful can lead to something that is difficult to maintain and keep up to date.
I have experienced this on projects before and don't wish to repeat it.

Instead, because of the sheer number of Javascript packages that can get pulled
in, I try and be especially conservative when it comes to dev dependencies. If
I'm adding something significant, then I will add it and try it out for a while
before iterating. This works well for me since I am generally less confident
with Node.js than Ruby, so it takes me longer to determine how I feel about
something new. 

As an example, I have not yet begun using Typescript. I've researched it, and I
see the advantages, but I don't yet feel comfortable enough that I have an
adequate knowledge of the more intricate parts of Node.js, Javascript and ES6 &
7 to start trying to concurrently figure out my type definitions.

### Format with prettier

One of the excellent Javascript tools to come along reasonably recently is
[prettier](https://prettier.io/). Prettier is an _opinionated_ code formatter,
which means I can let it format my code and get on with my day, trusting that it
has incorporated the best practises accepted by the community. 

Prettier is a neat solution as it began with support for Javascript, but now
supports a range of languages. I really like the approach of keeping
configuration extremely minimal. This is the same kind of approach that tools
like `go fmt` and `mix format` take. 

I have prettier incorporated into my VS Code plugins now, so use it on all the
JS I write. I'm slowly rolling out prettier across more languages - I'm
especially excited about the
[@prettier/plugin-ruby](https://github.com/prettier/plugin-ruby) plugin that
adds support for running prettier rules against Ruby files.

### Lint with eslint

Prettier is great at automatically formatting code, but eslint does a great job
filling in the gaps of ensuring that there aren't any loose ends left over.
Eslint is neat in that it doesn't come with much in the way of rules built in,
but rules _are_ packaged as NPM packages that can be installed and used easily.
This is nice because different segments of the Javascript community hold wildly
varying opinions (see: semicolons vs no semicolons), so these eslint can be
configured based on either the opinions that I hold, or the opinions of a
particular community that I tend to align with.

I tend to add eslint as one of the core parts of CI to Node.js _and_
[frontend]({{ "/frontend/" | relative_url }}) projects that I start. Just like
prettier, I do prefer to adopt community best practises rather than to try and
speculate. I generally get started with `eslint:recommended` (the eslint
recommended checks), `plugin:prettier/recommendations` (Prettier-compatible
eslint rules from eslint-plugin-prettier), and some context-specific rules (for
example, for frontend projects I'll usually include eslint-plugin-jsx-a11y to
lint for accessibility). 

### Decide on an approach for testing

The world of Javascript testing is...complex. There are many different parts of
a Javascript project to test, and there's not a consistent approach the
community has settled on like the Ruby community has settled on either RSpec or
Test::Unit. 

There are a few promising libraries in this area, but testing JS apps continues
to be something that I am working on bringing up to scratch. 

For frontend, [Cypress](https://www.cypress.io/) and
[react-testing-library](https://github.com/testing-library/react-testing-library)
are what I usually end up poking around.

For Node.js testing, [Jest](https://jestjs.io/) is what I've used the most of.
It has a similar assert/expect syntax to the kind of JUnit tests I've for
Android apps, so it's somewhat familiar to me, and with `async`/`await`, tests
can be written pretty elegantly, even when testing asynchrous code paths. This
particular library is almost certainly what I'll be using moving forward, while
hopefully forming some more concrete opinions and ideas around Jest and Node.js
testing in general.
