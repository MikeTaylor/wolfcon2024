# A case-study in modularity -- outline

<!-- md2toc -l 2 modularity-outline.md -->
* [Philosophy](#philosophy)
* [Modularity in FOLIO](#modularity-in-folio)
* [Benefits of the modular architecture](#benefits-of-the-modular-architecture)
* [An ecosystem of modules](#an-ecosystem-of-modules)
* [Some light technical details](#some-light-technical-details)
* [A case-study: mod-reporting](#a-case-study-mod-reporting)
* [Implications](#implications)


Here is the [published abstract](https://wolfcon2024.sched.com/event/1eesf/a-case-study-in-folio-modularity-replacing-mod-ldp-with-mod-reporting)


## Philosophy

> "I'm afraid I'm a practical man", said the doctor with gruff humour, "and I don't bother much about [...] philosophy."\
> "You'll never be a practical man till you do", said Father Brown.\
> -- G. K. Chesterton, _The Dagger with Wings_.

The design of FOLIO arose from a philosophy

* Communities **do things better** than top-down organizations.

* Communities **do better things** than top-down organizations.

* FOLIO belongs to the whole library community, not to a vendor.

* Any member of the community should be able to contribute.

There is no magic access for insiders: we eat our own dogfood.


## Modularity in FOLIO

That philosophy is reflected in the design of FOLIO: from the start, it was constructed to be modular.

The heart of every FOLIO system is a well-defined platform consisting of:

* Okapi, the API gateway that manages modules

* Well-defined specifications for what a module is (discussed below)

* A handful of core modules to handle authentication and related concerns

This provides a context in which any module from any source can be deployed.

(The UI is also modular, but we're not much concerned with that here.)


## Benefits of the modular architecture

This vision of modularity is crucial to FOLIO’s appeal to the library community, because it lowers the bar to participation:

* New functionality can be added as a module without requiring changes elsewhere in the system

* Individual libraries may create modules that meet their needs

  * Or hire developers to do so

  * Or contribute to crowdfunding modules that will be of use to a broader community

* Different libraries can deploy different sets of modules, lowering operating costs.

None of this needs “permission” from a central authority.


## An ecosystem of modules

For example, a FOLIO library might need a module for booking rooms.

They could create that module themselves, and make it available for other FOLIO libraries to use. 

This is exactly what happened with Course Reserves, which was developed by Index Data with funding from FLO, the Fenway Library Organization in Boston.

It's now used by more or less all US Academic libraries on FOLIO.

FOLIO was started in 2015, and the first modules (mod-users and mod-inventory) came into existence in 2016.

In the eight years since, it has grown to 114 back-end modules (as of the current snapshot build).

> SIDEBAR
>
> How are modules made available to the community in trusted form?
>
> This is more of a social problem than technical one. We can talk more about it in the Q&A if there is interest.


## Some light technical details

A FOLIO module is a computer program.

It can be written in any language (though most are in Java).

It must fulfil certain requirements:

* It must listen on a port and respond to WSAPI requests.

* Its WSAPIs are typically RESTful and use JSON payloads.

* It must provide a machine-readable description of the requests it accepts and the responses it can give. (This description is posted to Okapi to add a module to a running FOLIO system.)

* Its description can define permissions and specify which of them are required to access which WSAPI endpoints.

* Its description must specify what _interfaces_ the module requires and what interfaces it provides.

* All communication between modules is via these interfaces: _there is no shared database_.

All dependencies are on interfaces, not modules (which are implementations).

FOLIO enforces a partitioning of concerns: integration between modules is by contract-defined API-based communication.

Multiple modules can provide alternative implementations of the same interface.

Consumers of that interface neither know nor care what module implements it.


## A case-study: mod-reporting

FOLIO’s Reporting app allows users to search in Metadb, relational database that contains normalized records that are continually harvested from FOLIO as they change.

Apart from the UI component, the app is implemented by a back-end module (mod-ldp) which communicates on the UI behalf with Metadb.

mod-ldp is old code, written by a third party using tools alien to its maintainers, insecure in places, and badly in need of updates.

Rather than fix it, we decided to replace mod-ldp with a new module, mod-reporting, written in Go.

The new module provides the same version of the same interface as the old.

That means the new module is plug-compatible with the old.

It has been deployed in place of its predecessor with no problems whatsoever.

The existing UI module has not been changed to accommodate it, and continues to work exactly as before.

In fact, it's been running on Snapshot for a week and none of you noticed :-)

(What difficulties we experienced were in CI and CD.)


## Implications

Our experience provides an example of how such code upgrades can be performed.

It provides a pathway for piecewise refreshment of ageing parts of FOLIO.

A similar technical approach can also enable the substitution of different implementations of an API.

For example:

* A plug-compatible mod-users alternative that uses LDAP as the source of truth

* A plug-compatible mod-fqm-manager replacement that runs queries against MetaDB

Crucially, _anyone_ can do this.

Modularity is empowering!


