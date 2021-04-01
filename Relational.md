# Implementing a Relational Database using Globals

## What is a Relational Database?

In a Relational Database, information is represented as sets of 2-dimensional tables, with relationships between the tables described in terms of:

- one-to-one relationships
- one-to-many relationships
- many-to-many relationships

The data in a table is defined as a set of columns, with each column representing a property or attribute.  Each row in the table represents the data for an individual member of that table, for example, an individual person, or perhaps an individual invoice.

One or more of the columns (also known as *fields*) in a table will be defined as *keys* or *key fields*. The values for a table's keys define a unique member of the table: in other words, there can only be one member of the table with a particular set of values for the key fields.

Conversely, one or more rows in a table can have the same values for non-key fields.

Related tables will have some relationship between the keys of one table and the fields of the other.

Relational Databases are also associated with the Structured Query Language (SQL) which allows querying within and between tables, the latter based on the the relationships between them.

In this document I won't be referring to SQL: I'll be focusing on how a set of related tables can be defined using Global Storage.  The standard Global Storage APIs can be used for basic querying, but it's entirely possible to then implement SQL as a query language for those tables.  The main native implementations of Global Storage databases all have SQL implementations available.

## Example of Relational Tables

It's probably easiest to explain how to model a Relational Database using Global Storage by using a straightforward example.  Consider the following:

- a table representing customers (*CUSTOMER*)
- a table representing orders (*ORDERS*)
- a table representing the individual items that make up an order

In terms of relationships:

- a customer can place one or more orders
- an order is made up of one or more items

The table columns or fields are as follows:

- CUSTOMER:

  - customerId: a unique customer identifier.  This is this table's one and only *key* field.
  - name: the customer's name
  - address: the customer's address
  - totalOrders: the total number of orders placed by this customer to date

- ORDERS:

  - orderId: a unique order identifier.  This is this table's one and only *key* field.
  - customerId: the id of the customer who placed the order. This provides the relationship back to the member of the CUSTOMER table that placed the order.  It is not a key field for ORDERS, but as it is a *key field* in another table, it is referred to as a *foreign key* within the ORDERS table.
  - orderDate: the date the order was placed
  - invoiceDate: the date on which the order was invoiced
  - totalValue: the total value of the order in, let's say, UK pounds.

- ITEM:

  - orderId: the identifier of the order to which this item belongs.  This is a *key* field for this table.
  - itemNumber: the item number within the order.  This is also a *key* field for this table.
  - description: text describing the item
  - value: the value of the item

  In the ITEM table, therefore, rows are uniquely defined by a combination of two *key fields*: *orderId* and *itemNumber*.

Note that in the CUSTOMER table, the *totalOrders* field is not intended to be specified by a user.  Instead it is intended to be calculated automatically, being the sum of all *totalValue* fields in the ORDERS table, for orders placed by this customer.  As such, this field is known as a *derived field*.

Similarly, the *totalValue* field within the ORDERS table is, itself, a *derived field*, being the sum of all the *value* fields for members of the ITEM table belonging to the same *orderId*.


Here is this set of tables, summarised in diagrammatic form:

![rdb1](./diagrams/rdb1.png)


## Global Storage Representation

