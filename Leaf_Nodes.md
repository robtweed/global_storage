# Traversal of Leaf Nodes in Global Storage

This is another important feature of Global Storage.  It's not used as frequently as the *Next* and *Previous* APIs that were described in the previous section, but it can be extremely useful when you need it!

Not only is Global Storage optimised for traversal from one subscript value to its immediate peers, Leaf Nodes are also optimised to point to their previous and next Leaf Nodes.  What constitutes a previous and next Leaf Nodes is determined by the alphanumeric collation of the Global's subscripts, as described previously.

So, having accessed a particular Leaf Node in a Global, you can very efficiently get it its previous and/or next Leaf Node in the Global.  This can allow very rapid traversal through all the data within a Global, or through a sub-section/sub-tree of a Global.

Let's go back to that previous example where we'd created the following records, collated as shown according to their subscript values:

        employee["UK", Bristol", 987654321, "name"] = "John Smith"
        employee["UK", "London", 123456464, "job_title"] = "Accountant"
        employee["UK", "London", 123456464, "name"] = "John Smith"
        employee["UK", "London", 123456789, "job_title"] = "Consultant"
        employee["UK", "London", 123456789, "name"] = "Rob Tweed"
        employee["USA", Albany", 100011111, "name"] = "Tom Jones"

If we have accessed, for example, this Leaf Node:

        employee["UK", "London", 123456464, "name"]

then it has a pointer to its previous Leaf Node:

        employee["UK", "London", 123456464, "job_title"]

and a pointer to its next Leaf Node:

        employee["UK", "London", 123456789, "job_title"]


These Leaf Node pointers are automatically updated behind the scenes whenever a *Set* or *Kill* is invoked.


## The *Query* API

The Global Storage API that makes use of these Leaf Node pointers is named *Query*.

Implementation of the *Query* API can vary, but, in effect, it requires 3 arguments:

- Global Name (eg *employee*)
- An array or list of subscripts (eg ["UK", "London", 123456789, "name"])
- The direction of traversal: ie forwards or backwards, or next/previous

The *Query* API behaves as follows:

- If the Global Name and subscripts specify a Leaf Node that exists in the database, then what is returned is the reference to its previous or next Leaf Node, in the form of its Global Name and its array of subscripts.

- If the Global Name exists in the database, but the subscripts do not actually specify an existing Leaf Node, then what is returned is, in effect, a reference the previous or next Leaf Node for the one specified, *had it actually existed as a Leaf Node*.

- If, instead of an array of subscripts, the subscripts value is an empty string, then what is returned is:
  - a reference to the first Leaf Node of the Global, if the specified direction is *next*
  - a reference to the last Leaf Node of the Global, if the specified direction is *previous*

- If the specified Leaf Node is the first Leaf Node in the Global and the specified direction is *previous*, then an empty string is returned.

- Similarly, if the specified Node is the last Leaf Node in the Global and the specified direction is *next*, then an empty string is returned.

- if the Global Name doesn't exist in the database, an empty string is returned.

Used in an iteration loop, you can see that an empty string value for the subscript can be used to seed the iterations, and a return value of empty string can be used to determine completion of the traversal. 


## Comparison with *Next* and *Previous* APIs for Global Traversal

Leaf Node traversal through a Global can, of course, also be achieved by nesting *Next* APIs to iterate through all the subscripting levels.  However, in the example above, this would require:

- 2 iterations at subscript level 1 (to get *UK* and *USA*)
- 3 iterations at subscript level 2
- 4 iterations at subscript level 3
- 6 iterations at subscript level 4

So a total of 15 iterations to exhaustively traverse the Leaf Nodes of the Global.

By comparison, using the Leaf Node pointers allows traversal of the entire Global in just 6 iterations, a significant difference even in our tiny example Global.

So if what you're interested in is the Leaf Nodes and/or data records in a Global, the *Query* API can be a significantly more efficient means of traversal.

