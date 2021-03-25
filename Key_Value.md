# Key/Value Storage using Globals

## Basic Key/Value Storage


Implementing basic Key/Value storage is very simple with Global Storage.  It's simply a matter of using this very basic Global Structure:

        keyValueStore[key] = value


For example:

        telephone["211-555-9012"] = "James, George"
        telephone["617-555-1414"] = "Tweed, Rob" 

Viewed diagrammatically, this set of key/value pairs would look like this within a Global:

![key_value_1](./diagrams/kv1.png)


To add a record to the Key/Value Store:

        SET telephone[number] = name

To get a name if you know a key:

        name = GET telephone[number]

To update a value for a particular key:

        SET telephone[number] = new_name

To remove a record:

        KILL telephone[number]


To delete the entire telephone Key/Value store:

        KILL telephone


