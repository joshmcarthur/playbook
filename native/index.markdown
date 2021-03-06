---
layout: default
title: "Native Dev"
nav_order: 35
---

### Android

I've been doing Android development on and off for the past several years. I
would class myself as a developer with a working, but not deep knowledge of
Android. I am deeply interested in the ecosystem and devices around the OS, and
so my programming knowledge is just a part of that. I went through a phase of
running all sorts of custom ROMs (mostly Lineage variants), Tasker profiles and
various things using magisk or supersu to get a root shell. I now use and
appreciate the polish and stability using a stock Google-flavoured Android ROM
on my Pixel 2 XL. 

My Android development was initiated with Cordova (named Phonegap when I
started). I don't think this needs to be covered off since this is my playbook.
I don't use it, and don't particularly recommend it to others these days either.
I think it delivered plenty of value in the early days of showing what was
possible with JS and a bit of native code bridging, but that niche is now
well-taken care of with other tools. 

If Cordova _must_ be used, you might consider something like
[Ionic](https://ionicframework.com/) that will give you a bit more documentation
and tooling around keeping the mixture of SDKs, NPM packages and magical shell
scripts up to date that are required to properly maintain such an application.

Since Android Studio was released (replacing, or, may I say, _eclipsing_ 
Eclipse + Android plugin), I use native Android dev much more. 
I have a few guidelines that I stick to with Android development:

#### Java/Kotlin?

I have no solid opinion on this yet, but would be inclined to use Kotlin if
starting an application today. Kotlin is getting more and more developer
support, documentation, and tooling, and with the particular languages I have a
background in, I can normally reason through Kotlin code a little easier than I
can with Java. Java occasionally leans on names for things that I often don't
see in Ruby, Elixir or Javascript (for example: `Bean`, `Factory`, `Impl`) to
establish context about what the thing is doing, whereas Kotlin has a few more
features to just express the context of what the thing does in more succint
code. 

Having said that, I wouldn't discard Java out of hand. For agency work, Java
will be more suitable for some organisations, especially those that have
in-house Java ability. If I'm looking at an application that's going to have a
lot of hooks into libraries or APIs (device OR web), then I know that I'm going
to have more choice of mature libraries within the Java ecosystem. I can call
into those libraries from Kotlin, but there will frequently be a slight
disconnect in the structure and way that the Java APIs are called. 

#### Use XML layouts

It's pretty hard to avoid this these days, but it's still a pattern that I see
occasionally. XML layouts are a fantastic way of seperating presentation from
logic, and have the added benefit of being able to target different device
types, screen sizes, languages, and the like.

#### Use XML strings files

This advice is firmly in the "do as I say, not as I do", since this isn't
something I do as often as I should - in a similar way to Rails and I18n in
fact. Nevertheless, having all the strings used for presentation and messaging
in the application in one place makes it easy to customise these based on the
device locale, or something hardware-specific like the screen size (for example,
if a button is going to be in a different place, it may have different wording).


#### Abstract into fragments, don't start with them

Again, this advice is based on _my experience_, and might not be true for
everything and everyone - but I've found fragments a useful way of abstracting a
piece of functionality and UI that can be isolated from the rest of the
application, rather than building everything up as fragments as a lot of the
Android documentation will have you do. 

Fragments introduce a whole other state lifecycle to keep an eye on. Rather than
introduce this complexity for everything, I stick to using fragments for
managing a discrete piece of the application UI that would pollute the activity
it's in if it was built straight in. A good example of doing this would be a
video player, where it has UI (the video surface and controls), and a bunch of
underlying logic about playing/pausing/skipping forwards and backwards.

#### Use Plain Ol' Java Objects (POJO) whenever possible

This is definitely a pattern that exists in the Java ecosystem, but I had the
value proved to me in the Ruby world first, and brought this into the Android
development context. Keeping any kind of isolatable code in it's own object and
passing external dependencies into the class constructor leads to a nice generic
class that can easily be reasoned with, refactored, and tested.

#### Background processing

My experience with this on Android is low enough that I just have a general
convention. If I need to just do something 'in the background', I used to just
use an
[`AsyncTask`](https://developer.android.com/reference/android/os/AsyncTask) -
although it now looks like these are replaced with [Kotlin
coroutines](https://developer.android.com/topic/libraries/architecture/coroutines)
and `java.util.concurrent` (I'd probably initially focus on `Callable` and
`Future`). 

For cross-activity async communciation and processing, I've had a good
experience with [EventBus](https://github.com/greenrobot/EventBus). This just
allows async message passing between anything that can access a `Context`, so
it's quite nice for accessing different functionality from different levels of
an application - a little like a React context.

For background processing (e.g. a scenario where an activity is not assumed to
be running), an
[`IntentService`](https://developer.android.com/reference/android/app/IntentService)
as worked well for me. If I need to trigger this based on events from the OS or
other activities, I'll combine this with a
[`BroadcastReceiver`](https://developer.android.com/reference/android/content/BroadcastReceiver).


#### Testing

Testing on Android is something I've always been really impressed with compared
to my experiences on iOS. There were several popular community libraries for
Android testing, and the Android community has made real efforts to get behind a
few good tools recently, backing these up with integration into tooling and
first-class documentation.

For Android testing these days, I would initially reach for unit tests with
JUnit. These types of tests use a stubbed version of the Android API, meaning
that nothing can be done that actually requires hardware - however for testing
POJOs and classes or activities that can have mocked or stubbed dependencies
injected, the ability to run these tests quickly makes them the most valuable
for developer quality of life.

One of the disadvantages of unit tests (common to all languages and frameworks
by the way, not just Android), is that they are limited to testing how specific
code paths interact, and don't take into account variability around the
different states of storage, memory, power, and other factors that can influence
how an implemented feature functions.

For these testing behaviour at this level, Android now has really good
documentation and support for
[Espresso](https://developer.android.com/training/testing/espresso). Espresso is
to Android as Capybara is to Ruby or Cypress.io is to Javascript. Espresso can
start an Activity and interact with an app on-device. These types of test are
slow to build and run - they basically install an APK on an emulator or device
attached over USB or Wifi, and then send events to the application simulating
user input. The advantage of having these tests is that they can be run on
different device form factors (phone, tablet, TV etc), different Android
versions, and test actual user input and interaction rather than assuming the
code path being executed.

As with testing for web, getting the mixture of tests right is the tricky bit.
There is arguably a bit less variability to worry about with native apps, so
writing a couple of smoke tests that just exercise the core workflows of a
simple application, with unit tests covering common code paths and edge cases,
normally hits a good balance of functionality coverage and test runtime. More
specific UI tests can always be added later to cover more specific scenarios or
edge cases that can't be reached by unit tests.


#### Store & Distribution

Generally, getting an app published in Android is a bit simpler than iOS. My
playbook here isn't super comprehensive, as it mostly just works. A release
build is what should be uploaded to the Play Store - a release build can be
generated from within Android Studio, or via `gradle` in a terminal. Either APK
or AAB (Android App Bundle) can be produced - AAB is the newer format and is
probably the way to go for Play Store distribution these days. If you are
distributing builds elsewhere (internal or an alternative store such as
[f-droid](https://f-droid.org/)), publishing APK files might offer slightly more
distribution options.

Part of producing a release build requires either generating or selecting a
keystore and key within the keystore. I keep keystores in 1Password so that they
are backed up. They're easy to drop onto a filesystem and forget about, so make
sure it gets put somewhere safe right away. I also generate a new keystore for
each application I distribute - this makes it easier to transfer management to
somebody else, or otherwise allow others to help with distribution, without
affecting any other applications I am also publishing.

These days, the Google Play store can manage signing applications for you. You
need to opt-in to this, and if you are distributing something that is
open-source or otherwise in the public domain, you should probably keep Google
at arms length here. In any other situation though, having Google Play manage
signing for you makes this a lot easier. The key is stored and used by Google,
so you don't need to manage this yourself. Additionally, anyone else with access
to the project in the Google Play Developer Console can sign the application
without having to distribute the keystore. 

Before an application can be published, quite a lot of metadata needs to be
created - application icons, marketing images (screenshots and feature image),
descriptions, privacy policies and more. I recommend keeping copies of this
metadata in the same repository as your application. Keeping this information in
a directory compatible with
[fastlane](https://docs.fastlane.tools/actions/supply/) also means that you can
automate this in the future if you wish.

### iOS

Being an ex-iPhone user and ex-Macbook user, my iOS development experience is
pretty stale now, so my playbook for iOS development is pretty brief.

#### Use Swift

Swift is a really nice language for native development, and I would strongly
prefer it to Objective-C. It's also getting a lot more community support. Swift
is to Objective-C as Kotlin is to Java - the two languages are surprisingly
similar in the types of pain they aim to solve from the original languages.

#### Use fastlane

Generally, I've found the iOS developer tooling to be kind of horrible. There's
a lot more options to debugging build issues in XCode, but trying to get things
working on the command line can be quite difficult.

Fastlane is a suite of tools that abstracts over some of these details to make
it a bit easier to work with iOS applications via the command line OR XCode. It
takes a bit of setting up, but once it is there, it makes it significantly
easier to onboard and offboard developers and get features shipped generally. 

#### Use storyboards & layout tools - don't layout with code

There's a bunch of iOS apps that leverage the fact that iOS devices _used_ to be
a pool of 3-5 devices with a fairly small set of resolutions to support. So,
getting away with defining a layout for a phone and a layout for a tablet was
possible. This is unwieldy these days, and makes an application really scary and
hard to maintain. Instead, use some of the tooling introduced to XCode for
laying out UI and viewcontroller interactions. These tools usually allow for
some flexibility of screen resolutions and device configurations without needing
to hardcode everything. 

In conclusion, for iOS I have always had an OK time up until I had to start
compiling and distributing the app. Once I get to that point, I'm always going
to start leaning on third-party tools - it just makes things so much easier.

### Flutter

Disclaimer: I've built a single app in Flutter: https://gitlab.com/joshmcarthur/emote, however:

* It's released on Android and iOS
* It has users
* It has automated unit and functional tests

Because of this, I feel pretty confident in how I would use it. As native application frameworks go, I have found it to be absolutely fantastic, compared to anything else - "standard" native development included. 

Flutter applications are written using Dart, which is a mature and flexible language that closely matches the style and API of ES6, with optional typing. Flutter applications compile to native bytecode, so aren't just embedded webviews like Cordova applications are. Flutter if conceptually similar to React Native, however I have found the documentation, community support and developer tooling significantly better - a large factor of all this being that Flutter and Dart are both contributed and maintained by Google. 

A particular area where Flutter shines is apps that are aiming to ship quickly and wish to use the native UIs of the platform they are running on. Flutter has built-in support for either Material or Cupertino theming and widgets - this means that these native widgets are quick and easy put together, and integrate really closely with the look and feel of the platform.

From my experiences so far of Flutter, here's the guidelines I would follow:

#### Seperate view components early

With Flutter, widgets are defined in a nested tree-like structure. It's quite easy to end up with a complex widget tree by just starting to add to `main.dart` right after starting a new application. Instead, because widget trees can be assigned to variables, split into files and required in as necessary, it's best to abstract specific parts of the UI into their own files as soon as possible. This helps to keep the actual scaffold definitions nice, clean and easy to understand. It also helps to encourage thinking around reuse of UI modules, since they're already in their own file.

#### Autoformat Flutter files

The `flutter` CLI tool, the launch point for any flutter build or run step, also
includes a formatting command - `flutter format .`, very much in the spirit of `go fmt`. The formatting
tool will clean up extra blank lines, inconsistent indentation, and tidy up
imports. I run this tool as a precommit hook, so my files should always be
autoformatted before anything is pushed.


#### Lint Flutter code

While `flutter format` takes care of the formatting, `flutter`'s built-in linter
takes care of a number of stylistic and syntactical checks, including unused
variables and imports, required widget attributes, and even recommendations of
improvements or refactors that are possible.

I run the linting step as part of continous integration. This means that my
build will fail if the linter has a recommendation to address, so I have the
opportunity to fix it up. I'm likely to move in the future to also running this
as a precommit hook.

#### Functional tests with flutter_driver

Flutter has a built-in testing library that is very capable at unit and simple
integration tests. The point where the built-in testing doesn't work out so well
is when a widget requires something that is provided by a device or emulator -
typically, sensor or storage access. 

Just like my approach to testing in general, a certain amount of this can be
worked around using dependency injection, mocking and stubbing. This is what I
did for Emote by passing a database instance into my repository objects,
allowing these to be tested without needing an actual database. Eventually
though, it's important to actually have a couple of tests to make sure that the
app starts and the main behaviours work correctly. This is where
[`flutter_driver`](https://api.flutter.dev/flutter/flutter_driver/flutter_driver-library.html)
comes in. Flutter driver will build and run the application on an actual device
or emulator. One of the neat things about Flutter is that because it supports
multiple platforms, a flutter_driver test suite can be run on one or more
Android emulators or devices, and then one or more iOS simulators or devices -
in other words, a test suite that can be run across a range of supported devices
to ensure that behaviour is functional across phones and tables, iOS and
Android. The `flutter_driver` package also supports taking screenshots along the
way, which can be useful for publishing upcoming features, or for use in the app
store listing. 

#### async/await & Future

async/await is a language primitive adopted from ES6. Promises are also
supported, but are named 'Futures'. They otherwise have a similar behaviour,
with the ability to use either async/await, Futures, or a mixture of both to
perform actions like HTTP requests, database queries and file loads
asynchrously. Support for asynchrous operations is built all the way down into
core Flutter widgets, with the ability to have a widget that automatically
re-renders it's children when the future passed to the builder resolves (either
successfully or unsuccessfully). This allows for very easy implementation of
loading UI, for example.

#### Flutter on Desktop and Web

I'm putting this into my playbook because it's something to watch, especially
given the prevalence of desktop applications being shipped using Electron.js.
Flutter already supports building desktop applications created just like mobile
applications for Mac OS, with Linux and Windows support in progress. 

Desktop builds operate the same way as mobile apps - being compiled to native
bytecode, and therefore with performance that is on par with apps created in the
operating system's natively-supported language(s). While Mac OS is the only
supported system at the moment, when Linux and Windows support is released, it
will be possible to produce binary releases for all three platforms from a
single codebase, just as is possible for Android and iOS flutter builds already.

Flutter has also supported building for web for a little while. Again, this is a
very interesting concept, since it allows for native & web support for
functionality within the same codebase. The main blocker on Flutter for web is
waiting for third-party libraries to add web support - while nearly all of the
popular libraries already support building for web, it's possible to run into
cases where there's no support. The way that Flutter builds for web is really
interesting, and an indicator of how they shipped support for it relatively
quickly. Rather than transpiling the application source code into equivalent web
operations, Flutter instead implemented a drawing layer using DOM, Canvas and
CSS that could render widgets. A Flutter build for web essentially transpiles
the Flutter core framework, libraries and application source code from Dart to
Javascript, and then leverages this render layer to present the application as a
web application.

Builds for web are definitely intended to present applications, rather than
content, so I would use them with care. Situations where I see them being useful
is for embedded purposes, demonstrations (e.g. the ability to 'try out' an
application in-browser before installing an app), prototyping, and user
acceptance testing.

If it seems like I've written about Flutter much more than iOS and Android -
that is because I have had such a fantastic experience with this tool compared
to my previous native app experience. While I plan to maintain my existing
Android development knowledge, I really don't see myself _not_ using Flutter for
native application builds without an extremely compelling reason not to.