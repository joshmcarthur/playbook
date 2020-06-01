---
layout: default
title: Elixir & Phoenix
parent: Backend Dev
nav_order: 20
---


Elixir & Phoenix is a new framework for me, but given that it has a solid team
of ex-and-current Ruby on Rails contributors and developers behind it, it is
proving to be an environment that I really enjoy working in.

Technically, I feel that Phoenix code is easier to understand, test and reason
with. From a value delivery perspective, Rails is hard to beat here. It's not as
performant so costs more to operate - but the build time and compatiblity across
developers takes some beating. Nevertheless, I continue to work a bunch with
Phoenix on my personal projects as well as evangelising it to anyone who will
listen, because these problems are surmontable. Phoenix feels like a very solid
next step up from Rails, when something needs to be super solid and performant.
Not to say that this can't be done with Rails, just that Phoenix and the
underlying Elixir language, Erlang and BEAM VM makes this significantly easier
to do.

A lot of this playbook content so far is based on viewing Phoenix through the
lens of my Rails experience. I consider this a good thing, since a lot of my
Rails experience is cross-compatible.

### Lint with credo

[credo] is a static code analysis tool for Elixir code. For someone coming from
a Ruby background, writing Elixir code that is _like_ Ruby in syntax, but in all
other ways is really very different from Ruby, credo has been a lifesaver, and I
wouldn't be without it on an Elixir project.

While it can be frustrating to have code style alerts during a feature
implementation, the recommendations that credo has made have always helped me to
reshape my code to be more functional, maintanable, and ovearall more in the
style of Elixir rather than translated Ruby code.

### Format with `mix`

With Ruby, formatting and code style is incorporated into the same tool -
rubocop. With Elixir, credo provides code style checking, while `mix`, the
task-running tool of Elixir equivalent to `rake` + `bundler` provides formatting
via `mix format`. `mix format` will format Elixir code based on community
conventions. 

Just like credo, `mix format` can help me to write Elixir in a consistent style,
making it easier for both myself and others to understand and maintain features
I build.

### Be careful with contexts

Architecturally, I really like the idea of [Phoenix
contexts](https://hexdocs.pm/phoenix/contexts.html), however I personally
haven't been using them.

Contexts seem like a really neat idea to try and keep conceptually seperate
parts of the application seperate, but in reality I find myself struggling to
clearly identify the boundaries between concerns and to design an API that is
non-specific to a particular use-case while still allowing the necessary access
to the data model the context fronts for the application to function.

Having tried a couple of applications with context, I am now much more cautious.
A context is still something I try and stay on top of, but I suspect it would be
a better refactoring move when a very clear and discrete part of the application
is identified, rather than starting with the context and trying to patch the
functionality in.

### Authentication isn't solved just yet like Devise solves things for Rails

This is both a good and a bad thing, but in terms of my playbook, it's a
disadvantage that I need to keep in mind when starting a new Phoenix
application. For Rails, to get a standard authentication set up and running
quickly, [devise](https://github.com/heartcombo/devise) is unbeatable. 

Elixir libraries are still fairly low-level. Generally, this is a really good
thing. There is very little magic in libraries, and they are easy to jump in and
understand how the library functions. Generally, libraries provide functions, or
at the most a [Plug](https://hexdocs.pm/phoenix/plug.html) or two. 

Because of this level of libraries, I have not yet come across a solution that
provides ready-to-go authentication functionality. This is fine, but it does
mean that as I start a Phoenix application, I either need to consider the effort
to develop authentication, password resets, locks, timeouts and all the other
necessary stuff, or consider a different approach to authentication - perhaps
Auth0 or an OAuth integration, where the functionality is effectively
outsourced.

While there are no solutions in Phoenix for this just yet, there is some [work
in progress](https://dashbit.co/blog/a-new-authentication-solution-for-phoenix)
to support generating authentication into a new Phoenix application - e.g. `mix
phx.gen.auth`. This would provide the best of both worlds - it would not require
adding a library that hides a lot of the complexity it's taking care of, but
would also generate all of the code rather than requiring a lot of development
investment into coding up a problem that's already been solved in so many other
frameworks. 

The lack of simple authentication for Phoenix is genuinely for me one of the few
blockers to using this framework for actual projects, so I'm very excited to see
work on this problem progressing.

### Understand Ecto

Understanding the database access library is probably a sensible thing to do
regardless of the framework, but being in the process of translating some of my
Rails knowledge to Phoenix & Elixir by far the bit I get stuck on the most is
Ecto. 

It is not that it is hard to understand - quite the opposite in fact.
ActiveRecord (part of the Rails framework) is an incredible library, but just
like devise, it hides a lot of the complexity away from the developer and
exposes quite an abstract data access API. 

Ecto does not do this. It follows the repository pattern rather than the active
record pattern (note - the
[pattern](https://en.wikipedia.org/wiki/Active_record_pattern), not the library
that is named after it), as well as exposing a data interace API that is much
closer to SQL. 

Personally, I really like representing data access in something that is SQL and
SQL-like, so I have really enjoyed working with SQL. What I have found though,
is that Ecto, while being a simpler library at heart, has had a much greater
learning curve than ActiveRecord had for me, as it is less abstract. Ecto's API
is much more pure and easy to understand in terms of the database operation that
is taking place, but I frequently find myself missing the ability to just chain
a few `ActiveRecord::Relation` calls together and have results pop out the end
with the SQL statement getting put together and executed for me. 

### OTP is great - it might not be needed or wanted

<abbr title="Open Telecom Platform">OTP</abbr> is an absolutely fantastic part of Erlang, which Elixir is built on top of and benefits so much from. It abstracts over many of the difficult problems around fault tolerance and concurrency. 

While there is a certain value in having an understanding of OTP, I feel that for just Phoenix application development, it can be a distraction. While there is certainly plenty of opportunities to use OTP concepts and APIs within a Phoenix application, this level of knowledge is not required. If it's not _required_ to know to use a framework, then it justifies some pause before throwing it in, since anyone maintaining the feature will also need to have at least some knowledge of OTP. 

I've personally tried to learn OTP several times, and not been able to reconcile it to typical web application features that I build. Again, I could certainly find excuses to use OTP to build out a feature, and it would probably be very scalable, resilient and stable - however, thanks to the stability of Elixir and Phoenix itself, the same would probably be true had I just built the feature using Pheonix and perhaps a background processing library that abstracted OTP.
