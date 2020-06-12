---
layout: default
title: PostgreSQL
parent: "Databases & data"
nav_order: 20
---

Work in progress
{: .label .label-blue }

PostgreSQL is the default datastore for me. I find that it's capable, performant, stable, and there's a bunch of applications and frameworks that integrate really nicely with it. It has fewer weird gotchas than MySQL does, and has a clearer licencing model, being predominantly open-source.

Along with standard SQL features, Postgres is pretty good at adding extensions, functions and syntax that extends the standard featureset with additional capability. This allows for some neat performance or data modelling tricks that could only be accomplished with Postgres. The down-side of this is that there is some lock-in to this database engine. For this reason, frameworks such as Ruby on Rails tend to only support core functionality that is offered by the main database engines, leaving it up to library developers to extend the database integrations to add engine-specific functions.

The list of features that Postgres offers is huge. I'm always discovering little bits and pieces. The techniques I've listed here are ones that I have found myself either using, or considering using repeatedly. They seem to fit particularly well with my role as a developer. There are entirely different corners of Postgres to explore if you are in a DBA/analyst role!

### Serialized data columns

Postgres has the ability to store a subset of data in a serialized column. Typically, this is either an array column, or a JSON or JSONB column. 

Array columns are great for storing data that is of a known type. I typically will use array columns for lists of strings or numbers. Postgres has [specific functions and operators](https://www.postgresql.org/docs/current/functions-array.html) for arrays, however I normally interact with array columns via Rails' ActiveRecord library, which allows me to mutate Ruby arrays and have these reflected back into the column. 

Postres also supports both JSON, hstore and JSONB. All three formats are designed to store schemaless JSON data in a single column, but with minor differences:

  * hstore is an additional module to Postgres, so isn't db-native. It was introduced before JSON and JSONB, so is sometimes still seen, but is mostly superceded now. hstore only stores key value pairs (e.g. no arrays), and keys and values can only be text or NULL.
  * The JSON format supports the full JSON syntax, but is not especially optimised in the database for querying. It is essentially stored as text and parsed into a JSON object when required for querying. Because of this, it is faster to read and access, but [the JSON-specific functions and operators](https://www.postgresql.org/docs/current/functions-json.html) can take longer than hstore or JSONB operations. In other words, it is store-optimised, not query-optimised.
  * JSONB is similar to JSON, with the main difference being that the input JSON is serialized into a Postgres-specific optimised format before being persisted into the column, and is deserialized back into JSON when the column is SELECTed. JSONB support the same [operators and functions](https://www.postgresql.org/docs/current/functions-json.html) as JSON, plus some additional operators and functions that are specific to JSONB. All of these operators perform better than the equivalent JSON variant, since the database engine can work directly with the optimised format rather than having to parse the JSON each time. JSONB also has the significant benefit of supporting indexes on a JSON path, which can further increase performance of lookups of database rows when a JSON path is being used as the input for the query.

Given these formats, I will typically use `jsonb`, since I don't really need to consider what operators or functions may or may not work like I would need to if I was using `json`. I don't typically need to operate at a scale where I need to worry too much about the performance gains that `hstore` or `json` would provide, but do keep both of these formats on my radar, just in case. I believe that Hstore would offer the best performance, but only supports text values and key-value storage. JSON offers better read/write speed, but is slower to query. This means it may be better to use for _storing_ JSON data that does not need to be queried. JSONB takes longer to read and write, but can be queried and indexed all the way into the JSON structure.

> I needed to research this topic to write this section. [This StackOverflow answer on the differences between hstore, JSON and JSOB](https://stackoverflow.com/questions/22654170/explanation-of-jsonb-introduced-by-postgresql#22910602) was really useful for breaking down the differences, and I've paraphrased some of this information here. Previous to this research, the opinion I held was "Prefer JSONB over JSON". As discussed in this section, this opinion still holds, but it's useful for the rationale behind it to be explored.

---

* Materialized views are great for performance
* PGAnalyze with pev
* The capabilities of indexes
  * Partial indexes
  * Full-text indexes
  * jsonb indexes
* Geospatial
* Know how to examine the schema
* Backing up
* Optimise configuration