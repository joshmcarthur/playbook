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

### System tests

System tests provide a comprehensive overview of application functionality. They involve driving a 
browser - usually headless, that may or may not support Javascript - through the application, treating
it as a black box and interacting using buttons, form inputs, and other UI.

System tests sound great, but they can be slow. Nevertheless, in terms of the value that system tests deliver, they are worth it to me, with a couple of caveats. 

First, the style of testing needs to be different. Rather than declaring set up and tear down to happen around each unit tests, system tests run much faster if they are treated as scenarios. This means setting up once at the beginning of the set of tests, and then running through the steps to meet the test expectations in a single example. Capybara's `scenario` and `feature` test aliases can help with this, to indicate that a test is exercising an expected user flow, rather than a 'unit' of the page.

The second caveat for me is (when the application requirements allow), to try and retain as much functionality working without Javascript as possible. Accessibility and performance considerations aside, it allows a test driver to be used that makes HTTP requests and asserts against the responses, without the overhead of stylesheet or Javascript evaluation. Keeping all of the core scenarios within the non-browser test driver provides a core of system tests that are fast to run and cover the overarching application features, with more specific browser-driven scenarios covering dynamic functionality. The exact blend of non-browser vs browser system tests that I aim to write is determined entirely on the type of application - if it is a dynamic frontend application, my system tests may just cover server-rendered content such as login pages and the application container, with perhaps a single browser test that asserts that the frontend 'application' begins rendering. From there, I would be more likely to look at frontend framework testing tools.


### Model tests

I find model tests the most practical test to write - they help me to exercise and visualise the way the
data entities fit together, and also run quickly, so provide a high-level to make sure everything is functioning correctly.

When I write model tests, I try to avoid hitting the database, instead asserting that valid records are valid, and that methods and associations added to the model that work correctly. I'll always start an empty model test by defining a [factory](https://github.com/thoughtbot/factory_bot) for the model, and then asserting that an instance built using that factory is valid. 

From there, I will add an examples for validations asserting that a change to the instance causes the model to become invalid. I do not test all validations, just those that are either crucial for the model to operate correctly, or those that have complex criteria or validation checks. 

I try to keep model tests isolated from one another, stubbing or mocking inter-object integration points. Usually, these integration points will be [service objects](#move-self-contained-processes-into-service-objects), which is a nice test boundary, since the model can assert that the service object is called with the expected arguments (e.g. the model instance), and the serice object tests then assert that the object behaves correctly given those inputs.

Because of the level of community support available, I will usually use [RSpec](https://rspec.info/) for writing tests. I do however prefer the Minitest/Test::Unit framework when creating tests for my own personal projects. I find that writing plain assertions directly in Ruby code (as opposed to RSpec's DSL) leads to me having a higher comprension of the code under tests and how the tests interact with the code. Writing plain Ruby code also means I can use all the same linting (rubocop) and code navigation (solargraph) tools that I use for application code.

Test::Unit is also the official test tool of Rails, which means that it typically has closer and more up-to-date integrations with the framework. An example of this is support for running tests in parallel. This leads to an appreciable decrease in test suite run time on Rails 6 and above. Support for running tests in parallel is still coming in Rspec, as the RSpec library needs to be modified to handle async result collection.

### Write request tests for non-visual interactions

Along with system and model tests, request specs fill in the final missing gap for me - the ablity to exercise any functionality that does not have a user interaction component, and has code paths that tie service objects, models and other types of objects together to return a result.

Request specs are similar to the now-deprecated controller specs, but operate from a black-box approach. The test interface is effectively an HTTP client, where requests are issued against the application, with assertions against the response.

Request specs provide a valuable test interface - they run more or less as quickly as the request takes to execute, do not require external dependencies or UI like system tests do, but still make assertions against the entire application stack integrated together. 

Request specs are the tool I reach for when testing API responses, error handling, and anything else where a system test cannot get to the code path to execute.

### Use the app directory 

Ruby on Rails favours convention over configuration. One of the understated advantages of this is a really well organised app directory. Just because it starts well organised though doesn't mean it can't stay that way!

Most applications will have some kind of pattern that emerges that justifies a new directory under `app`. This won't be the first thing that comes along, but adding a new directory to `app/` is much better than trying to shoehorn an object into `models`, `helpers` or something like that when it doesn't belong there. 

Some examples of app subdirecotries that I use regularly are `app/validators` (either params-hash or [`ActiveModel::EachValidator`](https://api.rubyonrails.org/v3.2.13/classes/ActiveModel/EachValidator.html) subclasses), `app/presenters` ([presenters](#move-object-specific-helpers-into-presenters)) and `app/services` ([service objects](#move-self-contained-processes-into-service-objects)).

### Update constantly

Ruby on Rails releases major version updates. Usually these don't come with breaking changes from the minor version, but changes get more and more significant the older the release.

Over many years of Rails upgrades ranging from easy to painful, I have learned that it is easiest to keep applications up to date, fixing the minor breaking changes as they go. There is also a rake task that will handle configuration changes - running `rake rails:update` will take care of some of the breaking changes automatically.

If a project does need to be brought up to date, it's best to do it one version at a time, fixing the breakges for each version before moving onto the next. 