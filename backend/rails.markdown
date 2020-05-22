---
layout: default
title: Ruby on Rails
parent: Backend Dev
nav_order: 10
---

Work in progress
{: .label .label-blue }

Ruby on Rails is a great development platform for me. My main interest lies in taking a problem, and solving it with working software, quickly. That's exactly what Rails is great at. It is a battle-tested, flexible tool. There's certainly scenarios where it may not be the best tool to use, but it's a great starting point for _most_ cases that don't have specialised requirements.

I've got a few guidelines that I try and consistently follow that have worked for me repeatedly over the last 10 years of me using Ruby on Rails.

### Keep dependencies minimal

The Ruby ecosystem has a rich and extremely broad collection of code libraries (gems) available for use. This is especially true for Rails specifically. While library quality has stabilised and improved over the last several years, for some time there was definitely a culture of adding plugins and gems to your application until it did what was required, kinda. 

Each gem that is added to your application takes one line added to install it - something like `gem "library"`. The thing to keep in mind is that one line can, and frequently is, pulling in hundreds or thousands of lines of code that may or may not remain maintained. If the library doesn't get enough maintenance to stay bug free as the framework, language and other libraries update, then the onus for getting your application working again is on you. You can change your application to not use that library - that can be anywhere from very easy to extremely hard, depending on how embedded it is into your use cases. You could also fork the gem and patch it yourself - in doing this recognising that if others find your patches useful, then you may find yourself maintaining the library, or at least supporting your patches. By forking the library to your own copy, you also lose any future updates (unless you pull in updates from upstream), increase fragmentation. 

There are some frameworks that simply discourage including dependencies at all,
but I feel that this is too far in the opposite direction. Instead, I have
developed a much deeper awareness of how to assess the quality and
maintainability of a library. Rather than blindly installing, I now tend to look
into what the library is doing, and how it is doing it. There are a surprising
number of libraries that don't do all that much, and in these cases it can be
easier to build the functionality the libraries provide right into your
application, where they will be more visible to other developers, easier to
directly test, and adjustable if the way that the application interfaces with
the functionality needs to be refactored.

To provide some basic stats, my primary gem dependencies for a Ruby on Rails project gemfile range between 10 and 60 gems, with a median of about 25. Generally, the older the project is, the more dependencies are included.


* Use templates to maintain consistency
* Move nested objects and param wrangling into form objects
* Move object-specific helpers into presenters
* Move self-contained processes into service objects
* Write system tests
* Write model tests
* Write request tests for non-visual interactions
* Automated checks
* Update constantly
