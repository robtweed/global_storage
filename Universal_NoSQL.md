# Universal NoSQL as a consequence of Global Storage

# NoSQL?

In 2009, the term [*NoSQL*](https://en.wikipedia.org/wiki/NoSQL)
 was coined as a generic term for any database technology that was not a Relational database (with its associated Structured Query Language or SQL, hence the name).  The concept of *NoSQL* attracted considerable attention and created quite a bit of  disruption at the time: since the mid to late 1970s and up to that time, Relational Databases had pretty much become the established norm: if you wanted a database, it would simply be assumed it would be a Relational database.

The resulting *NoSQL* movement gained traction because the growing demands for databases that powered Web-based platforms were beginning to expose potential weaknesses or deficiencies in the Relational architecture and SQL, primarily in the areas of raw performance and high-end scalability once database volumes began to reach stratospheric levels.  Alternative searching technology such as Map/Reduce were being shown to be more scalable than SQL.  At the time, this *NoSQL* movement served to bring more widespread attention to and recognition of a whole range of alternative databases that had, by that time, begun to emerge to meet these so-called *Internet-Scale* demands.

The databases that fell within the *NoSQL* moniker could be further sub-categorised as:

- Key/Value Stores (eg Redis, memcached);
- Columnar Stores (eg Cassandra, HBase);
- Document Databases (eg CouchDB, MongoDB)
- Graph Databases (eg Neo4J)

Also, on the fringes of *NoSQL* were so-called Native XML Databases (eg MarkLogic at that time)


# A New Term for an Old Concept?

An interesting aspect of *NoSQL* was that the earliest databases that emerged in the late 1950s and 1960s were non-relational.  Until [Edgar Codd](https://en.wikipedia.org/wiki/Edgar_F._Codd) 
published his hugely influential paper "A Relational Model of Data for Large Shared Data Banks" in 1970 while working for IBM, the predominant database models were actually based around 
[hierarchical](https://en.wikipedia.org/wiki/Hierarchical_database_model) and 
[network](https://en.wikipedia.org/wiki/Network_model) architectures.  It wasn't really until 1979, when Larry Ellison adopted Codd's ideas and launched the now ubiquitous Oracle Database that Relational/SQL databases actually took off.

Global Storage databases first appeared in the late 1960s and early 1970s, an example of one of the then in-vogue hierarchical databases.  Global Storage databases have actually been extremely successful in some specific niches, in particular Healthcare and Financial Services/Banking.


[EPIC](epic.com), one of the most-used and most successful so-called Electronic Healthcare Records (EHR), and used by hospitals around the world, is implemented with a Global Storage database.  Sadly, however, outside these niche marketplaces, Global Storage databases are little-known: in fact most people in the IT world have never heard of them, even though there's a strong likelihood that much of their healthcare history is recorded in one, and that their banking may rely on them!


# Global Storage: The Universal NoSQL Platform?

Almost all the NoSQL databases are what I'd describe as "one-trick ponies": They are designed to implement just one particular NoSQL category, and they are just fine if all your data can be represented by that category's data model.  More often than not, however, you'll find that your database requirements can't be satisfied by one particular category, resulting in the need for multiple databases, each of which need to be somehow kept in synchronisation with each other, and each requiring different database management skills: not an ideal solution.

In 2010, [we published a paper jointly with George James](http://www.mgateway.com/docs/universalNoSQL.pdf), which explained how Global Storage could be used pretty straightforwardly to implement *all* the NoSQL database categories, and therefore describing it as a *Universal NoSQL* Database, something unique in the database world.

Indeed Global Storage even goes beyond that, proving capable of also supporting the Relational Database model.  At least one of the major Global Storage database vendors provides a fully-functional Relational capability, including SQL as part of their offering.  
Furthermore, we provide an [Open Source implementation](https://github.com/chrisemunt/mgsql) that can be used on some Global Storage implementations.

The reason for this is straightforward: pretty much every database technology is actually implemented on top of an underlying hierarchical model, the most common being a so-called 
[*b-tree*](https://en.wikipedia.org/wiki/B-tree). It therefore follows that the highly-flexible (and much higher level) hierarchical model provided by Global Storage naturally provides the basis for implementing every other database model you want, both NoSQL and Relational.

One of the interesting consequences of this is that any database technology on which Global Storage can be implemented immediately becomes a *Universal NoSQL Database*!  Possibly one of the most extreme examples of this is Redis which is usually thought of as being little more than a Key/Value store with a somewhat limited (if extremely high-performance) repertoire of capabilities.  However, our 
[Global Storage implementation for Redis](https://github.com/robtweed/ewd-redis-globals) transforms it, giving it database capabilities previously considered way beyond it's abilities!

Even more interesting is the fact that Global Storage can support any and all other database models *simultaneously* if required.  You could literally store some data using a Document Database model, some Relationally and some as key/value storage, all at the same time and all in the same physical database!

There is really no other database technology out there that has such a capability.


# Example Illustrations of NoSQL and Relational Implementations

If you're interested to understand how the various database models can be implemented using Global Storage, I've put together some suggestions/illustrations showing how it might be done in the following documents.

To fully understand these illustrations, you really need to have read the document explaining 
the basics of [how Global Storage works](./Global_Storage.md).  After that, you'll see that each database model
is just a relatively straightforward application of the basic capabilities of Global Storage.

- [Key/Value Storage](./Key_Value.md)
- [Document Database](./Document_DB.md)
- Columnar/Tabular Database
- Graph Database
- XML Database/Persistent DOM
- Relational Database

Note: with some exceptions, these illustrations aren't intended to be definitive specifications for modelling each type of database, but more to demonstrate how the basic capabilities of Global Storage can be harnessed to implement the needed functionality.  I've made several available as fully-functional implementations in an Open Source project named [QEWD-JSdb](https://github.com/robtweed/qewd-jsdb):

- Key/Value and Key/Object Storage
- Document Database/Persistent JSON
- XML Database/Persistent DOM

QEWD-JSdb can be used with any Global Storage implementation.

