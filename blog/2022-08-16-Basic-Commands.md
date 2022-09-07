---
slug: Basic-Commands-in-FerretDB
title: Basic_Commands
authors:
  - name: Ander Nikah
    title: Basic Commands in FerretDB
    url: https://github.com/fashander
    image_url: https://github.com/fashander.png
tags: [MongoDb, CRUD, Basic Commands]
---

# How to Implement Basic MongoDB Tutorials With FerretDB

As MongoDB moves away from its open-source roots, developers and tech enthusiasts need an alternative—a new way to handle and manage NoSQL operations.

FerretDB is an open-source proxy that converts MongoDB queries to SQL. 

In this blog post, you'll learn how to pass basic MongoDB CRUD operations using FerretDB.


## MongoDB Crud Operations With FerretDB

CRUD (Create, Read, Update, and Delete) operations are fundamental to any database management. They allow a user to interact with the database to create or modify documents within it. 

To perform common MongoDB operations in FerretDB, you need to have [Git](https://git-scm.com/downloads) and [Docker](https://www.docker.com/get-started) installed on your local machine. 

Once you are ready, install FerretDB by following the quickstart instructions on the [GitHub account.](https://github.com/FerretDB/FerretDB) 

Once you have FerretDB running, you’ll be greeted with a **test** collection. Show the list of commands using the following command:

```

show dbs

```

Create a new database by running the `use &lt;database_name>`. For example,

```

use inventory

```

This creates a new collection – **inventory** – in your FerretDB storage backend on PostgreSQL. 

Read on to learn more about the basic commands or operations you can perform on FerretDB.


## Create Operation

Like MongoDB, FerretDB uses the `insertOne()` and `insertMany()` commands to create new records in a database.


### insertOne()

Using the **inventory** collection created in the last section, you can insert a single document record into the database by calling the `insertOne()` method and then insert the fields and values as follows:

```

db.collection.insertOne({field1: value1, field2: value2, …})

```

This operation established the schema of the database collection. You should note, however, that if a collection does not exist yet, the insert command will automatically create one for you. Let’s insert a single document into the collection:

```

db.inventory.insertOne({item: "carpet", discount:false, cost: 500, qty: 30, tags: ["grey"], size: { h: 28, w: 35.5, uom: "cm" }})

```

This line of code creates a new document in your collection. If the write operation is successful, you’ll get a write concern response with _acknowledged_ set as ‘true’, and the id of the transaction (_insertedID_) containing the ObjectId.

{

  acknowledged: true,

  insertedId: ObjectId("63079f553f266779f14fa1ed")

}


### insertMany()

Similarly, instead of creating just a single document in the collection, you can create multiple documents with the `insertMany()` command. Indicate that you are inserting multiple document records with a bracket inside of the method and then each document using commas.

Now let’s see how this works, using a new collection.

```

db.inventory.insertMany([{item: "rug", discount: true, cost: 390, qty: 5, tags: ["wool", "white"], size: {h: 10, w: 34, uom: "cm"}},{item: "mirror", discount: true, cost: 200, qty: 50, tags: ["bedstand", "rectangular"], size: {h: 40, w: 12, uom: "cm"}}])

```

This operation inserts two new documents into the collection. 


## Read Operation

The read operations in FerretDB take a similar approach to those of MongoDB. There are two methods for querying records – `doc.collection.find()` and `doc.collection.findOne()`.


### find()

This operation selects all the documents in a collection that matches the query parameters. If there are no parameters, the method selects all the records present in the collection. Let’s attempt to select all the documents in the inventory collection created earlier. 

```

db.inventory.find()

``` 

**[Image needed here]**

In the query result, you’ll notice a new field (_id) was added to the document and assigned the ObjectId. Now let’s add a query parameter to the `find()` operation. 

```

db.inventory.find({item:'carpet'})

```

You can filter the collection for documents containing at least one of an array of specified values. To do this, an `$in` operator containing an error specific value will be added to the query filter. The following syntax represents the `$in` operation.

```

{ field: { $in: [&lt;value1>, &lt;value2>, ... &lt;valueN> ] } }

```

For instance, let’s add an argument to find all items with a cost of 390 or 500:

```

db.inventory.find({cost:{$in:[390,500]}})

```

The response from this is as follows:

```

[

 {

    _id: ObjectId("63088f973f266779f14fa1ef"),

    item: 'carpet',

    discount: false,

    cost: 500,

    qty: 30,

    tags: [ 'grey' ],

    size: { h: 28, w: 35.5, uom: 'cm' }

  },

  {

    _id: ObjectId("630891403f266779f14fa1f0"),

    item: 'rug',

    discount: true,

    cost: 390,

    qty: 5,

    tags: [ 'wool', 'white' ],

    size: { h: 10, w: 34, uom: 'cm' }

  }

]

```

To select documents in the collection with a _qty_ field that’s less than 50.

```

db.inventory.find({qty:{$lt:50}})

```

You can also perform other operations on the find() method, such as:



* $gt: greater than
* $gte: greater or equal to
* $lte: less than or equal to
* $nin: not in the array
* $ne: not equal
* $eq: equal


### findOne()

The `findOne()` operation selects the first document that matches a specified set of query parameters. For instance, let’s query the collection for documents that have the discount set to true.

```

db.inventory.findOne({discount:true})

```

Even though two documents match this criteria, the query results display only the first one. 

```

{

  _id: ObjectId("630891403f266779f14fa1f0"),

  item: 'rug',

  discount: true,

  cost: 390,

  qty: 5,

  tags: [ 'wool', 'white' ],

  size: { h: 10, w: 34, uom: 'cm' }

}

``` 


## Update Operation

Update operations are write operations that accept a query parameter and changes to be applied to a document. Take note, however, that **FerretDB maintains a single document-level atomicity**. 

There are three basic methods available for updating documents: `updateOne()`, `updateMany()`, and `replaceOne()`. 


### updateOne()

The `updateOne()` method takes a query parameter and updates a single document in a collection. The following syntax depicts the update operation where $set is the field to be updated in the document:

```

db.collection.updateOne({&lt;query-params>}, {$set: {&lt;update fields>}})

```
changing this and that.
```

db.inventory.updateOne({item:'rug'}, {$set: {qty:3}})

```

If this operation is successful, the queried document will be updated and the write concern should return:

```

{

  acknowledged: true,

  insertedId: null,

  matchedCount: 1,

  modifiedCount: 1,

  upsertedCount: 0

}

```


### updateMany()

The `updateMany()` operation is able to take a query and make updates to many documents at once. 

```

db.inventory.updateMany({cost:{$lte: 500}}, {$set: {discount:false}})

```

The write concern for this operation should look like this and this: 

```bash

{

  acknowledged: true,

  insertedId: null,

  matchedCount: 3,

  modifiedCount: 2,

  upsertedCount: 0

}

```


### replaceOne()

The replaceOne() method is ideal if you intend to replace an entire document at once.

```jsx
db.inventory.replaceOne({item: "rug"}, {item:"headphone", discount: true})
```


## Delete Operation

The delete operations are atomic transactions that only affect a single collection. There are two methods for deleting documents in a collection – deleteOne() and deleteMany().


### deleteOne()

The `deleteOne()` method takes in a query parameter that filters and deletes a document in your collection. Take note that this operation deletes the first record in the collection. 

```jsx

db.inventory.deleteOne({item:'carpet'})

```

The write concern:

```bash

{ acknowledged: true, deletedCount: 1 }

```


### deleteMany()

The deleteMany() method is used for deleting multiple documents in a collection. The operation takes in a query, then filters and deletes the documents matching the query.

```jsx
db.inventory.deleteMany({discount: false})
```


## Get Started With Basic MongoDB Tutorials With FerretDB 



* Conclusion
* CTA to Github quickstart page 