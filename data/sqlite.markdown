---
layout: default
title: SQLite
parent: "Databases & data"
nav_order: 25
---

SQLite is a database that I don't use as often as Postres - simply because most of my work relates to web applications, something that SQLite can definitely do, but not in a way that makes it safe to use for normal production use. This is mostly due to the exact things that make SQLite so good for basically any other use - a SQLite database is stored in a single file, and is optimised for reads, with one write allowed at a time. 

There are a few applications within the web space where SQLite can be used. Prototype is a great fit for SQLite. Because the database is a single file, it can be easily backed-up, be distributed to others for analysis or data loading, and can even be included in releases for testing. 

The other area where I frequently use SQLite is in native application development, in Flutter, Android or iOS. Using an established relational database engine in these applications takes care of a lot of architectural and design concerns that otherwise need a bunch of thought. Again, a database being represented in a single file makes it easy and convenient to version, backup and allow sharing to the database. I actually find it kind of amazing to be able to use indexes, tables, views, and all the other features that a standard SQL database offers. I can transfer just about all of my SQL knowledge from Postgres with web applications to SQLite with native applications. 

One of the neatest features available in SQLite, just like PostgreSQL, is support for full-text search. Supporting full-text search unlocks all kinds of searching functionality that becomes possible, including in the native and embedded contexts I've just mentioned. I can also use this functionality when prototyping to test out how search would fit into whatever it is I'm prototyping - SQLite full text search supports a subset of what Postgres full text search supports, but all of the main functionality is there, including rank ordering, match highlighting, boolean operators, exact and prefix matches.

Just like Postgres, the SQLite CLI supports a bunch of handy commands. The ones I use frequently are:

* `.mode`/`.output` - handy for exporting CSV. `.mode csv` sets the row format to CSV, and `.output` will save to the filepath provided.
* `.tables` lists and filters tables in the database
* `.schema` outputs a represenation of the database schema in SQL format
* `.mode`/`.import` - the same as exporting, but imports data

SQLite is amazing for what it's intended for - so long as it's not asked to do something beyond it's designed use-case, it's a fantastic tool. It's my go-to any time I need to store data outside of a web application context (even then, I know it _would_ probably be fine, but I would need to switch to Postgres eventually, so I may as well start on it).