---
layout: default
has_children: true
title: "Databases & data"
nav_order: 30
---

Work in progress
{: .label .label-blue }

Managing data seems to be one of the things I find myself doing more and more
of. I suspect there is a relationship here between my experiences on personal
and professional projects, as I find more specialised tools and techniques for
specific aspects of data mangement, instead of reinventing the wheel with a more
general programming language. 

The overarching theme to my learning over the past few years has been to lean on
whatever functionality the database provides - as long as it can be versioned
and managed in source code along with the rest of your application. If the
database engine being used supports validations, associations, views,
pubsub...there's very little excuse to not learn and use them. They will almost
always represent a sensible level of abstraction away from your application,
will perform better, and will be compatible with other database clients,
languages and programs, leading to a more flexible solution.

In other words, I let the application handle the business logic, and let the
database take care of the data that logic creates and depends on. 

Generally, before data enters into the database, it requires some preprocessing.
A significant component of my experiece with data management relates to the
tools and techniques I have picked up not in working with data once it's in the
database, but with cleaning, formatting and slicing data into a shape where it's
well-formed and ready to be dumped into the database. For working with large
files, I really can't understate the benefits that come from picking up some
basics of popular command line utilities for quickly and easily transforming
text data. I go into more depth into this tooling in the ['Preprocessing'
section]({{ "data/preprocessing.html" | relative_url }}), but I think that
learning how to transform raw data is the most valuable and flexible piece of
knowledge I hold.

The database engine that I use almost exclusively is PostgreSQL. Because of
this, I lean into using all the nice functionality that PG provides. I do this
specifically because I build web applications using frameworks, rather than
creating those frameworks myself. I can use the knowledge of the requirements of
the application to assess the database engine features I need, without needing
to be compatible with multiple database engines. This is a distinct advantage
for web applications rather than applications and frameworks that are intended
for broader audiences and therefore need to use ANSI-SQL and/or database
compatibility layers to make sure that functionality works across multiple
database engines. The specific Postgres features I regularly use are covered in
the ['Postgres' section]({{ 'data/postgresql.html' | relative_url }}).

I have a great deal of faith in PostgreSQL to do at least an acceptable job of
most data storage, maintenance, and querying operations. I've been involved in
migrations _to_ Postgres, but never _away from_ the database. I also feel secure
in the knowledge that Postgres compatibility is available in alternative
engines, such as AWS Redshift and Aurora, should I have the need for more scale
or Big Data™ processing. 

For embedded/native situations, PostgreSQL isn't really suitable - it's designed
for client/server usage, with clients connecting to a central server. While this
is certainly doable via an API, typically data should be stored on-device, so
that it is always available locally regardless of the network connectivity
status of the device. For these situations, SQLite is my go-to. While it has a
slightly different feature set than Postgresql, it offers a stable and
well-documented API to store and retrieve data from a local database. There's
also plenty of tooling available on native platforms for migrating, backing up,
restoring and analysing SQLite databases. The specific SQLite features I
regularly use are covered in the ['SQLite' section]({{'data/sqlite.html' | relative_url }}).

* Indexes
* Constraints
* Syntax
* Schema design up-front
* Serialisation formats
