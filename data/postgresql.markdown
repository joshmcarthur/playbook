---
layout: default
title: PostgreSQL
parent: "Databases & data"
nav_order: 20
---

Work in progress
{: .label .label-blue }

PostgreSQL is the default datastore for me. I find that it's capable,
performant, stable, and there's a bunch of applications and frameworks that
integrate really nicely with it. It has fewer weird gotchas than MySQL does, and
has a clearer licencing model, being predominantly open-source.

Along with standard SQL features, Postgres is pretty good at adding extensions,
functions and syntax that extends the standard featureset with additional
capability. This allows for some neat performance or data modelling tricks that
could only be accomplished with Postgres. The down-side of this is that there is
some lock-in to this database engine. For this reason, frameworks such as Ruby
on Rails tend to only support core functionality that is offered by the main
database engines, leaving it up to library developers to extend the database
integrations to add engine-specific functions.

The list of features that Postgres offers is huge. I'm always discovering little
bits and pieces. The techniques I've listed here are ones that I have found
myself either using, or considering using repeatedly. They seem to fit
particularly well with my role as a developer. There are entirely different
corners of Postgres to explore if you are in a DBA/analyst role!

### Serialized data columns

Postgres has the ability to store a subset of data in a serialized column.
Typically, this is either an array column, or a JSON or JSONB column. 

