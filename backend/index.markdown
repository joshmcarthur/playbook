---
layout: default
title: Backend Dev
has_children: true
nav_order: 20
---

[Choose Boring Technology](http://boringtechnology.club/). I like to get things done, and produce consistent and maintainable code. I also like new languages, frameworks and libraries. I try them out, and compare them to what I know really well. This approach means that I iterate my development approach and skill over time, but always coming from a background of something I know well.

For anything I build, I try and find automated checks to help me stick to 
best practises and community guidelines, including:

* Format checks
* Complexity and code style checks
* Unit tests
* Integration tests
* Package/library auditing
* Security scanning

I have found that languages that have this kind of developer tooling available are usually sufficiently advanced to justify some experimentation.

Most of my pratical experience is in [Ruby on Rails](/backend/rails.html), and I use this experience as a baseline to assess other tools. Learning to work deeply with this framework has allowed to to develop a good sense of patterns and techniques which I find work consistently for me across all types of languages and frameworks.

### Single Responsibility Principle

I try not to start with this, but instead refactor and iterate towards it. It's more of a guiding principle for me - "how can I reduce the number of "things" this class/function/module does?". 

SRP is well-known, so I won't rehash it, but it's essentially endeavouring to make sure that each module of code has a single responsibility. How this plays out really depends on the language, but what it tends to result in for me is that I end up with smaller pieces of code that are more spread out, but also easier to read and maintain. 

SRP also forces me to think about the next point - dependency injection - if I have all these small, do-one-thing pieces of code, how do I control a code path that I need them to follow by talking to one another?

### Pass in function dependencies, don't inline them

Some languages make this easier than others, I think, but Ruby has covered 
a bunch of ways I have found useful that I think apply across languages, including passing them as optional args, extracting the dependency to a method that can then be mocked or stubbed, or proxying the dependency through an adapter that can be externall configured (e.g. such as talking to different databases through ActiveRecord).

I have found that the combination of learning more about both SRP and dependency injection has made me more confident about the code I produce being flexible, maintainable and easy to understand by others. An overhead is certainly introduced by taking this view on feature development (vs. just chucking it all down into a single file), but over time I have found that my habits have changed, so creating APIs and file structures inspired by SRP and DI principles have become something that I just do. 

### Don't reinvent the wheel

This is a all a balancing act, but I have certainly been spoiled in this regard by the rich ecosystem that is [rubygems](http://rubygems.org/). Because of the specific time when Rails was becoming popular, there is a comprehensive set of libraries available that can do just about anything. 

I used to think that this was a real strength of a language that had these kind of "do it for you" libraries available, but as I have had to experience maintaining applications that have taken the approach of using such libraries, I've had to revise my thinking.

The unfortunate side effect of adding all of these third-party libraries is that _someone_ still has to maintain that library code. It is hopefully not you, but it might be if the library maintainer(s) take a break. Or, the entire library might be declared as being unmaintained or abandoned, and there's then the difficult decision on whether to try and replace whatever it's doing in the application with something else.

So, I've had to adjust how I approach libraries a little. Generally now, I spend a lot more time investigating and auditing libraries. It's surprising to me how many of them are just wrapping a single class or method, along with some configuration which would not be necessary if I dropped that class directly in my application and configured it the way I needed it!

At the same time, there's another end of the spectrum here of refusing to add any dependencies at all. This is certainly seen in more languages than others, I think. Node.js has always had a very strong culture of installing packages until all the functions required can just be imported. Python sits at the other end of the spectrum for me, which dependencies there usually being abstractions over HTTP, database communication or something like exception reporting, rather than something that adds features to your application. Ruby sits right in the middle for me - there are a large number of libraries available, but I'll only add a dependency if the [library looks healthy](#understand-libraries-and-frameworks), and it delivers some concrete API or feature that would take me a long time to build and/or get right. 

It's a real balancing act. All I can really do here I think is give some examples of libraries I commonly use:

1. [`requests`](https://requests.readthedocs.io/en/master/) - because _everyone_ in Python world seems to use this, and it makes HTTP requests so, so easy. 
2. [`pundit`](https://github.com/varvet/pundit) - a simple authorisation framework for Ruby. I use this because it lays out a very sensible API for being able to bake authorisation checks into all parts of the application - but the library code is clear, easy to read and well-maintained. I feel safer using this library than others because it is so simple that I don't see it breaking easily due to external dependency changes.
3.  [`ecto`](https://hexdocs.pm/ecto/Ecto.html) - all I can really do is compare it to Ruby's ActiveRecord, and say that it strikes a very nice balance between not abstracting from the database engine too much, while still enabling database access via a clean API. 
4. [`devise`](https://github.com/heartcombo/devise) - authentication in itself isn't too bad - find a `bcrypt` library and go for it. The problem that many people advocating for not using a library for auth seem to gloss over is that it's not just about verifying a password - it's also password resets, account locking, registration, email sending AND logging in. Devise adds all this functionality, which makes it incredibly easy to get started with an application and move onto the actual problems. The downside to this library is how much feature functionality it keeps inside the library - I wish that it could generate the feature code into the application and then be removed as a dependency.

### No surprises

Producing unsurprising code leads to maintainability. Whether I'm working with myself, or with others, I aim to produce code that the reader is _expecting to see_. That means favouring boring code and trying to use existing patterns that are already established within the application, rather than trying to reinvent the wheel. Doing this forces whoever is reading my code to try and reverse-engineer the behaviour I intended.

### Understand libraries and frameworks

Creating web applications often involves working at a level of abstraction when typically I'll be working with frameworks and libraries rather than lower-level building blocks. One of the most important parts of my personal development has been to explore the frameworks and libraries I use. By making the effort to try and understand these, I have learned different coding styles, patterns, and approaches to problem solving that I can see being used in a context that I understand. 

Reading the source code of libraries and frameworks also allows me to gain a deeper understanding of their capabilities. Sometimes this means finding a new API or method that I would not have otherwise found out about, or occasionally just understanding from code comments, tests and structure the strengths and weaknesses of the approach the library is following. 

Exploring open source is a never-ending journey for me. While I started off just understanding the syntax of what was written, as I gain more experience I am able to recognise patterns, contrast to other libraries and frameworks, and feel confident in my decision to use or borrow from a library or framework.

### Test early & often

When I first started development, testing felt like something that provided little benefit, and took away from feature development. Having now been developing for several years, I couldn't do without testing. It provides a mechanism for me to consume my own code and APIs, and reflect on what I'm doing - quite apart from the main benefit of actually checking that the code I write does what it's supposed to.

I have experimented with BDD and TDD, but these partcular processes don't work well for me. I prefer to write a 'draft' of my code, and then use the tests to drive refactoring and reflection to get the draft to a state that is ready for peer review.

Usually, I will try and get tests in as early as I can, and then use these to help me to develop the rest of my solution. Incorprating tests into an automated continuous tool is also something I do as early as possible. Having CI tell me when I have broken a test is much more motivating for me that remembering to run the tests properly myself!

### Use all the automated checks available

The first thing I look for in any language that I"m using are the types of checks that I can leverage. Automated checks that I can build into my continuous integration processes help me to maintain consistent code style, alert me if I am using outdated packages, and perform a range of code analysis that can identify potentially insecure code, optimisations and more - all depending on the language.

For Ruby, the tools that I regularly use here are:

* Rubocop, for style linting
* Brakeman, for secuity scanning based on static code analysis
* Bundle-audit, for identifying outdated packages

*[BDD]: Behaviour-driven development
*[CI]: Continuous integration (automatically running checks when the source code is changed)
*[TDD]: Test-driven development