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

IRIS containers are available for both Intel x86 and ARM 64 systems, but not for 32-bit operating systems running on ARM processors.


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

Intel i7 (8th generation) NUC, with Samsung NVME SD and 16Gb RAM, running Windows 10 Professional.

A Unbuntu Linux Virtual Machine, running on HyperV on this machine was set up to host the Docker Containers.


## Basic Global Set Performance Test


Summary results (per 100,000 Global Sets):

- IRIS: 0.22 sec
- YottaDB: 0.25 sec
- BDB: 1.4 sec
- LMDB: 180 sec
- Redis: 40.2 sec


## Basic Global Get Performance Test (per 100,000 Global Gets)

- IRIS: 0.16 sec
- YottaDB: 0.17 sec
- BDB: 1.4 sec
- LMDB: 1.25 sec
- Redis: 10.34 sec


## XML DOM Parsing Performance

**The following text is just initial notes by me...to be tidied up in final document**

For this I run a Node.js script which instantiates an mg-dbx connection to YottaDB (via its API interface), and then "ingests" a 92kb XML document (actually a FHIR document) and parses it and stores it in YottaDB as a persistent DOM.

This XML processing creates 3,471 DOM nodes, involving a total of 287,604 mg-dbx API calls from Node.js to YottaDB as follows:

- set: 29,416
- get: 103,169
- next calls: 2,269
- kills: 1
- data calls: 149,278
- increment calls: 3471




