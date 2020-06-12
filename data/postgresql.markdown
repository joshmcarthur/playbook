---
layout: default
title: PostgreSQL
parent: "Databases & data"
nav_order: 20
---

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

### Indexing

In a similar way to views, indexes are supported by all the major relational
database engines. Indexes are a really powerful tool to both understand the
shape of data, and how it is accessed and used. I've got a pretty good general
outline to how I approach indexes across database engines in [the main data
section]({{ '/data/#indexes' | relative_url }}). This section specifically
covers indexing features that I believe are unique to Postgresql that I frequently use.

#### Partial indexes

The ability to index a subset of rows in a table is super useful. I use this
most often to add a unique index on user email where the user is not archived,
but it can be useful for all sorts of things - typically where there's a natural
condition that should apply to a central index. In ActiveRecord, this usually
means a default scope or frequently applied scope that would normally exclude
the rows that do not need to be indexed. Keeping indexes small is a key part of
letting Postgres work efficiently.

To create a partial index, a condition can just be passed to the `CREATE INDEX`
statement as a `WHERE` clause. 

#### Ordered indexes

The index creation statement can also specify an order, so long as the index is
using the (default) btree (binary tree) index implementation. Specifying an
order with an index can speed up the specific case where rows are consistently
fetched with a particular order that is not ascending order with nulls last (as
this is the default index order).

A particular case where an ordered index can provide a speed boost is when using
an order in conjunction with `LIMIT` to fetch the first _n_ rows. In this case,
Postgres would normally need to scan the entire result to order the results
before taking the first _n_ rows - however, with an ordered index, the index can
be used directly, with no sequential scan required of the underlying relation.

> `NULLS FIRST` and `NULLS LAST` is a Postgres-specific sorting syntax. Other
> database engines support this functionality, but with a different syntax. It
> is used to indicate whether rows with a null value should be shown _before_ or
> _after_ the ordered results. For example, this can be used to show all posts
> by their category in ascending order - `NULLS FIRST` would show all the posts
> that do not have a category first, while `NULLS LAST` would them last.

#### JSONB indexes

JSONB indexes support indexing rows by a value at a given JSONB path expression.
This is advantageous for tables that store JSON with a known structure, where it
is anticipated that a query will need to quickly look up rows based on the key. 

I don't use JSONB indexes terribly often, but wanted to mention them here since
they are a type of index that can only be used with JSONB columns, not hstore or
JSON columns. Speaking very generally, if a JSONB index is looking like it's
needed, I'd be looking at that data structure to see if it's appropriate to be
storing data that is that structured in a schemaless column, since it would be
difficult to enforce consistency and constraints upon the column data to ensure
the index would actually be used.

#### Full-text indexes

