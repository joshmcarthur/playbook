---
layout: default
title: Elixir & Phoenix
parent: Backend Dev
nav_order: 20
---

Work in progress
{: .label .label-blue }


Elixir & Phoenix is a new framework for me, but given that it has a solid team of ex-and-current Ruby on Rails contributors and developers behind it, it is proving to be an environment that I really enjoy working in.

Technically, I feel that Phoenix code is easier to understand, test and reason with. From a value delivery perspective, Rails is hard to beat here. It's not as performant so costs more to operate - but the build time and compatiblity across developers takes some beating. Nevertheless, I continue to work a bunch with Phoenix on my personal projects as well as evangelising it to anyone who will listen, because these problems are surmontable. Phoenix feels like a very solid next step up from Rails, when something needs to be super solid and performant. Not to say that this can't be done with Rails, just that Phoenix and the underlying Elixir language, Erlang and BEAM VM makes this significantly easier to do.

A lot of this playbook content so far is based on viewing Phoenix through the lens of my Rails experience. I consider this a good thing, since a lot of my Rails experience is cross-compatible.

### Lint with credo

[credo] is a static code analysis tool for Elixir code. For someone coming from a Ruby background, writing Elixir code that is _like_ Ruby in syntax, but in all other ways is really very different from Ruby, credo has been a lifesaver, and I wouldn't be without it on an Elixir project.

While it can be frustrating to have code style alerts during a feature implementation, the recommendations that credo has made have always helped me to reshape my code to be more functional, maintanable, and ovearall more in the style of Elixir rather than translated Ruby code.

### Format with `mix`

With Ruby, formatting and code style is incorporated into the same tool - rubocop. With Elixir, credo provides code style checking, while `mix`, the task-running tool of Elixir equivalent to `rake` + `bundler` provides formatting via `mix format`. `mix format` will format Elixir code based on community conventions. 

Just like credo, `mix format` can help me to write Elixir in a consistent style, making it easier for both myself and others to understand and maintain features I build.

* Be careful with contexts
* Authentication isn't solved just yet like Devise solves things for Rails
* Understand Ecto
* OTP is great - probably not needed