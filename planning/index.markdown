---
layout: default
title: Estimation & Delivery
nav_order: 15
---

## Principles

* Make decisions based on what is best for the user, then what is best for the client, then what is best for myself. 
* A product owner, a single person appointed by client to represent the organisation makes a massive contribution to project success.
* Estimate effort, not time.
* Work closely with business development and clients - having a good relationship with clients practically guarantees a high quality and accurate plan.
* I write down everything that I assume. This will sometimes be the only context that can be accessed by others to get an idea of my thought process behind a feature.
* Plan features, not requirements. If the features need to be quite low-fidelity, that's OK. Requirements aren't actionable by a developer, so they're not particularly useful to try and map effort against.

## Estimation Techniques 

##### Effort, not time

Effort is relative, and can also be treated logarithmatically rather than linearly. Effort can be translated into time if absolutely necessary, but focusing on what is easier or harder than something makes the planning process significantly more productive. 

##### Features, not requirements

A requirement such as "The system must securely limit access to authorised users" doesn't actually express any particular set of features that need to be built. There's numerous ways to interpret this, and providing an estimate against the requirement only provides no indication of the interpretation.

Instead, I initially try and translate a set of requirements into an overview of a project. Idealistically, this is a solid project vision, but at least a succinct description of what the project represents. 

From here, I start taking those requirements, and bundling them into epics. The epics represent logical groupings of features, and these will provide valuable context around the complex parts of the project, as well as begin to identify dependencies and delivery ordering. 

Once requirements are represented with epics, a high-level size can normally be represented at a number of development sprints level. A development sprint for me is typically two weeks, including planning, review and retro. For example, a user authentication epic, assuming it contained some customisation, may be sized as one sprint. 

Sometimes, sizing to development sprints is sufficient to provide an estimate and breakdown of the project delivery. More frequently, epics need to be broken down to features. During this process, features should still be left as high-level as possible, while providing room to break down into smaller parts, or alternatively recording the smaller parts as sub-tasks within the feature.

Feature estimation should be continue to be based off points of effort, rather than hours to deliver a feature. Effort can be scaled up or down, and allows for team members of different capabilities to have an idea of the project delivery commitments they may be able to make. 

##### The user comes first, then the client

Any project should always be oriented around delivering what users need, with the best possible experience. Having buy-in on this from the full stack of project team members in my experience makes for a fantastic attitude through project delivery.

The next consideration that I consider is the best action I can take for clients. I'm a believer that clients value me for the experience and knowledge I can apply to their problems, not for the amount of quality of code I can produce. For this reason, it is important to me that the correct solution for the client's need is identified - not the thing that may provide the most immediate financial benefit. Clients who value these skills are often those who end up having an active and ongoing relationship that continues to deliver benefit to my employers, both financial and via feedback and positive experience passed onto others.

## Delivery techniques