It's worth pointing out that the Postgres docs have [a full section on text
searching](https://www.postgresql.org/docs/current/textsearch.html), so I'm
going to avoid going into deep detail here.

The main thing I keep in mind is that full text searching _is_ something that
Postgres supports. It doesn't support the same depth of features that dedicated
full-text search platform (e.g. Elasticsearch, Solr) does, but so far in my time
using Postgres full-text indexes, I've never run into a complete dead end.
Additionally, the reduced maintenance burden from not introducing another system
to keep up and running, _especially_ one that is also a datastore cannot be
ignored.

Essentially, full-text indexes can be set up on tables using a GIN index with
the `to_tsvector` function - as the Postgres docs specify:

`CREATE INDEX pgweb_idx ON pgweb USING GIN (to_tsvector('english', title || ' ' || body));`


> Postgres' full-text functions like `to_tsvector` are just regular functions.
> They can be used in any particular part of a query or other statement, however
> when full-text searching, adding a dedicated index is quite important.

There are all sorts of handy operators and functions available in Postgres,
including tricks like using [generated
columns](https://www.postgresql.org/docs/current/ddl-generated-columns.html) to
collate full text searchable content. One function worth keeping in mind here is
`websearch_to_tsquery`, which will take a search with Google-type operators, and
transform it into a full text search (allowing text operators like "AND" "OR"
"&&", etc.).

Generally, I've found that there is sometimes a call for a full-text search
engine like Elasticsearch, but not as often as people think. Pushing Postgres as
far as it can go makes sense - a relational database is needed in any case, and
while you can keep fulltext search in PG, the existing tooling for scaling,
backups, monitoring and performance optimisation can be leveraged. 

Full-text search functionality is part of Postgres code - it is not an extension
like Postgis. This means that full-text search functions can be used within any
recent Postgres database.

### Know how to examine the schema

This is mostly just about how to jump into `psql` and check out a database. There are plenty of visual tools for this which can be used as well. I tend to just stick to `psql` - it's consistent and can be used anywhere, including on a server (without needing to drill an SSH tunnel for a UI tool to connect through).

The commands I use all the time are:

* `\dt` - Lists all of the tables in a database
* `\d+` - Shows the structure of a table, including indexes and foreign keys
* `\dm` - Shows materialized views
* `\o [filename]` - Outputs query results from this point on to `[filename]`,

There are heaps of other utilities here, just run `\?` to get a list of them.

The point of these commands is to be able to use `psql` to see details of the
schema. This is particularly useful with a database that I'm not familiar with,
since `\dt` and `\d+` lets me figure out the main tables, their structure, and
the relationships that exist between tables.

### Backing up

Postgres will normally be the canonical source of the application's data. Not
something that should be lost! For databases that can afford some table or
row-level locking and need a simple solution, `pg_dump` can't be beat. I use
`-Fc` (custom format). This is a format that is specific to Postgres, but backs
up quickly to a single compressed file that is restored with `pg_restore`.
Because it's a custom format, it has a few more options when restoring, such as
the ability to skip particular parts of the import. I've also experimented with
setting the `--jobs` option, but haven't seen any appreciable speed increse with
the size databases I tend to be backing up.

For larger databases, replication is usually the way to go. Postgres supports
replication with WAL (write-ahead-logging), that is pretty good and relatively
easy to set up - although this is the sort of point where I'll tend to defer to
the DB hosting platform. On RDS this means a read replica, on Heroku a DB
follower. Replication is definitely able to be set up, but it's another
important piece of infrastructure that I'd rather not manage myself.

### Optimise configuration

Postgres is incredibly customisable, and it seems to be a surprisingly neglected
part of performance tuning. Since Postgres is (a) quite an old open-source
project, and (b) agnostic about _where_ it gets deployed, the default
configuration for Postgres tends to be quite conservative. 

If Postgres is being pushed _at all_, performance configurations should almost
always be adjusted - it's highly likely otherwise that it won't matter how much
memory or CPU the hardware provides, Postgres won't use it. 

The most useful setting to change initially is `maintenance_work_mem`. This
setting can be expected to speed up indexing, materialized view refreshes, and
vacuuming. This setting allows Postgres to use more memory when performing
maintenance tasks. More memory means that more data can be loaded at once before
PG needs to go back and load more data off the (slow) disk.

Next, `work_mem` can be tuned to make the most of memory available on the
hardware. `work_mem` functions similarly to `maintenance_work_mem`, but relates
to primary query execution, rather than maintenance tasks. Work memory can be
expected to help performance of queries that normally need to load large amounts
of data to perform a query, such as a non-indexed order operation on a large
table (as this will require a sequential scan of the entire table to sort the
rows). 

Autovacuuming is also something that can normally be dialled back, or even
disabled and run manually if the database is only ever written to in batches
(for example, if data is imported into the database in a mass load and then only
ever read). Autovacuuming runs by default based on the
`autovacuum_vacuum_threshold` and `autovacuum_vacuum_scale_factor` settings,
which are in turn based on the volume of writes to the database. Dialling back
autovacuuming basically trades off table consistency for additional performance
that can go towards fulfilling queries.  

Regardless of the configuration of autovacuuming, running a manual vacuum with
`VACUUM ANALYZE` every now and then tends to clean out cruft, rebuild indexes,
and lead to better performance. This does need to be done sometime when the
database isn't under load though, so isn't always practical - but it's often a
good quick fix!

---

This section has been long. Thanks for sticking with it. It is long because of
the sheer volume of useful stuff contained in the PostgreSQL database. Learning
even a fraction of the functionality is well worth it - this functionality can
be used anywhere where Postgres is supported, and can often displace the need to
build infrastructure or equivalent functions into the application codebase
entirely.