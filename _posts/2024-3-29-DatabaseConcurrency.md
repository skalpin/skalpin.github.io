---
layout: post
title:  "Database Concurrency"
date:   2024-03-29 00:00:00 +0000
categories: update
---

I am in a technical book club and we are currently working through a book
"Designing Data-Intesive Applications" by Martin Kleppmann. We just finished
Chapter 7 - Transactions. This chapter focuses on transactions, but really
zeros in on why transactions exist. They exist to help developers prevent
concurrency issues.

## Transactions

Personally, transactions are not my first thought when working on an
application feature. Generally I'm concerned with what type of data
needs to be stored, how the applciation should read it, and making
sure the application writes to the correct records. What happens when
multiple users are using the application is often an afterthought. As
in after a bug has been submitted that some data was overwritten.

Even before committing to using transactions, I prefer to structure the data
in a way that doesn't require me to update multiple records. I would rather
have my application read and write data in an isolated way so that it never
needs to worry about another user overwriting it.

Of course this is nieve. Sure, you can structure your data and data access
as cleanly as you want, but you are going to run into situations where
denormalizing the data to improve performance will force you to write
data to multiple records assuming they are in the same state as you read
them.

## Optimistic Concurrency Control

Optimistic Concurrency Control assumes that data conflicts will not occur
and provides better performance. This is because it does not require
the database to lock and keep track of resources that are being worked on.

Transictions are one form of optimistic concurrency control, but there
are other ways the application can make sure writes are not ovewriting
another users data.

There are some great articles that discuss concurrency, but since I tend
to use entity framework with SQL server, this is my favorite.
[https://learn.microsoft.com/en-us/ef/core/saving/concurrency?tabs=data-annotations#optimistic-concurrency](https://learn.microsoft.com/en-us/ef/core/saving/concurrency?tabs=data-annotations#optimistic-concurrency)

SQL Server has the concept of a `rowversion` column type that can be
used by Entity Framework. When data is read / manupliated / written back to the
database, the rowversion remains the same in application code. Then when data is
written back to the database, the database can check the `rowversion` value given
and the current `rowversion` on the row. If they don't match, the database server
can abort the write, and throw an exception to the application code. The application
can then decide what to do with it.

## Aggregate Roots and Concurrency

So which persistent objects need to have a rowversion column in the relational
database to make sure that data does not get lost? An aggregate is a collection
of objects that make up an entity. For example a Customer entity will have an
address value. But you probably don't want to access address directly. In our
application Address only makes sense in the context of a Customer. Therefore
our application shouldn't update an Address directly, it should update a Customer's
Address.

Concurrency needs to be considered at the Aggregate Root because the Aggregate Root holds
the invariants to make sure state remains consistent and valid. When the Aggregate
Root object is loaded into memory, it needs to have a concurrency token or
rowversion that will allow the application to detect when the Customer object
has been updated without their knowledge. This will allow the Customer to determine
if it can safely continue with the update, or if it needs to abort the change due
to a conflict.

## Summary

This is a quick overview of optimistic concurrency control, transactions, and
how it applies to your application model. I completely skipped over pessimistic
concurrency control because I do not work on any systems that need it, and I don't
think may do. Most applications can get away with optimistic concurrency control,
but it's important that application developers think about concurrency. It's too
easy to do your feature testing locally, find that everything is working okay, and
push changes to quality assurance for testing. Then only to discover bugs that
are difficult to reproduce, diagnose and resolve.