Array columns are great for storing data that is of a known type. I typically
will use array columns for lists of strings or numbers. Postgres has [specific
functions and
operators](https://www.postgresql.org/docs/current/functions-array.html) for
arrays, however I normally interact with array columns via Rails' ActiveRecord
library, which allows me to mutate Ruby arrays and have these reflected back
into the column. 

Postres also supports both JSON, hstore and JSONB. All three formats are
designed to store schemaless JSON data in a single column, but with minor
differences:

  * hstore is an additional module to Postgres, so isn't db-native. It was
    introduced before JSON and JSONB, so is sometimes still seen, but is mostly
    superceded now. hstore only stores key value pairs (e.g. no arrays), and
    keys and values can only be text or NULL.
  * The JSON format supports the full JSON syntax, but is not especially
    optimised in the database for querying. It is essentially stored as text and
    parsed into a JSON object when required for querying. Because of this, it is
    faster to read and access, but [the JSON-specific functions and
    operators](https://www.postgresql.org/docs/current/functions-json.html) can
    take longer than hstore or JSONB operations. In other words, it is
    store-optimised, not query-optimised.
  * JSONB is similar to JSON, with the main difference being that the input JSON
    is serialized into a Postgres-specific optimised format before being
    persisted into the column, and is deserialized back into JSON when the
    column is SELECTed. JSONB support the same [operators and
    functions](https://www.postgresql.org/docs/current/functions-json.html) as
    JSON, plus some additional operators and functions that are specific to
    JSONB. All of these operators perform better than the equivalent JSON
    variant, since the database engine can work directly with the optimised
    format rather than having to parse the JSON each time. JSONB also has the
    significant benefit of supporting indexes on a JSON path, which can further
    increase performance of lookups of database rows when a JSON path is being
    used as the input for the query.

Given these formats, I will typically use `jsonb`, since I don't really need to
consider what operators or functions may or may not work like I would need to if
I was using `json`. I don't typically need to operate at a scale where I need to
worry too much about the performance gains that `hstore` or `json` would
provide, but do keep both of these formats on my radar, just in case. I believe
that Hstore would offer the best performance, but only supports text values and
key-value storage. JSON offers better read/write speed, but is slower to query.
This means it may be better to use for _storing_ JSON data that does not need to
be queried. JSONB takes longer to read and write, but can be queried and indexed
all the way into the JSON structure.

> I needed to research this topic to write this section. [This StackOverflow
> answer on the differences between hstore, JSON and
> JSOB](https://stackoverflow.com/questions/22654170/explanation-of-jsonb-introduced-by-postgresql#22910602)
> was really useful for breaking down the differences, and I've paraphrased some
> of this information here. Previous to this research, the opinion I held was
> "Prefer JSONB over JSON". As discussed in this section, this opinion still
> holds, but it's useful for the rationale behind it to be explored.

### Materialized views

All relational databases support views. Views are basically saved queries - a
view is defined with a query, and this allows a `SELECT` statement to be run
against the view as if it was a table. Some database engines (Postgres
included), also support writes back to the view that are resolved back to the
original tables - so long as all the necessary constraints are satisfied.

Views are a powerful enough tool in their own right - especially for reporting,
single-table-inheritance, and any other usecase where you need to represent data
from multiple sources in one place. The part where they can stop working is
performance. Views run the query they represent each time they are queried (this
is a bit of a generalisation since I'm sure there's some optimisation going on
here). This means that each select statement against a view will take as long as
they underlying query takes to run. For views that represent a large of complex
dataset, this can cause issues. 

[Materialized
views](https://www.postgresql.org/docs/current/rules-materializedviews.html) are
views that cache their result indefinitely. This means that the view query runs
once to populate the result, and then any future references to the materialized
view will use the cached result. Because materialized views are effectively
tables at this point, indexes can also be added to these views to further speed
up queries. 

The obvious downside of materialized views is expiring the cache when necessary,
however with database triggers or application-level instrumentation (e.g.
ActiveRecord callbacks, Sidekiq workers or other scheduled workers), this is a
simple operation. Postgres supports the `REFRESH MATERIALIZED VIEW` operation
for expiring the cache, so that it will run the underlying query and update the
result. There is however a performance impact to SQL statements using the view,
since the result is being recalculated, and queries accessing the view are
locked out until the result is recalculated. To not lock queries for as long,
the `WITH NO DATA` option can be specified. This will not lock the view
immediately, however the first subsequent query to access the view will lock it
while the result is calculated. 

If the data structure supports rows having at least once unique index on the
 materialized view, then there is also the option to `REFRESH MATERIALIZED VIEW
 CONCURRENTLY`. This differs from a non-concurrent refresh in that reads from
 the view will not be blocked while the materialized view is refreshed, and
 replaces the cached view concurrently - e.g. this command does not just expire
 the cache, it also recalculates the new result in the background. While the
 materialized view is being recalculated, queries accessing the view will use
 the previously-cached value. Becaused materialized views need to retain access
 to this existing data while recalculating the result, they tend to take
 substantially than a normal view refresh, so this also needs to be taken into
 account. This is especially true with many indexes, since the cached result
 needs to effectively be completely rewritten to the underlying storage. I have
 seen good improvements that are possible to materialized view refreshing (and
 query performance in general) by tuning Postgres settings to speed up index
 updates and writes.

Because materialized views take time to refresh, they are not really suitable
for situations where data needs to be available in real-time. In situations
where views are being considered anyway though, this is rarely the case, and
having a materialized view that is automatically updated on a schedule (hourly,
for example), and highly optimised for read access can be a great addition for
dashboards, reporting, and even direct application use.

In general, both normal and materalized views are a really powerful tool that I
have seen be underutilised, especially for web application development. They can
entirely replace complicated data models and caching layers in applications,
reducing the complexity of application maintenance and usually outperforming
equivalent data access patterns (e.g. querying multiple tables and combining
results in the application layer). 

Views are also treated as relations along with tables in Postgres. This means
that views can have the same roles and permissions applied to them that tables
have. I have utilised this before to expose a limited amount of data to a
less-privileged role. In this case, I used a non-materialized view, as the data
needed to be available in real time. 

### EXPLAIN ANALYZE

Even for queries that aren't underperforming (yet), taking some time to
understand how Postgres plans and executes queries has been incredibly
beneficial for me. It's kind of easy to deal with the abstraction that SQL
offers where data is requested, and then retrieved, while not really knowing the
detail about how that happens. 

EXPLAIN ANALYZE is a command available in most database engines. Postgres
supports a couple of neat extensions, including output as JSON, for example:

`EXPLAIN (ANALYZE, COSTS, VERBOSE, BUFFERS, FORMAT JSON) SELECT * FROM posts;`

These flags will include the normal EXPLAIN ANALYZE output, as well as more
specific metadata, and will output in JSON format. This is useful for
destructuring with someone like `jq`. If I'm analyzing in `psql`, then I will
normally leave the format as default, since I find it easier to read in a
terminal.

The output of an EXPLAIN ANALYZE call can be a little hard to read. Basically
any time I'm not just getting an overall runtime for a query, I use a really
neat visualisation tool called [pev](https://github.com/AlexTatiyants/pev) (live
version is [also available](http://tatiyants.com/pev/) so it doesn't need to be
setup and run). pev takes `EXPLAIN (ANALYZE, FORMAT JSON)` output and the
optional original query, and produces a graphical representation of the way the
database engine has planned the query, and the time to run each part. I find
this visualisation substantially easier to parse, and iterate on when resolving
a performance problem, or to get an idea of how a query is being planned and
executed. EXPLAIN ANALYZE can tell you what indexes and relations are being used
to put a query together, and the steps the engine must go through to prepare the
requested result. The output of this command is incredibly useful to experiment
with exactly what part of a query is causing an issue. I've frequently been
surprised by exactly what can cause issues, and the output of EXPLAIN ANALYZE
has let me see the problem straight away.

For simpler performance stats, enabling timing in `psql` with `\timing on` will
output the overall execution time of each query. I find this is a useful option
to have enabled whenever I'm using `psql`, as it lets me see the comparative
runtime of more complex queries I'm writing.

### Geospatial

Postgres has arguably the most capable geospatial database extension out of all
the relational database engines. There are certainly specialised tools that
perform more complex geospatial operations, but nothing that does just as well
as a relational database. 

This database extension is called _postgis_. It is available on _most_ database
hosting platforms (Heroku Data, Amazon RDS, Google Cloud SQL), but is _opt in_
and must be enabled using `CREATE EXTENSION postgis;`. It also requires package
installation or use of a specialized Docker image if you're managing your own
database.

Postgis has all kinds of capabilities, and I don't have nearly enough expertise
to go into here. I thought it was important to include into my Playbook because
it _is_ a tool that I use a lot - basically whenever I'm doing anything with
geographic or geometric data. 

> What's the difference between _geometric_ and _geographic_? _Geometric_ data
> is operating in a spatial context that is not scoped to a particular system -
> it's just points with an X and a Y. _Geographic_ data is specifically the
> context of world coordinates - they must fit within a partricular hemisphere
> and have a consistent unit (this depends on the spatial system used, typically
> meters). _Geospatial_ is a superset of both of these.

Rather than breaking down Postgis in detail, there's just a few things that I
regularly consider using Postgis:

1. Choose the correct SRID. The SRID is the identifier of the spatial system you
   are using. The spatial system identifies the bounds, the coordinate unit,
   accuracy, and the distance units. Different spatial systems are used in
   different parts of the world. In New Zealand, I most frequently run into
   WGS84 (SRID 4326) and NZGD2000/NZTM2000 (SRID 2193). Choosing the SRID to
   store data as will save having to transform every time the coordinate needs
   to be presented in a difference system.

2. `ogr2ogr` and `ogrinfo` are invaluable to get geospatial data into Postgis.
   These tools are part of the `gdal` library, and are very capable at
   transforming different types of files between formats. Typically, I use this
   tool to transform ESRI shapefiles or GeoTIFF to Postgis or GeoJSON. `gdal`
   can drop data straight into a Postgis table, and can be split or transformed
   further from there.

3. Be aware of geometric and geographic types and functions. Postgis basically
   supports two silos of geospatial concepts - geometric and geographic. The
   types can be mixed, but if the value from a geometric column is being passed
   to a geographic column (or vice-verse), then it will need to be cast. This
   can add a substantial performance overhead to queries. 

Essentially, a geospatial-heavy database requires a larger allocation of effort
for schema design. Consideration needs to be given for the type of data that
needs to be loaded, the geospatial system and format it is in, and the
calculations and queries that need to be executed. 

As a final note, Postgres DOES have a native `POINT` type that can be used. This
type has some limitations - it is a geometry type only - it stores an X Y
coordinate with no other metadata (e.g. spatial system). This also means that
any value is accepted, not just 'standard' latitude and longitude. The advantage
of this type is that it doesn't require the entire Postgis extension to be
added, and is perfectly capable of storing basic points. I tend to use this type
if I just need to store a latitude and longitude alongside other, more standard
relational data, and only move to Postgis if I need to do more complex queries
or datatypes. For users of the ActiveRecord library, `POINT` is natively
supported, deserializing to an `[x, y]` array. Keep in mind that X, Y order is
longitude, latitude, not the latitude, longitude order you might be accustomed
to.

---

* The capabilities of indexes
  * Partial indexes
  * Full-text indexes
  * jsonb indexes
* Know how to examine the schema
* Backing up
* Optimise configuration