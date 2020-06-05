---
layout: default
title: Serverless
parent: Backend Dev
nav_order: 50
---

Serverless functions are a really good tool for simple tasks and APIs. They're a next step for me if I'm looking at something that needs to be scalable and available (e.g. ruling out [Google App Script]({{ relative_url | '/backend/google-script.html' }})). 

In terms of specific platform, at this stage I am not committed to anything in particular. I have used Lambda quite a lot, and Google Cloud Functions a little. Lamba + API Gateway is a powerful combination of tools, but I do sometimes find Lambda a little complex for a simple HTTP endpoint-type task. For this type of thing, I have been tending more towards Google Cloud Functions, since I have found the deployment and runtime process easier to wrap my head around.

One of the main goals I try and meet with serverless functions is to abstract away enough of the serverless cruft so that the core of the function can be run (and ideally tested) locally. From experience, I have found that deploying a function over and over again just to test it is frustrating. Instead, I prefer to start with a file that I can invoke locally, get the core result that I need, and then add layers on top of that until I have an interface that I can point GCF or Lambda at. 

This approach is not always possible if my serverless function relies on other cloud services - for example, a Lambda triggered by an S3 object upload, SQS queue message, or SNS topic. In this case, I'll create one or more mock objects, and use these to invoke the function, with a shell wrapper if necessary. These mock objects either compliment or are also used by unit tests.

In terms of language, I've tried a range of those that are available, especially for Lambda, and have found that an ES6-compatible Node.js is the best bet. Pretty much every single platform out there supports Node.js, and it has a well established dependency management tool in NPM that produces a local tree of dependencies, which is handy for packaging up a single deployment artifact. 

Python and Ruby are other candidates, however, both are somewhat more complex to deploy, and for the types of tasks I look to serverless functions for, don't really offer any particular advantage over Node.js (except in the case of Python, where I may choose this language if there's a Python library available for some kind of data processing or similar thing - Python dependencies around data processing and analytics are substantially further along than other languages due to Python's ubiquity in those fields).

For actual HTTP endpoints, if I've got a single-task function to run, I'll stick with normal Lambda/GCF invocation HTTP endpoints. I will and have used an API Gateway before for more complex cases, where I have a few endpoints to support, but not so many so as to justify a full backend application written in something like Rails or Phoenix. 

In this case, I'll stick with Lambda and put AWS API Gateway in front of it. API Gateway will take care of light params parsing and validation, routing, CORS, and API token authentication for me, saving a significant amount of boilerplate. The drawback of this is a response time penalty and an additional billing metric to keep an eye on. It's generally worth it. I don't deploy API Gateway by default though - only when I actually have multiple functions to route between.

Finally, as an abstraction tool, I almost always use the [serverless framework](https://www.serverless.com/). This framework introuces configuration in YAML that ends up as a Cloudformation template, making it a lot easier to get all the necessary Lambda functions, API Gateway resources, IAM roles, permissions and policy attachments and all the other AWS stuff. Serverless also supports other providers, notably Google Cloud Functions and Azure. Moving between providers isn't seamless, since they all use slightly different names for the same thing, but it's more doable than if the functions were all manually provisioned. Serverless doesn't provide a _huge_ benefit to me, and if my needs are particularly simple I do tend to use a cloud function directly. What serverless does give me is a configuration language with which to express more complex dependencies - things like S3 buckets, bucket events, queue listeners, SNS topic subscriptions, additional IAM roles, API gateway settings, Lambda dependencies, layers, and settings - these can all be expressed in YAML, and applied with `serverless deploy`. 