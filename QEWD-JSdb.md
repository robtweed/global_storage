# Introduction to QEWD-JSdb

## Preface

Before reading this document, it is recommended that you first familiarise yourself with the following documents that will help you understand the underpinnings of QEWD-JSdb:

- [What is a Global Storage Database?](./Global_Storage.md)
- [Universal NoSQL as a consequence of Global Storage](./Universal_NoSQL.md)


## Global Storage and JSON

There is a 
[natural mapping between the hierarchical Global Storage and JSON](./Global_Storage.md#global-storage-is-actually-just-like-json).  If you consider the various NoSQL database models, and also the Relational model that are [described elsewhere in terms of Global Storage](./Universal_NoSQL.md), all of those mappings could equally be described in terms of JSON.  Take, for example, Global Storage being used to maintain a [List](./Key_Value.md#lists) of two items:

        list["MyList", "firstNode"] = 1
        list["MyList", "lastNode"] = 2
        list["MyList", "node", 1, "value"] = "MyFirstValue"
        list["MyList", "node", 1, "nextNode"] = 2
        list["MyList", "node", 2, "previousNode"] = 1
        list["MyList", "node", 2, "value"] = "MySecondValue"
        list["myList", "counter"] = 2


We could equally express this in terms of JSON:

        {
          "MyList": {
            "firstNode": 1,
            "lastNode": 2,
            "node": {
              1: {
                "value": "MyFirstValue",
                "nextNode": 2
              },
              2: {
                "value": "MySecondValue",
                "previousNode": 1
              }
            },
            "counter": 2
          }
        }


## Universal NoSQL using JSON?

On this basis, it follows that, perhaps surprisingly, JSON provides a syntax for modelling any and all the NoSQL database types, and even the Relational Database model!  JSON, of course, is normally assumed to be an in-memory structure, but the ability to seamlessly map JSON to a corresponding Global Storage structure, means that Global Storage makes persistent, on-disk JSON a realistic possibility, and, not only that: as a consequence, on-disk JSON provides the basis for implementing
[Universal NoSQL](./Universal_NoSQL.md).


## QEWD-JSdb: On-Disk or In-Memory JavaScript Objects

What is described above is the set of concepts that underpin our [QEWD-JSdb](https://github.com/robtweed/qewd-jsdb) technology. 

As JSON originated in the JavaScript language, and as JavaScript is naturally designed around dynamic, schema-free Objects which can be naturally described in JSON terms, QEWD-JSdb is written in JavaScript, designed to be run in the server-side [Node.js](https://nodejs.org) environment.

But QEWD-JSdb actually takes things one step even further by blurring the distinction between in-memory and on-disk JSON by implementing what we term *persistent JavaScript Objects*: Objects whose contents transparently and seamlessly reside on-disk rather than in-memory.  

A key intention of QEWD-JSdb, then, is to remove the hard-line that traditionally separates a programming language from a database, and instead make the database something that is largely transparent to the developer.  The developer needs only decide: is the JSON data structure that I want to use and maintain temporary and ephemeral (so therefore appropriate as in-memory) or something I want to persist permanently or semi-permanently (so therefore appropriate for storage on-disk)?  I like to describe this concept as *databaseless* persistent data.  Yes, of course it actually requires a database - a Global Storage database - but it doesn't seem like it to the developer when accessing it.


## Getting Started with QEWD-JSdb

You might want to begin by watching [this presentation on QEWD-JSdb](https://www.youtube.com/watch?v=1TlAKTw167s&list=PLam_80-FY3vSPW9apMaczTN_4dtke9GYM)
, given by Rob Tweed at the [London Node.js Users Group](https://lnug.org)
 meeting in January 2020.

The [QEWD-JSdb Github Repository](https://github.com/robtweed/qewd-jsdb) is then the best place to continue.  It provides a comprehensive set of documentation and tutorials.  These include tutorials allowing you to explore, using JavaScript, the various NoSQL models that are already implemented in and an integral part of QEWD-JSdb.

QEWD-JSdb is actually an integral part of a more wide-reaching technology known as 
[QEWD](https://github.com/robtweed/qewd).  QEWD provides a back-end Node.js-based development platform and run-time environment for *databaseless* REST and interactive browser-based applications using QEWD-JSdb.

When using the resources in the [QEWD-JSdb repository](https://github.com/robtweed/qewd-jsdb)
, you'll be encouraged to use a Dockerised version of QEWD which includes QEWD-JSdb and uses the Open Source YottaDB Global Storage database.  See below for other options you can subsequently explore.


## QEWD-JSdb Dependencies

QEWD-JSdb will work, "right out of the box" with any Global Storage database implementation, provided it can be accessed via a suitable JavaScript/Node.js implementation of the core Global Storage APIs, ie:

- the [Basic APIs](./Basic_APIs.md)
- the [subscript traversal APIs](./Subscripts.md#the-next-and-previous-apis)
- the [Leaf Node traversal APIs](./Leaf_Nodes.md)
- the [Multi-User Access APIs](./Multi_User.md)

The [mg_dbx](https://github.com/chrisemunt/mg-dbx) module implements these APIs for the Natively Implemented Global Storage Databases (YottaDB, IRIS and Cach&eacute;)

Our layered abstrations of Global Storage are implemented in JavaScript/Node.js and include the same standard JavaScript implementations of the Global Storage APIs:

- [Global Storage for Redis](https://github.com/robtweed/ewd-redis-globals)
- [Global Storage for Berkeley DB and LMDB](https://github.com/chrisemunt/mg-dbx-bdb)


When interfaced to QEWD-JSdb, all these implementations of Global Storage appear identical to the developer, as they are all abstracted as QEWD-JSdb's *databaseless* persistent JavaScript Objects.  This means that you can develop an application using QEWD-JSdb using one Global Storage database and run it against any other Global Storage database and it will run without any changes being required!


## Trying out QEWD-JSdb with the Various Global Storage Databases

To make it easy to try out all the various implementations of Global Storage, we've created a number of pre-built Docker Containers and a set of associated Github Repositories that describe their use.  Feel free to try them out.  They all include the same set of documentation and tutorials, but each refers to the particular database implementation on which they are based:

- [IRIS](https://github.com/robtweed/qewd-jsdb-kit-iris)  Note that as IRIS is a proprietary licensed database, this kit makes use of a Docker Container created by InterSystems

- [YottaDB](https://github.com/robtweed/qewd-jsdb)

- [Redis](https://github.com/robtweed/qewd-jsdb-kit-redis)

- [BerkeleyDB](https://github.com/robtweed/qewd-jsdb-kit-bdb)


You'll see that most of the kits above also explain how to create non-Dockerised (ie native) installations of QEWD, QEWD-JSdb and the particular Global Storage Database.  

However, we've created [a specific kit if want to explore the use of a non-Dockerised implementation for YottaDB](https://github.com/robtweed/qewd-starter-kit-yottadb).

If you're using the IRIS database, you may want to explore its use as a networked database: ie where the Node.js QEWD environment runs on a separate physical server to the IRIS database.  If so, 
[see the kit provided in this repository](https://github.com/robtweed/qewd-starter-kit-iris-networked).