I am most accustomed to the [Scrum methodology](https://www.scrum.org/), but I recognise that every project is different. The most valuable thing I can bring to the table is a general collection of techniques that I can mix and match based on what is required. 

I believe the following ceremonies/rituals, as I interpret them, benefit all projects:

#### Product owner

A product owner does not need to be skilled or experienced in product ownership, but do need to believe in the project, have time to work and contribute as a **key member** of the delivery team, and have sufficient authority and autonomy within the client organistion to be able to unblock delivery at any point.

I've worked with a range of different product owners, and the most important attribute I can ask for is a genuine sense of ownership and investment in project success. This sense tends to infect the whole of the delivery team, usually with great results.

#### Sprints

Working iteratively is important, regardless of the project. Sometimes, work is being identified based on user feedback just ahead of delivery. More typically, we have a planned pipeline of work, and the iteration is instead focused on making delivery more efficient. 

Sprints provide a nice way of working within a timeframe while still having a fun, no-pressure name that represents what they are.

While I have worked with one-week sprints before, I usually find that with the type of client engagement I am usually working with, that two weeks provides more time for communication and feedback loops. 

#### Backlog grooming

I find backlog grooming is good to do on alternating weeks when sprint planning/reviews are not happening.

If I had to pick one process to keep, this would be it. Backlog grooming gives me a chance to get down into the weeds of features that are planned for the next sprint, and make sure that myself and client have a really good understanding of what the feature "is". 

The discussion around each story goes into assumptions, acceptance criteria, development tasks and story descriptions, but it's the communication that is really valuable. 

I have done backlog grooming both with the whole dev team and with just myself and the product owner. I personally prefer the relationship building and efficiency that comes with backlog grooming just with the product owner, but this does require follow-up work with other project team members to ensure understanding ahead of sprint planning.

Backlog grooming has the secondary effect of identifying missing information or dependencies before the sprint begins, giving time for the product owner to unblock the feature so it is ready for development, or to otherwise re-shape the sprint to work around the blocker.

#### Sprint planning

Sprint planning should normally be a quick and painless process. Aside from anything else, it's a chance for me to get together with the team I'm working with and run over the stories, deal with any blockers, questions or clarifications, ultimately resulting in a planned list of features we plan to build for the sprint, and a goal we'll try and hit.

I use the time required for sprint planning as a metric for judging the quality of the backlog. For a two-week sprint, a planning session should take between 30 minutes and an hour. Any longer than this is normally an indicator that there was not sufficient detail in the features ahead of time, resulting in discussion that needed to happen prior to planning.

> Efficient grooming and planning processes is something that I continue to work on - typically for me, planning will be significantly longer at the beginning of a project than towards the end, and I'm making efforts to even this out.
{: .text-grey-dark-000 }

#### Sprint review

Just like planning, review should be quick and painless. During the sprint, the team should be delivering features and working with the product owner to check they are accepted. Review should be the opportunity for the team to check over the tasks that have been delivered, demonstrate features that have been delivered (and _only_ features that have been delivered, not work in progress!), decide whether the sprint goal was met, and close off the sprint. Having a discrete finish point of one sprint, and start of another, is incredibly important to me, so I really get a lot of value out of this session.

#### Sprint retrospective

Retrospectives tend to be the trickiest of sprint processes, the trickiness being directly proportional to how the project is going. If it is going well, retrospectives are a wonderful way to fill an hour with back-patting and showering each other with compliments. This is useful, as long as it is temporised by identifying things that didn't work as well as they could have, no matter how minor. For projects not going well, it's crucial to facilitate feedback that describes symptoms based on individual experiences that lead to underlying problems that can be identified and resolved. Person to person feedback is not part of a sprint retrospective, though it may be wrapped around it. A sprint retrospective is the project team providing feedback on how the delivery process has gone, and how to make it better. 

There's a tonne of retrospective exercises available, see the tools section below. A "Good", "Bad", "Change" exercise is universally suitable, as different aspects of "Good" or "Bad"/"Change" can be focused on to accentuate parts of the project that are going especially well or not so well. There are a range of variants to this that identify positive and negative influences on project delivery. 

I've had wonderful experiences with some expert scrummasters who have taught me the most valuable lesson I have taken away from retrospectives - don't discount the team building opportunity that retrospectives represent, even if a project is going terribly. Introducing drawing, letter writing, using different materials and mediums, can all help to unlock different thought processes to get really accurate and valuble insights. 

Finally retrospectives should always have follow-ups. If a problem has been identified, and a potential solution suggested, a member of the project team should volunteer or be elected to put the solution into place. This provides an opportunity for the whole team to own and tweak the process, and adjust things to fit the specific needs of the project. 

## Tools overview

* I love to hate [jira](https://www.atlassian.com/software/jira), but it's so ubiquitous now that it kind of works, surprisingly. It is _ridiculously_ slow though, as it is a heavy enterprise project management tool that has had agile processes bolted on top. I use the [Jira CLI written in Go](https://github.com/go-jira/jira) to quickly look at issues, since this beats loading the issue from a scrum or kanban board 100% of the time.
* I would love to use Github or Gitlab issue boards more - and in fact when I've been working with a technical team, this has worked great. It's never worked well for me with a mixture of team member role backgrounds though.
* [https://www.funretrospectives.com/](https://www.funretrospectives.com/) is a blog/ideas platform that has a bunch of new ways to tweak retrospectives, especially if there is a particular outcome a retrospective needs to work towards.
* Planning poker cards were [created](https://www.mountaingoatsoftware.com/agile/planning-poker) by Goat Software - they are cards with the Fibonacci sequence from 0 to 13, and provide a nice consistent way for a group of people estimating points to work within a consistent scale. 
