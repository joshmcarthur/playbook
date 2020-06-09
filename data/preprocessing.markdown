---
layout: default
title: (Pre)Processing data
parent: "Databases & data"
nav_order: 10
---

Work in progress
{: .label .label-blue }

Data comes from a range of sources. I have found that the preprocessing of data
can be an enormously beneficial step to getting a project off the ground well.
When the alternative is manual entry, any kind of efficiency gain that can be
introduced pays off in freeing up resources that can be spent on more helpful
things than typing into text fields. 

Data is all over the place. The first thing to do is to try and collect raw data
sources into a central location. If the preprocessing step is going to be run on
an ongoing basis, then a certain amount of tooling needs to go into managing
this process to make it maintainable and open for others to work on. This
generally means that data management should be represented in scripts - the
language doesn't matter so much as the ability to reproduce the results. I
usually use Bash for simple procedural stuff, and drop down to Ruby if I need to
do a more complex transformation. 

In terms of fetching the data from remote data sources, cURL is pretty
unbeatable. cURL is an HTTP client that can fetch pretty much anything from
anywhere. Wget can be used for a subset of what cURL can be used for, and has
the advantage of being preinstalled on most Linux distributions - in my
experience though, I've never really gotten wget to work all the way through a
more complex data fetch, and normally end up switching to cURl.

I'm trying to keep technical detail out of this content, but cURL does have some
useful options. It supports shell pipes and redirections, so an HTTP response
can be piped into another command (such as `jq`). It can perform POST requests
to simulate a form submission or API request, or OPTIONS requests to check a
<abbr title="Cross-Origin Resource Sharing">CORS</abbr> request. Most visual
HTTP clients, such as [Insomnia](https://insomnia.rest/) and
[Postman](https://www.postman.com/) support importing and exporting requests in
cURL format. cURL can also have it's verbosity dialled up (using `-vvv`), which
will also display the request and response headers, DNS resolution and SSL
negotiation details. When I am fetching data from a remote source, I do try and
keep in mind the licensing and usage rights of this data. This means that I may
push a script to _fetch_ the data to a git repository for others to consume, but
not the data itself.

With data fetched (or, if the data is already available via a local file), it
needs to be transformed into a format that will be easy loaded into a database.
The amount of transformation thaat data requires depends entirely on the source.
Data is often available in a format that is already close to a table-like
structure - for example, XLS, XLSX, CSV and other row/column formats. Sometimes,
data has already been designed into an object-like structure like JSON or YAML.
Finally, data can be represented in a format that is designed for human, rather
than machine interpretation - for example, PDF, DOC, DOCX or raw text that is
not specially formatted. 

For CSV and other *SV files, minimal transformation is usually needed. The main
things to be aware of is that the file may not be UTF-8 encoded (for example,
files from Excel are frequently encoded as WINDOWS-1252). Libraries like iconv
are useful for handling encoding transformations. After that PostreSQL and
SQLite support importing from a CSV file with or without headers, with a
different separator (e.g. `<tab>` instead of comma), and a bunch of other
configuration. If the CSV file headers differ from the table structure I want,
or if I want to build up a single table from multiple CSV files, that's no
problem - I just import into a temporary table, and then `INSERT` using a
`SELECT` statement to select the rows to insert from the temporary table(s),
joining or filtering as necessary.

  * Postgres support copying from the Postgres client, `psql` using
    [`\copy`](https://www.postgresql.org/docs/12/app-psql.html#APP-PSQL-META-COMMANDS-COPY),
    and via the [SQL `COPY`
    statement](https://www.postgresql.org/docs/12/sql-copy.html), with fairly
    minor differences between the two (I usually use `\copy`).
  * SQLite supports importing from the `sqlite3` client using
    [`.import`](https://www.sqlite.org/cli.html#csv_import). SQLite also
    supports a SQL `COPY` statement with slightly different options from
    `.import` and Postgres' `COPY` statement.

For JSON and XML data, there are additional utilities that can be used to
extract meaningful data from the file(s). For JSON, the `jq` utility is a very
powerful tool for extracting particular JSON keys from an object, array, or
array of objects. It also supports some primitive enumeration and
transformations, such as arithmetic operators, `map`, `select` and `if`/`else`.
`jq` has an online tester at https://jqplay.org/ that is handy for figuring out
what string to pass to `jq` to get the desired result. `jq` can send it's output
straight to a file, or it can be piped to another command (e.g. `grep` or
`head`). It can also accept input from STDIN, which means that the output of
another command can be streamed _to_ `jq` - such as the output of a cURL
command.

For XML, there are...less modern and cutting edge solutions. Fortunately, XML
already has an established query language in `xpath`. The most minimal tool I
have found for extracting data from an XML file is `xmllint`. This utility
accepts an XPath expression to apply to the file, and will output matching
elements to STDOUT, where it can be piped to another command or output to a
file. I've written a [blog
post](https://www.joshmcarthur.com/til/2018/06/19/extracting-xml-data-with-curl-and-xmllint.html)
that is probably the most succint example I have of extracting data from XML.

* head, tail and grep to examine text data
* awk and print to transform data 