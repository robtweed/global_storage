# Catering for Multi-User Access: Locking and Transactions in Global Storage

Like any database, Global Storage databases are designed for concurrent use by multiple users.  However, in order for this to be possible, it is important that mechanisms are in place to prevent more than one user changing a particular database record at the same time.  This is especially important if a set of 
[indices](./Indexing.md) are also involved when setting a particular value.  If two or more users happen to simultaneously update the same data record, then the associated indices could become hopelessly corrupted.

A common technique for avoiding such situations is to make it possible to apply a lock around one or more database APIs.  Only the first user to reach that part of the application logic is allowed to set that lock, and all other users who try to set that same lock will be forced to wait, effectively in a queue, until the first user releases the lock on successful completion of their database update.  If the *unlock* is done after the last of the index update APIs completes, then you can protect against index corruption.

Global Storage databases provide such a mechanism by including a *Lock* and *Unlock* API.

## *Lock*

The *Lock* API requires 2 arguments:

- Global Name (eg *employee*)
- An array or list of subscripts (eg ["UK", "London", 123456789, "name"])

The Global Node to be *Lock*ed in this way may or may not physically exist, and can be either an Intermediate or Leaf Node.

Typically, if you are updating a database record for, say, an employee, and you want to prevent index corruption while they are being updated, you would *Lock* the Intermediate Node for the employee Id being updated, eg:

        LOCK employee["UK", "London", 123456789]

The *Lock* API is synchronous.  It will be successfully completed, and processing will move on to the next command, only if nobody else has set a *Lock* for the specified Global Node.  Otherwise, processing will halt until the current holder of the *Lock* releases it for that Global Node.

The *Lock* API will return *true* on successful completion.

An optional third argument - a timeout specified in seconds - is usually also specified, preventing the risk of a user being permanently locked in the event of some unforeseen error occurring elsewhere.

If the timeout expires before the *Lock* is released (by another user issuing an *Unlock*), then the *Lock* API returns *false*.


## *Unlock*

The *Unlock* API requires 2 arguments:

- Global Name (eg *employee*)
- An array or list of subscripts (eg ["UK", "London", 123456789, "name"])

The Global Node specified in the Unlock should be the same as specified in the corresponding *Lock*.

The *Unlock* API releases the previously-set *Lock* on the specified Global Node, allowing any other user waiting on the same *Lock* to proceed.

Note that applying an *Unlock* to a Global Node that has not been *Lock*ed is simply ignored.


# Transactions in Global Storage

A *transaction* is a set of database actions that should always be completed as a group.

For example, when a Employee name is changed, the *last_name* Index record for the previous name value must be deleted before a new *last_name* Index record is created.  A *transaction* defining three grouped APIs should be therefore specified:

- delete old name index record
- create new name index record
- change name property in data record

Clearly, by specifying a *Lock* API before this group of APIs and an *Unlock* API at the end of this group would be a simple and basic way to define a *transaction*.

However, *transactions* in database systems are usually considered to be more complex that this, and should provide a means of handling any unforseen error occurring in the middle of a group of database APIs.  Left unchecked, such an error would cause corrupted index records, with the APIs that managed to complete successfully properly pointing to the correct data record(s), but the ones that did not manage to complete will either not exist at all or will be pointing to the wrong data record(s).

*Transactions* therefore usually provide something called *Rollback* which is triggered in the event of an error.  *Rollback* unwinds or undoes any completed APIs within the transaction, reverting the database back to its state before the *Transaction* commenced, therefore removing any index corruption and therefore ensuring that so-called *database integrity* is maintained.

Typically, database APIs within a *Transaction* are effectively only notionally actioned until the last one is invoked, at which point the entire group of APIs are *Committed*.

Most Global Storage implementations therefore also include a set of APIs for implementing such a *Transaction* scheme:

- TStart: signals the start of a Transaction
- TEnd: signals the end of a Transaction
- TCommit: signals that the group of database APIs between the *TStart* and *TEnd* can be Committed
- TRollback: signals that something went wrong between the TStart and TEnd, and any APIs that were invoked after the TStart should be undone.

Note that Transaction APIs are synchronous.

If you use *Transactions*, you don't need to use Locks.  Usually *Transactions* are the better mechanism to use, but *Locks* still have their uses for more simple situations.


