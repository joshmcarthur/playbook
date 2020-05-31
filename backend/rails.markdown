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

### Use templates to maintain consistency

Rails has a really neat ability to use templates when creating a new application. Templates function just like generators, but there's nothing quite like them for getting up to speed correctly.

I have my own [rails-template](http://github.com/joshmcarthur/rails-template). I originally forked this from an [upstream](https://github.com/mattbrictson/rails-template) that was aligned to the tools and processes I commonly use. 

The efficiency gains from using the template when setting up projects professionally led my employer to fork and maintain their own open-source rails-template the changes from which I frequently merge back into my own repository.

Templates provide a log of architecture considerations and decisions, as well as show an evolution of improvement in tools and processes over time. 

Rails' support for templates is also neat, as it allows templates to be re-applied. This means that it is possible to both create new projects, and use it as an upgrade tool for less up-to-date projects.

### Move nested objects and param wrangling into form objects

Creating multiple objects in a single form seems to consistently be an architectural problem that I struggle with in Rails - and in fact any web framework.

Having a single UI create multiple database resources doesn't naturally map well to CRUD operations against tables, but a technique that I find works well for me to overcome this is to use form objects.

Form objects accept params and other context about the request, and perform whatever validation and processing is required to get one or more database objects ready to saved (or even saved, in some circumstances).

I usually place the bulk of the validations into the form object, leaving the models to enforce enum values and database constraints. 

This approach completely decouples the view layer from the database layer, and allows for a much more flexible UI. It is also nice and testable, since the model objects stay quite simple and reusable (since the validations are on the form object), the form object can be unit tested without database writes, and the entire stack can be tested with one or more system integration tests.

### Move object-specific helpers into presenters

Another pattern I use a lot is to introduce presenters into applications. Presenters accept an
object to 'present', and add or override methods as required for the object to be used for presentation - for example in a template.

Helpers can also be a solution for this, but I prefer helpers for global, application-level helpers. Presenters add an additional layer between models and templates, and allow templates to not contain too much logic, while also allowing models to represent the data schema, and nothing more.

There are libraries available for presenters, but Rails'
[`delegate_missing_to`](https://apidock.com/rails/v5.2.3/Module/delegate_missing_to)
provides pretty much all the functionality I need. Presenters are also easy to
test - they just need a view context and the object to present, and the output
can be tested with assert or Capybara matchers.

### Move self-contained processes into service objects

I need to be careful not to over-use service objects, but they fill a niche between data entities
as models, and steps of, or entire workflows. 

Generally service objects I create function similarly to a lambda, but with an initialiser. They'll accept arguments that determines what adapters, API clients etc. to use, and respond to `call` to actually perform the action. By sticking to this contract, I have a built-in check to make sure that the service object represents a self-contained operation, and also have a nice hook for stubbing, since I can pass in an actual lambda to mock the result the service object.

Service objects are useful for all sorts of things, but I use them especially to cut down on controller complexity, or handle an interaction with a third-party API or database in a reusable and self-contained way. 


* Write system tests
* Write model tests
* Write request tests for non-visual interactions
* Automated checks
* Update constantly
