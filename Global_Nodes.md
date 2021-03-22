# Global Nodes

## Intermediate and Leaf Nodes in a Global

So far we've really only focused on Global Nodes that hold data, *eg*:

        employee["UK", "London", 123456789, "name"] = "Rob Tweed"

However, in a Global Storage Database, each one of the levels within the hierarchy represented by this Node's subscripts is also accessible as a Global Node, *ie* for the example above, the following Global Nodes also exist and can be accessed:

        employee["UK", "London", 123456789]
        employee["UK", "London"]
        employee["UK"]

These nodes, which do not, themselves, have a data value, but which have one or more Child Nodes beneath them, are known as *Intermediate Nodes*.  To clarify what I mean by *Child Nodes*, in the simple example above:

        employee["UK"]  

has 1 Child Node, represented by the additional subscript value *London*


        employee["UK", "London"]  

has 1 Child Node, represented by the additional subscript value *123456789*


        employee["UK", "London", 123456789] 

has 1 Child Node, represented by the additional subscript value *name*


If we added this Leaf Node:

        employee["UK", "London", 123456789, "job_title"] = "Consultant"

then:

        employee["UK", "London", 123456789] 

now has 2 Child Nodes, represented by the additional subscript values *name* and *job_title*.


Global Nodes that hold a data value and have no Child Nodes are known as *Leaf Nodes*. *ie* in our example:

        employee["UK", "London", 123456789, "name"]
        employee["UK", "London", 123456789, "job_title"]

are Leaf Nodes


## Intermediate Nodes Can Have A Data Value

One somewhat unusual feature of Global Storage is that, unlike JSON, an Intermediate Node can actually hold a data value if required.  Personally I don't like this capability and tend not to make use of it, but you should be aware that it exists and you can make use of it if you wish.  You also need to be aware of this feature in order to fully understand one of the Global Storage APIs that we'll discuss later: the *data* API.

To summarise, the following would be legitimate in Global Storage:

        employee["UK"] = 10                                         Intermediate Node with value
        employee["UK", "London"] = 21                               Intermediate Node with value
        employee["UK", "London", 123456789]                         Intermediate Node without value
        employee["UK", "London", 123456789, "name"] = "Rob Tweed"   Leaf Node

Note that a Leaf Node **must** have a value, by definition.

However, I'd recommend not giving values to Intermediate Nodes. *ie* this would be a more usual scenario:

        employee["UK"]                                              Intermediate Node without value
        employee["UK", "London"]                                    Intermediate Node without value
        employee["UK", "London", 123456789]                         Intermediate Node without value
        employee["UK", "London", 123456789, "name"] = "Rob Tweed"   Leaf Node

