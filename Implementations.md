# Global Storage Database Implementations

## Native Global Storage Databases

In this category are databases that natively implement Global Storage, rather than implement a projection on top of some other underlying database technology.

There are three such Native Global Storage Databases that are worth considering:

- [**YottaDB**](https://yottadb.com) This is an Open Source Global Storage database with a long and excellent pedigree particularly in core banking systems and also, more recently, in healthcare for so-called Electronic Healthcare Records.  It is primarily designed to run on Intel-based Linux platforms, but at ARM port is also available for the Raspberry Pi.

- [**IRIS**](https://www.intersystems.com/products/intersystems-iris/)  This is a proprietary product, developed by InterSystems Corp.  Although marketed as a database platform with multi-model and analytics capabilities, at its heart it is a Global Storage database.  IRIS is widely used in both healthcare applications and financial services, but is present in many other sectors too.

- [**Cach&eacute;**](https://www.intersystems.com/products/cache/)  This is also a proprietary product from InterSystems.  It is effectively a deprecated product, however, with its current users tending to migrate to the newer IRIS data platform (see above).  The core underlying database engine used by Cach&eacute; is basically an earlier implementation of the same Global Storage engine used by IRIS.

These databases come with their own platform-specific language interfaces, allowing the Global Storage to be accessed from a range of commonly-used programming languages.  However, we provide a set of Open-Source language interfaces that provide a common approach and behave identically on all three of the Native Global Storage databases:

- [Python](https://github.com/chrisemunt/mg_python)
- [PHP](https://github.com/chrisemunt/mg_php)
- [Ruby](https://github.com/chrisemunt/mg_ruby)
- [Go](https://github.com/chrisemunt/mg_go)

We also provide a high-performance [JavaScript interface for use with Node.js](https://github.com/chrisemunt/mg-dbx).

The latter interface is used by our [QEWD-JSdb](https://github.com/robtweed/qewd-jsdb) *databaseless* [Persistent JavaScript Objects](./QEWD-JSdb.md#qewd-jsdb-on-disk-or-in-memory-javascript-objects) abstraction of Global Storage, and is a key dependency of our [QEWD](https://github.com/robtweed/qewd) technology.


## Global Storage Abstractions on other Databases

It is possible to implement a Global Storage database engine as an abstraction on top of certain other database technologies.  The most suitable candidate databases for such an abstraction are ones that are essentially low-level hierarchical databases.  The two best known and most widely used are so-called *embedded databases*:

- [**Oracle Berkeley DB**](https://www.oracle.com/uk/database/technologies/related/berkeleydb.html)
- [**Lightning Memory-Mapped Database (LMDB)**](https://symas.com/lmdb/)

Both databases are probably best-known for their use to implement the [Lightweight Directory Access Protoco (LDAP)](https://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol), where LMDB has largely supplanted Berkeley DB as a result of LMDB's reported better performance.


Perhaps somewhat surprisingly, it has also proven possible to implement Global Storage on the 
[Redis](https://redis.io) database.  Whilst this is primarily best known as a high-performance key/value database, it has two particular features that made Global Storage abstraction possible:

- sorted sets (using the Z- APIs), which make it possible to implement the automatic sorting behaviour of Global subscripts
- distributed locks, allowing the implementation of the Global Storage Lock and Unlock APIs.

We have implemented these three Open Source Global Storage abstractions using the JavaScript language, for use as server-side Node.js Modules:

- [Berkeley DB and LMDB](https://github.com/chrisemunt/mg-dbx-bdb)
- [Redis](https://github.com/robtweed/ewd-redis-globals)

Our [QEWD-JSdb](https://github.com/robtweed/qewd-jsdb) *databaseless* [Persistent JavaScript Objects](./QEWD-JSdb.md#qewd-jsdb-on-disk-or-in-memory-javascript-objects) abstraction of Global Storage can integrate directly with these abstracted Global Storage Implementations.  [Read this document](QEWD-JSdb.md#trying-out-qewd-jsdb-with-the-various-global-storage-databases) to learn how to try them out.





