# Setting Up and Using a Metadb Reporting Database to Provide Your Institution with Real-Time Data Reports from FOLIO

<!-- md2toc -l 2 intro-to-metadb--outline.md -->
* [Introduction](#introduction)
* [How Metadb works](#how-metadb-works)
* [How data gets from FOLIO into Metadb](#how-data-gets-from-folio-into-metadb)
* [How the Metadb data can be interrogated](#how-the-metadb-data-can-be-interrogated)


(30 minutes including Q&A)

Topics to _avoid_:
* Getting Started with Building Reports Using Metadb Software with FOLIO Data and Using Report Samples, Derived Tables, and Other Tools on the Folio-Analytics GitHub repository (Sharon and Scott)
* Using the Reporting App in FOLIO to Build and Run Real-Time Data reports right from the FOLIO Application Interface (Scott)
* Using Tableau to Deliver FOLIO Data Dashboards from Metadb (Suzette)
* Getting to Know the FOLIO Reporting Community (Scott)


## Introduction

Why not just run SQL queries against the FOLIO database?
* All the data is in JSON so queries would have to transform it
* The records are highly denormalized with data fragments split across tables.
* No record is kept of old data
* Performance implications of running expensive reports

What's needed is a separately provisioned database, with a structure optimised for report-type queries, containing old data as well as the present data, and that stays up to date as the FOLIO database changes.

That's Metadb.


## How Metadb works

FOLIO stores its data in a Postgres database.

When changes are made in this database by the day-to-day operation of the FOLIO ILS, change records are written to a replication slot.

Metadb harvests these change records to keep its own database up to date.

(Actually, it's more complicated than that -- stay tuned.)

Old versions of changed records are retained and accessible via historical tables that record all previous versions of each record with the start and end of their validity period.

Each record coming into Metadb has its JSON blog extracted and transformed into SQL-level fields.

Each record coming into Metadb goes through a further series of transformations that include denormalization to include data from related records.

For example, user records in Metadb contain not just a `userGroupId` but an expanded `userGroup` sub-record including the name of the group that the user belongs to.

MARC records, which are stored as opaque blobs in FOLIO, are expanded into queryable tables.


## How data gets from FOLIO into Metadb

See [the diagram](https://docs.google.com/presentation/d/1405stn-Vtjw2RA3nYQ9C8bEFytmv6Le6gRhTNFwEBI4/edit#slide=id.p)

It would be possible for Metadb to read directly from the replication slot of the FOLIO system's Postgres database.

That would not be ideal:
* Change events could back up in the Postgres replication slot
* State could get confused if FOLIO or Metadb goes down
* We would be limited to harvesting from Postgres, and Metadb is more general than that -- e.g. could harvest from a VuFind database

Instead we use a more flexible but complex system:
* Debezium uses the Postgres connector from Kafka Connect to read from the FOLIO database's replication slot
* It feeds those events into Kafka
* Metadb reads events from Kafka

This provides buffering, robustness and database-independence.


## How the Metadb data can be interrogated

The Metadb database can be queried using SQL:
* On the command-line with `psql`
* Using a GUI like DBeaver
* Programatically by client software

It is also available to use directly from FOLIO using the Reporting app.


