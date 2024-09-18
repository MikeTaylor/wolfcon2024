# A case-study in modularity -- outline

<!-- md2toc -l 2 modularity-outline.md -->
* [Philosophy](#philosophy)
* [What follows from philosophy](#what-follows-from-philosophy)
* [A case-study in reporting](#a-case-study-in-reporting)


[Published abstract](https://wolfcon2024.sched.com/event/1eesf/a-case-study-in-folio-modularity-replacing-mod-ldp-with-mod-reporting)


## Philosophy

> 'I'm afraid I'm a practical man,' said the doctor with gruff humour, 'and I don't bother much about [...] philosophy.'\
> 'You'll never be a practical man till you do,' said Father Brown.\
> -- G. K. Chesterton, _The Dagger with Wings_.

From the earliest design of the FOLIO platform, it was constructed to be modular.

The heart of every FOLIO system is a well-defined platform consisting of Okapi and some core modules to handle authentication and related concerns.

This provides a surface onto which any module can be deployed — depending on some interfaces and providing others.


## What follows from philosophy

This approach provides multiple concrete benefits.

First, it enforces a partitioning of concerns, with contract-defined API-based communication between modules.

Second, it allows new functionality to be added as a module without requiring changes elsewhere in the system.

Third, it allows multiple modules to provide alternative implementations of the same interface, with consumers of that interface neither knowing nor caring what module implements it.


## A case-study in reporting

FOLIO’s Reporting app consists of a UI module (ui-ldp) and a back-end module (mod-ldp) which communicates on the UI module’s behalf with the MetaDB datastore.

mod-ldp is old code, written by a third party using tools alien to its maintainers, insecure in places, and badly in need of updates.

We therefore decided to re-implement mod-ldp’s interface by a new module written in Go (mod-reporting).

The new module is plug-compatible with the old, providing the same version of the same interface,

and has been deployed in place of its predecessor with no problems whatsoever.

The existing UI module has not been changed to accommodate it, and continues to work exactly as before.

XXX Difficulties were in CI and CD.


## Implications

Our experience provides an example of how such code upgrades can be performed,

and provides a pathway for piecewise refreshment of parts of FOLIO as they age out.

A similar technical approach can also enable the substitution of different implementations of an API, for example implementing a plug-compatible mod-fqm-manager replacement that runs queries against MetaDB.


