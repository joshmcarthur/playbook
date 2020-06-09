---
layout: default
has_children: true
title: "Databases & data"
nav_order: 30
---

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
regularly use are covered in the ['SQLite' section]({{'data/sqlite.html' |
relative_url }}).

Regardless of the database engine I'm using, there are a few SQL features and
techniques that I consistently use:

### Indexes

Indexing data has both a technical and non-technical benefit. From a technical
point of view, indexing allows the database engine to optimise the data that is
being stored in a way that is much faster to look up a particular way. An
example of this is the primary key index that exists on a table's primary key.
Because the database can anticipate that a record is likely to be looked up by
it's primary key, it can arrange the data in a way that it can either jump
straight to the record it's after, or to a very small set of likely results. 

From a non-technical point of view, indexes both provide an opportunity to
consider, and demonstrate that the developer has considered, the access patterns
for a particular table. The presence of indexes on a table provide hints to
schema readers about how a table is supposed to be used. Conversely, the lack of
indexes can show that a table or group or tables was possibly put together in a
hurry without understanding how the data would be looked up, and that there are
some potential quick wins by adding indexes to these tables.

The negative of adding indexes is that writes are slowed down by the database
needing to update each index when a relevant row of an indexed table is written
to (e.g. INSERT or UPDATE). Therefore, while too few indexes can indicate a lack
of understanding of the data structure, too many indexes can indicate exactly
the same thing - perhaps a scattergun approach intended to try and solve a
problem without understanding the underlying cause - potentially having a net
negative impact on database performance.

The guideline that I follow when indexing is that I should be able to justify
each index - with a comment, if it's not immediately obvious from the index name
(although if it's not obvious from the index name I am more likely to rename the
index than to add a comment). If I am adding an index just for the sake of
having it indexed, I'm not necessarily gaining anything, and may be introducing
a harmful pattern that others may inadvertently follow themselves.

> There are a lot of other tricks to indexes, but they tend to be more specific
> to the database engine. I go more into specific index types in the ['Postgres'
> section]({{ '/data/postgresql.html' | relative_url }}).

### Constraints

Constraints are rules or checks that can be put into place in the database. In
my experience, constraints tend to be underused or ignored entirely in web
application frameworks where data rules and validations can be implemented in
code instead of data, which I think is unfortunate, as constraints offer a
reliable method of ensuring data integrity. 

Common constraints are `NOT NULL`, `UNIQUE` and foreign keys, which are
typically attached during column definition and schema design. Frameworks such
as Rails often support these, since these types of constraint work regardless of
the database engine.

Depending on the engine though, much more complex checks are possible. The issue
that prevents their use more widely for me is that Rails uses a Ruby format to
represent the schema by default. This means that only database primitives that
the ActiveRecord library is aware of can be serialized and deserialized into
Ruby code when snapshottting the database. It is possible to change the schema
format to SQL, but this leads to a file that is much harder to work on amongst a
development team since even minor database differences can cause git conflicts. 

Nevertheless, even if constraints and checks cannot be used _widely_ in a web
application, they are a tool I constantly keep in mind. Constraints I have used
before include:

* Checking a value is included in a list of values (enum)
* Checking a value based on the presence of another value

### Consistent formatting

ANSI SQL is pretty flexible when it comes to formatting, and even syntax to a
certain extent. Something I always do though, is stick to some basic formatting
rules:

* SQL keywords in UPPERCASE (`SELECT`, `INSERT`, `INNER JOIN`)
* A short line length with plenty of line breaks.
* Break and indent at logic points (e.g. on a select, update or insert statement
  component - `WHERE`, `ORDER BY`, etc)
* Double quote relation and column names
* Column names underscored, lowercase
* Plural table names
* Singular column names

These rules are pretty common across the community, but they are also not
enforced as part of either the database client, libraries, or the SQL standard
itself, so it's important that I hold myself to these to ensure database queries
that I create are clear and easy to write and maintain. Sticking to a consistent
format also helps to communicate the intent and main structure of the query.

### Schema design

Ad-hoc schema design is tempting in the guise of moving quickly, but I've always
found that planning the database schema in advance pays off. It allows a chance
to get a feel for potentially problematic parts of the model and refactor these
in terms of the database relationship structure before any code actually gets
written. Moving the schema design to be a planning process is also a great
communication and learning opportunity for other programmers, product owners,
and other stakeholders. For particularly focused or complex workflows, database
interactions can be simulated via pen and paper in schema design to see how
pieces of data can fit together.

Schema design won't be correct right off the bat, and changes and corrections
can always be expected. By starting with a documented, or at least brainstormed,
database schema, these changes can be diffed and assessed against the previous
versions. By having previous versions, changes can be _proposed_ and _reviewed_
before the changes are actually made in code. 