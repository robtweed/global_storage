# Performance Comparisons of Global Storage Databases

## Purpose

In this document, we'll look at the relative performance of the following implementations of Global Storage:

- IRIS
- YottaDB
- [*mg-dbx-bdb*](https://github.com/chrisemunt/mg-dbx-bdb) abstraction of Berkeley DB
- [*mg-dbx-bdb*](https://github.com/chrisemunt/mg-dbx-bdb) abstraction of LMDB
- [*ewd-redis-globals*](https://github.com/robtweed/ewd-redis-globals) abstraction of Redis


## Docker Containers for each Implementation

In order to create a "level playing field" for proper cross-comparison, and to allow others to try our tests out for themselves and compare our results, we've created Docker Containers for each, containing a pre-installed, pre-configured instance of the specific database and the QEWD and QEWD-JSdb environments.

The source Dockerfiles for these and other Containers we've created are in 
[this Github Repository](https://github.com/robtweed/dockerfiles).  Pre-built copies can be pulled from Docker Hub.  Specifically:

- YottaDB
  - for Linux on Intel x86:
    - [Source](https://github.com/robtweed/dockerfiles/tree/master/qewd-server)
    - or *docker pull rtweed/qewd-server*
  - for Linux on ARM 64:
    - [source](https://github.com/robtweed/dockerfiles/tree/master/qewd-server-arm64)
    - or *docker pull rtweed/qewd-server-arm64*
  - for 32-bit Raspberry Pi Raspian:
    - [source](https://github.com/robtweed/dockerfiles/tree/master/qewd-server-rpi)
    - or *docker pull rtweed/qewd-server-rpi*

- Global Storage using BDB:
  - for Linux on Intel x86:
    - [Source](https://github.com/robtweed/dockerfiles/tree/master/qewd-bdb)
    - or *docker pull rtweed/qewd-bdb*
  - for Linux on ARM 64:
    - [source](https://github.com/robtweed/dockerfiles/tree/master/qewd-bdb-arm64)
    - or *docker pull rtweed/qewd-bdb-arm64*
  - for 32-bit Raspberry Pi Raspian:
    - [source](https://github.com/robtweed/dockerfiles/tree/master/qewd-bdb-rpi)
    - or *docker pull rtweed/qewd-bdb-rpi*

- Global Storage using LMDB:
  - for Linux on Intel x86:
    - [Source](https://github.com/robtweed/dockerfiles/tree/master/qewd-lmdb)
    - or *docker pull rtweed/qewd-lmdb*
  - for Linux on ARM 64:
    - [source](https://github.com/robtweed/dockerfiles/tree/master/qewd-lmdb-arm64)
    - or *docker pull rtweed/qewd-lmdb-arm64*
  - for 32-bit Raspberry Pi Raspian:
    - [source](https://github.com/robtweed/dockerfiles/tree/master/qewd-lmdb-rpi)
    - or *docker pull rtweed/qewd-lmdb-rpi*

- Global Storage using Redis:
  - for Linux on Intel x86:
    - [Source](https://github.com/robtweed/dockerfiles/tree/master/qewd-redis)
    - or *docker pull rtweed/qewd-redis*
  - for Linux on ARM 64:
    - [source](https://github.com/robtweed/dockerfiles/tree/master/qewd-redis-arm64)
    - or *docker pull rtweed/qewd-redis-arm64*
  - for 32-bit Raspberry Pi Raspian:
    - [source](https://github.com/robtweed/dockerfiles/tree/master/qewd-redis-rpi)
    - or *docker pull rtweed/qewd-redis-rpi*

Note that the ARM 64 versions should be used on:

- Apple MacOS-based machines using the M1 processor
- Raspberry Pi running 640-bit Ubuntu Linux


IRIS has to be handled a bit differently, as it is a proprietary product and we have to use the packaging made available by InterSystems.  However, they do make a Dockerised, so-called 
[*community edition*](https://hub.docker.com/_/intersystems-iris-data-platform/plans/222f869e-567c-4928-b572-eb6a29706fbd?tab=instructions)
 available.  This provides all the core functionality needed for the QEWD-JSdb abstraction, but does not include Node.js or the Node-js-based QEWD or QEWD-JSdb modules.  We've therefore 
[provided instructions](https://github.com/robtweed/qewd-jsdb-kit-iris/blob/master/INSTALL.md) 
on how to modify the InterSystems Community Edition Container to add these missing components.  The resulting Docker Container is then directly and fairly comparable with our other containers.

IRIS Containers are available for both Intel x86 and ARM 64 systems, but not for 32-bit operating systems running on ARM processors.

Note that unlike the other Containers that are based on Open Source databases, the IRIS Containers are severely restricted in terms of available user licenses.  They are usable for the tests described here, and for running the QEWD environment with a recommended maximum PoolSize of 2.


## Kits for Use with the Containers

Each of the Containers above requires a mapped volume to be set up on the Host machine.  This mapped volume does two things:

- it provides the configuration settings needed by the QEWD environment
- it is where all the test scripts and supporting files expect to be found within the running Container.

Once again, we've provided pre-built kits which create the appropriate mapped volume for you.  The instructions for setting them up and running the relevant Docker Container is in the respective repository's *README.md* file:

- [IRIS](https://github.com/robtweed/qewd-jsdb-kit-iris)
- [YottaDB](https://github.com/robtweed/qewd-jsdb)
- [BDB](https://github.com/robtweed/qewd-jsdb-kit-bdb)
- [LMDB](https://github.com/robtweed/qewd-jsdb-kit-lmdb)
- [Redis](https://github.com/robtweed/qewd-jsdb-kit-redis)


## Test Platform used for the Results Reported in this Document

The performance tests reported in this document were conducted on an Intel i7 (8th generation) NUC, with Samsung NVME SD and 16Gb RAM, running Windows 10 Professional.  A Unbuntu Linux Virtual Machine, running via HyperV on this machine was used to host the Docker Containers.


## Database Implementations and Configuration

In each case, a standard default database installation and configuration was used.  No performance tuning was applied to any of the databases.  It is likely that some improvements for each database could be achieved with appropriate re-configuration and tuning of available parameters, but we wanted to run as fair and comparable test as possible, hence the use of standard default installations.

To see how each database was installed and configured, study the appropriate Dockerfile source code (see earlier section for details).


## Node.js-based Test Interfaces

All implementations were tested via the available Node.js APIs for that implementation, ie:

- IRIS & YottaDB: [mg-dbx](https://github.com/chrisemunt/mg-dbx)
- Berkeley DB and LMDB: [mg-dbx-dbd](https://github.com/chrisemunt/mg-dbx-bdb)
- Redis: [ewd-qoper8-redis](https://github.com/robtweed/ewd-qoper8-redis)

At the Node.js level, these interfaces are all directly comparable.  However, a key difference is that the Redis interface works over a network connection (albeit within the same machine).  The other interfaces directly access the low-level API interfaces provided by the target databases.


## Basic Global Set (Write) Performance Test

### Background

The first test is a very simple one, setting a sequence of basic Global key/value pairs:

        Set cm[n] = some text

where n is an incremented integer value from 1 to a pre-set maximum.

This provides a basic idea of how fast each implementation can create and store key/value pairs as Global Nodes.

- For YottaDB and IRIS, this test directly creates the Global Nodes.  This is done via Node.js as follows:

  - having created an instance of the interface component and opened a connection to the database, perform a simple loop:

        var max = 1000000;
        var global = new mglobal(db, 'cm');
        for (var n = 1; n <= max; n++) {
          global.set(n, "Chris Munt");
        }

- For Berkeley DB (BDB), the equivalent logic is applied to create the Global Nodes, but it is worth considering what this test does in terms of the underlying BDB database, to see that nothing particularly unusual is going on:

  - the BDB database environment is first created and opened:

        db_env_create(&penvironment, 0);
        penvironment->open(penvironment, env_dir, DB_CREATE | DB_INIT_CDB| DB_INIT_MPOOL, 0);
        db_create(&pdb, penvironment, 0);
        pdb->open(pdb, NULL, db_file, NULL, DB_BTREE, DB_CREATE, 0);

  - to set each key/value pair record (eg the first one):

        key = '1';  // value incremented within the loop
        data = 'Chris Munt';
        pdb->put(pdb, NULL, key, data, 0);

  - to finally close the database:

        pdb->close(pdb, 0);


- For LMDB, again nothing unusual or complex is going on.  Note that each Global Set is performed within a transaction:

  - the LMDB database environment is first created and opened:

        mdb_env_create(&penvironment);
        mdb_env_open(penvironment, env_dir, MDB_NOTLS, 0664);
        mdb_txn_begin(penvironment, NULL, 0, &ptransaction);
        mdb_dbi_open(ptransaction, NULL, 0, pdb);
        mdb_txn_commit(ptransaction);

  - to set each key/value pair record (eg the first one): 

        key = '1';  // value incremented within the loop
        data = 'Chris Munt';
        mdb_txn_begin(penvironment, NULL, 0, ptransaction);
        mdb_put(ptransaction, db, key, data, 0);
        mdb_txn_commit(ptransaction);

  - to finally close the database:

        mdb_dbi_close(penvironment, db);
        mdb_env_close(penvironment);


- For Redis, this test involves little more than creating standard Redis simple key-value pairs.

- a network connection to Redis is first established in the normal way

- when the first Global Node is created in the test, the following keys are created:

        1) "node:cm"
        2) "leaves:cm"
        3) "node:cm\x011"
        4) "children:cm"

  Keys 1, 3 and 4 are created to provide the emulation of the Global's hierarchical structure and first level of subscripting.

- subsequent Global Nodes in this test just involve adding new *node:cm* keys, eg when the second iteration of the test loop occurs, the Redis keys are:

        1) "leaves:cm"
        2) "children:cm"
        3) "node:cm\x012"
        4) "node:cm\x011"
        5) "node:cm"

  So, once again, for Redis, this test is doing nothing particularly out of the ordinary or unexpected.


### Results

(Draft to be confirmed) Summary results (per 100,000 Global Sets):

- IRIS: 0.22 sec
- YottaDB: 0.25 sec
- BDB: 1.4 sec
- LMDB: 180 sec
- Redis: 40.2 sec


## Basic Global Get (Read) Performance Test

### Background

The second basic test measures the speed at which the records created in the first (Set/Write) test can be retrieved, ie:

        value = Get cm[n]

where n is an incremented integer value from 1 to a pre-set maximum.

Given the details shown in the previous section on how these simple key/value pairs are actually created within the underlying databases, it should be self-evident that retrieving the key/value pairs is exercising the very basic functionality of each database, and doing nothing unusual in each case.  

Basically the only key difference in the Get/Read test for each database is the use of a *get* API instead of a *set/put* API.


### Results

(Preliminary draft results, to be confirmed)  Time to read/get 100,000 Global Nodes:

- IRIS: 0.16 sec
- YottaDB: 0.17 sec
- BDB: 1.4 sec
- LMDB: 1.25 sec
- Redis: 10.34 sec


## XML DOM Parsing Performance

In the real world, a mixture of read and write APIs would be used, and the Globals created and manipulated would be multi-levelled and accessed in effectively a random way rather than sequentially as in the first two tests.

To simulate a more real-world scenario, we made use of the QEWD-JSdb XML DOM model, and applied its *parseFile* API to ingest a large, complex and deeply-nested XML document.

You can see the test XML Document in each of the kit repositories.  Look for the file named *benchmark.xml*.  This file is 90Kb in size, and is actually an example of a healthcare CDA document.

The QEWD-JSdb *parseFile* API parses this document and converts it into a set of XML DOM Nodes, persisted in a Global named *exampleDOM*.

This XML processing creates 3,471 DOM nodes, involving a total of 287,604 Global Storage API calls as follows:

- Set: 29,416
- Get: 103,169
- Next: 2,269
- Kill: 1
- Data: 149,278
- Increment: 3471

As the parser recurses its way through the XML DOM's hierarchical structure, it is invoking these APIs across and through all levels within the Global Storage hierarchy as it adds each node, indexes it and modifies the various pointers between Nodes (eg *parent*, *firstChild*, *lastChild*, *nextSibling*, *previousSibling*). It is therefore a pretty good repeatable simulation of typical real-world Global Storage usage.

The same XML Document was used for each database, and the lengths of time were recorded to:

- parse/create the DOM in Global Storage; and then 
- recurse through the DOM structure to re-create the XML document

In all cases the logic was identical, using the QEWD-JSdb XML DOM API abstractions.

### Results

To be provided, but preliminary results are:

- YottaDB:
  - parse: 3.4 sec
  - output back to XML: 0.8 sec

- IRIS:

  - parse: 4.1 sec
  - output back to XML: 0.8 sec

- Redis:

  - parse: 28.9 sec
  - output back to XML: 4 sec

- BDB:

  - parse: 7.1 sec
  - output back to XML: 1.9 sec

- LMDB:

  - parse: ?
  - output back to XML: ?


## Conclusions

It is pretty clear from these tests that, contrary to our expectations, the Native Global Storage databases (ie IRIS and YottaDB) significantly outperform the other databases.

I say "contrary to our expectations" because:

- BDB, LMDB and Redis are renowned as being probably the fastest databases known;
- the simple Set and Get tests were exercising these databases in pretty much the most natural way.  Nothing unusual was being done and we had expected them to be blisteringly fast.

As it turned out, in the Set and Get tests, the Native Global Storage databases appear to be capable of significantly outperforming the others as simple key/value stores. The fact that both IRIS and YottaDB are almost unknown in the mainstream IT community is therefore pretty surprising.

As the more "real world* XML DOM test also shows, these Native Global Storage databases similarly outperform the other databases by a wide margin.

We have little doubt that an expert in each of the databases could tune them for improved performance, however:

- the difference in performance is so significant between IRIS and YottaDB versus the others, that we doubt that difference could be made up, given the limited control possible in embedded databases;

- whatever difference could be made up could probably be equalled by an expert in tuning IRIS or YottaDB.

The overwhelming conclusion has to be that the Native Global Storage databases appear to be one of the "best kept secrets" in the IT world.  The fact is that they are blisteringly fast at anything including simple key/value storage and retrieval, and, as has been shown in this set of documents, they have a "Universal NoSQL" capability that makes them the most versatile databases available.

Whether of not you've ever heard of these databases before, our study suggests you really need to check them out as soon as possible!



