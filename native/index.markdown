---
layout: default
title: "Native Dev"
nav_order: 35
---

Work in progress
{: .label .label-blue }

### Android

I've been doing Android development on and off for the past several years. I would class myself as a developer with a working, but not deep knowledge of Android. I am deeply interested in the ecosystem and devices around the OS, and so my programming knowledge is just a part of that. I went through a phase of running all sorts of custom ROMs (mostly Lineage variants), Tasker profiles and various things using magisk or supersu to get a root shell. I now use and appreciate the polish and stability using a stock Google-flavoured Android ROM on my Pixel 2 XL. 

My Android development was initiated with Cordova (named Phonegap when I started). I don't think this needs to be covered off since this is my playbook. I don't use it, and don't particularly recommend it to others these days either. I think it delivered plenty of value in the early days of showing what was possible with JS and a bit of native code bridging, but that niche is now well-taken care of with other tools. 

If Cordova _must_ be used, you might consider something like [Ionic](https://ionicframework.com/) that will give you a bit more documentation and tooling around keeping the mixture of SDKs, NPM packages and magical shell scripts up to date that are required to properly maintain such an application.

Since Android Studio was released (replacing, or, may I say, _eclipsing_ Eclipse + Android plugin), I use native Android dev much more. I have a few guidelines that I stick to with Android development:

#### Java/Kotlin?

I have no solid opinion on this yet, but would be inclined to use Kotlin if starting an application today. Kotlin is getting more and more developer support, documentation, and tooling, and with the particular languages I have a background in, I can normally reason through Kotlin code a little easier than I can with Java. Java occasionally leans on names for things that I often don't see in Ruby, Elixir or Javascript (for example: `Bean`, `Factory`, `Impl`) to establish context about what the thing is doing, whereas Kotlin has a few more features to just express the context of what the thing does in more succint code. 

Having said that, I wouldn't discard Java out of hand. For agency work, Java will be more suitable for some organisations, especially those that have in-house Java ability. If I'm looking at an application that's going to have a lot of hooks into libraries or APIs (device OR web), then I know that I'm going to have more choice of mature libraries within the Java ecosystem. I can call into those libraries from Kotlin, but there will frequently be a slight disconnect in the structure and way that the Java APIs are called. 

#### Use XML layouts

It's pretty hard to avoid this these days, but it's still a pattern that I see occasionally. XML layouts are a fantastic way of seperating presentation from logic, and have the added benefit of being able to target different device types, screen sizes, languages, and the like.

#### Use XML strings files

This advice is firmly in the "do as I say, not as I do", since this isn't something I do as often as I should - in a similar way to Rails and I18n in fact. Nevertheless, having all the strings used for presentation and messaging in the application in one place makes it easy to customise these based on the device locale, or something hardware-specific like the screen size (for example, if a button is going to be in a different place, it may have different wording). 

#### Abstract into fragments, don't start with them

Again, this advice is based on _my experience_, and might not be true for everything and everyone - but I've found fragments a useful way of abstracting a piece of functionality and UI that can be isolated from the rest of the application, rather than building everything up as fragments as a lot of the Android documentation will have you do. 

Fragments introduce a whole other state lifecycle to keep an eye on. Rather than introduce this complexity for everything, I stick to using fragments for managing a discrete piece of the application UI that would pollute the activity it's in if it was built straight in. A good example of doing this would be a video player, where it has UI (the video surface and controls), and a bunch of underlying logic about playing/pausing/skipping forwards and backwards.

#### Use <abbr title="Plain Ol' Java Objects">POJOs</abbr> whenever possible

This is definitely a pattern that exists in the Java ecosystem, but I had the value proved to me in the Ruby world first, and brought this into the Android development context. Keeping any kind of isolatable code in it's own object and passing external dependencies into the class constructor leads to a nice generic class that can easily be reasoned with, refactored, and tested.

#### Background processing

My experience with this on Android is low enough that I just have a general convention. If I need to just do something 'in the background', I used to just use an [`AsyncTask`](https://developer.android.com/reference/android/os/AsyncTask) - although it now looks like these are replaced with [Kotlin coroutines](https://developer.android.com/topic/libraries/architecture/coroutines) and `java.util.concurrent` (I'd probably initially focus on `Callable` and `Future`). 

For cross-activity async communciation and processing, I've had a good experience with [EventBus](https://github.com/greenrobot/EventBus). This just allows async message passing between anything that can access a `Context`, so it's quite nice for accessing different functionality from different levels of an application - a little like a React context.

For background processing (e.g. a scenario where an activity is not assumed to be running), an [`IntentService`](https://developer.android.com/reference/android/app/IntentService) as worked well for me. If I need to trigger this based on events from the OS or other activities, I'll combine this with a [`BroadcastReceiver`](https://developer.android.com/reference/android/content/BroadcastReceiver). 

#### Testing

Coming soon
{: .label .label-yellow }

#### Store & Distribution

Coming soon
{: .label .label-yellow }

### iOS

Coming soon
{: .label .label-yellow }

### Flutter

Coming soon
{: .label .label-yellow }