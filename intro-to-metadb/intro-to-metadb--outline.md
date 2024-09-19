# Setting Up and Using a Metadb Reporting Database to Provide Your Institution with Real-Time Data Reports from FOLIO

(30 minutes including Q&A)

Topics to _avoid_:
* Getting Started with Building Reports Using Metadb Software with FOLIO Data and Using Report Samples, Derived Tables, and Other Tools on the Folio-Analytics GitHub repository (Sharon and Scott)
* Using the Reporting App in FOLIO to Build and Run Real-Time Data reports right from the FOLIO Application Interface (Scott)
* Using Tableau to Deliver FOLIO Data Dashboards from Metadb (Suzette)
* Getting to Know the FOLIO Reporting Community (Scott)


## Introduction

Why not just run SQL queries against the FOLIO database?
* It's not in a good structure for reporting: highly denormalized
* No record of old data
* Performance implications of running expensive reports

What's needed is a separately provisioned database, with a structure optimised for report-type queries, containing old data as well as the present data.

That's Metadb.


## How it works

FOLIO stores its data in a Postgres database.

When changes are made in this database by the day-to-day operation of the FOLIO ILS, change records are written to a replication slot.

Metadb harvests these change records to keep its own database up to date.

(Actually, it's more complicated than that -- stay tuned.)

Old versions of changed records are retained and accessible via historical tables that record all previous versions of each record with the start and end of their validity period.

Each record coming into Metadb goes through a series of transformations that include denormalization to include data from related records.

For example, user records in Metadb contain not just a `userGroupId` but an expanded `userGroup` sub-record including the name of the group that the user belongs to.

MARC records, which are stored as opaque blobs in FOLIO, are expanded into queryable tables.

The Metadb database can be queried using SQL (on the command-line, using a GUI like DBeaver, or programatically).

It is also available to use directly from FOLIO using the Reporting app.

