---
layout: default
title: Google App Script
parent: Backend Dev
nav_order: 40
---

Google App Script is one of my favourite little agile/prototypical development
tools. It is a free service offered by Google for integrating betwen different
Google services. To accomplish this, it offers streamined APIs and access to a
range of Google Services with much less set up than other environments. 

Google App Scripts can be created either as standalone scripts, or attached to a
Google Document or Form. If it is attached to a doc, it can be accessed using
special APIs such as `getActiveSheet()`, which means the document to read from
doesn't need to be hardcoded. This is nice, since it allows scripts to be
'attached' to Google Docs to add functionality.

Google App Scripts can be published in a number of ways. The most useful
publishing method is to publish it as a Web API. This produces an endpoint that
can be sent HTTP requests via GET or POST. There are also publishing options to
add functionality like menus and dialogs to Google Drive documents, and to
deploy as an API executable that is callable from Google SDKs for native
applications and a range of other languages.

Along with easy access to Google APIs, there are some utility APIs built into
the Google App Script runtime for logging and key-value configuration storage.
The logging is distributed and logs both local executions and web requests.
Error reporting is handled automatically, with error reports being emailed to
the project author. Google App Scripts also have a dashboard available for the
project that displays error counts, execution times, and other useful stats.

Google App Script, as of quite recently, also supports ES6. This means that some
nice language features like arrow functions, let/const, async/await etc can be
used when writing code. It also means that aside from any Google App
Script-specific APIs that are used, the code is pretty portable to be dropped
into a different serverless platform if more scalability is required.

There are some downsides however: the rate limits are quite low, and can't be
changed. Google App Scripts generally need to be edited online (e.g. there is no
filesystem access), and don't support version control like git or svn. Google
App Script supports including libraries that are also developed using Google App
Script, but does not support external dependencies like those from CDNs or NPM. 

Some Google SDKs have a nice simple Google App Script abstraction API built over
the top of them, but not all. Once you run out of official API support to
accomplish a task, there's not a lot of support. 

Overall, Google App Script functions are really good for quick serverless glue
functions that need to stay up and running for a long period of time at a low
volume. The cost is unbeatable, being free, and the logging and error reporting
is good enough to just leave it running and handle errors as they come up.

Because of the rate limiting, it is not suitable for use in production sites,
but can act as a web service to be consumed by other services, where the rate
that requests are made can be controlled. It is also good for use in webhooks or
other triggered events, where having a zero-maintenance endpoint available is
the real convenience. SSL is of course supported (mandated, actually), and the
simple access to HTTP requests and responses allows receiving and sending query
params, body params, JSON payloads and HTTP headers. 

I have have great success using Google App Scripts for:

* Logging travel times between my home and work. This script has been running
  with very little downtime and absolutely no cost every 30 minutes for the last
  5 years, feeding into a spreadsheet that I can download at any time for
  analysis.
* Taking a form submission and posting the result to both a Slack channel, as
  well as direct messaging users based on the submitted form data.
* Auto-populating a Google Form with radio button options using data from a
  spreadsheet. The Google Form auto-updated in near-realtime as the spreadsheet
  is modified.
* Geocoding addresses entered into a spreadsheet
* Reverse-geocoding latitudes and longitudes entered into a spreadsheet
* Syncing data to Google Fit
* Scraping HTML for an image URL that is dynamically updated with a random
  filename, and responding with the image file.

Generally speaking, Google App Script will be the tool that I reach for if I
think that I need to accomplish a single, clear-cut task, and don't think I'll
need the full scale of a serverless function. I can get started straight away, I
don't need to worry about SDKs, OAuth and all the other hoops to jump through to
access Google services, and I can deploy onto Google-quality infrastructure with
SSL and versioned endpoints - all for free. If I end up needing to move it to a serverless function, then I can take the ES6 code that I've already written, replace the Google App Script API implementations with whatever the Node.js equilvant is (in most cases, this is the relevant Node.js Google SDK + OAuth/service account JSON), and push to whatever serverless platform I'd prefer.