A Relational Database normally requires such table definitions to be formally defined/specified.  This is often done via [SQL commands](https://www.w3schools.com/sql/sql_primarykey.ASP).

Global Storage, of course, is schema-free, so, for SQL support of a relational database built using Global Storage, it's usually necessary to also create a set of Globals for the schemas that define relational tables.  This is beyond the scope of this document: we'll just focus on how the tables themselves could be modelled.

As always, there are numerous potential ways to model relational tables, but here's one such way.  In this suggested specification, the intention is that the model is *computable*, with the Global Storage for any one table containing enough information to navigate up and down any associated relationships to/from other tables.

At the very top, therefore, we have a "master" record, providing pointers down to top-level tables:

        DATABASE["subTable", top_table_name, "key:" + key_name(s)"] = ""

  where key_name(s) is a comma-delimited list of the *key field* names for the top table.

The tables themselves could be defined using the model:

        tableName[key_1, (...key_n,) "fields", field_name] = field_value

while the relationship to other subsidiary tables could be modelled as:

- pointing down a one-to-many relationship from the one to the many:

        tableName[key_1, (...key_n,) "subTable", sub_tableName, "key:" + key_name(s), key_value*] = ""

    where key_value* is one or more key values, as specified by the key_name(s) list.

- pointing up from the many to the one:

        tableName[key_1, (...key_n,) "parentTable", parent_tableName, "key:" + key_name(s), key_value*] = ""

This is somewhat more complex a structure than we've seen for the other NoSQL database models.  It's noteworthy because it demonstrates just how flexible your design of subscripts and the semantics of their values can be.  This model hopefully becomes more understandable when applied to an example.

For our example, the model would look as follows

- the top-level "master" record, which tells us that we have a top-level table named *CUSTOMER" which has a single *key field* named *customerId*:

        DATABASE["subTable", "CUSTOMER", "key:customerId"] = ""

The *CUSTOMER* table has the following fields for each *customerId*:

        CUSTOMER[customerId, "fields", "name"] = name
        CUSTOMER[customerId, "fields", "address"] = address
        CUSTOMER[customerId, "fields", "totalValue"] = totalValue

Its parent table is the top-level "master" table (*DATABASE*), which has no keys:

        CUSTOMER[customerId, "parentTable", "DATABASE", "key:"] = ""

The *CUSTOMER* table has one related sub-table - *ORDERS* - which has a single *key field* named *orderId*.  Each such *subTable* record specifies the values of the *orderId*s related to this *customerId*:

        CUSTOMER[customerId, "subTable", "ORDERS", "key:orderId", orderId] = ""


The *ORDERS* table has the following fields for each *orderId*:

        ORDERS[orderId, "fields", "customerId"] = customerId
        ORDERS[orderId, "fields", "orderDate"] = orderDate
        ORDERS[orderId, "fields", "invoiceDate"] = invoiceDate
        ORDERS[orderId, "fields", "totalValue"] = totalValue

Its parent table is the *CUSTOMER* table which has a single key (*custimerId*), whose value is specified as the last subscript:

        ORDERS[orderId, "parentTable", "CUSTOMER", "key:customerId", customerId] = ""

The *ORDERS* table has one related sub-table - *ITEM* - which has two *key field*s named *orderId* and *itemNumber*.  Because *orderId* is already a *key* for the *ORDERS* table, we don't need to add it as a redundant penultimate subscript: instead we just specify the relevant value(s) for the *itemNumber* *key fields* of the *ITEM* table:

        ORDERS[orderId, "subTable", "ITEM", "key:orderId,itemNumber", itemNumber] = "" 


Finally, the *ITEM* table has the following fields for each unique combination of *orderId* and *itemNumber*:

        ITEM[orderId, itemNumber, "fields", "description"] = description
        ITEM[orderId, itemNumber, "fields", "value"] = value

Its parent table is *ORDERS* which has a single key (*orderId*). Because this is already specified as a *key field* in each *ITEM* record, its value can be implied and therefore doesn't need to be redundantly specified as the final subscript:

        ITEM[orderId, itemNumber, "parentTable", "ORDERS", "key:orderId”] = ""

Finally, the *ITEM* table doesn't have any related sub-tables.


So, based on this model, let's show a worked example of some actual records for these 3 tables in Global Storage:

        DATABASE["subTable", "CUSTOMER", "key:customerId"] = ""

        CUSTOMER[123, "fields", "name"] = "Rob Tweed"
        CUSTOMER[123, "fields", "address"] = "1, The Street, Redhill, Surrey"
        CUSTOMER[123, "fields", "totalValue"] = 100.00
        CUSTOMER[123, "parentTable", "DATABASE", "key:"] = ""
        CUSTOMER[123, "subTable", "ORDERS", "key:orderId", 28] = ""
        CUSTOMER[123, "subTable", "ORDERS", "key:orderId", 42] = ""

        ORDERS[28, "fields", "customerId"] = 123
        ORDERS[28, "fields", "orderDate"] = "23/06/2020"
        ORDERS[28, "fields", "invoiceDate"] = "24/06/2020"
        ORDERS[28, "fields", "totalValue"] = 40.00
        ORDERS[28, "parentTable", "CUSTOMER", "key:customerId", 123] = ""
        ORDERS[28, "subTable", "ITEM", "key:orderId,itemNumber", 1] = "" 
        ORDERS[28, "subTable", "ITEM", "key:orderId,itemNumber", 2] = "" 
        ORDERS[28, "subTable", "ITEM", "key:orderId,itemNumber", 3] = "" 

        ORDERS[42, "fields", "customerId"] = 123
        ORDERS[42, "fields", "orderDate"] = "19/09/2020"
        ORDERS[42, "fields", "invoiceDate"] = "21/09/2020"
        ORDERS[42, "fields", "totalValue"] = 60.00
        ORDERS[42, "parentTable", "CUSTOMER", "key:customerId", 123] = ""
        ORDERS[42, "subTable", "ITEM", "key:orderId,itemNumber", 1] = "" 

        ITEM[28, 1, "fields", "description"] = "widget 1"
        ITEM[28, 1, "fields", "value"] = 10.00
        ITEM[28, 1, "parentTable", "ORDERS", "key:orderId”] = ""

        ITEM[28, 2, "fields", "description"] = "widget 2"
        ITEM[28, 2, "fields", "value"] = 25.00
        ITEM[28, 2, "parentTable", "ORDERS", "key:orderId”] = ""

        ITEM[28, 3, "fields", "description"] = "widget 3"
        ITEM[28, 3, "fields", "value"] = 5.00
        ITEM[28, 3, "parentTable", "ORDERS", "key:orderId”] = ""

        ITEM[42, 1, "fields", "description"] = "widget 4"
        ITEM[42, 1, "fields", "value"] = 60.00
        ITEM[42, 1, "parentTable", "ORDERS", "key:orderId”] = ""

