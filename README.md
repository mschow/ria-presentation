# A novice's guide to MongoDB

The MongoDB website describes MongoDB as a "Scalable, open-source, high performance, document oriented database" platform. Some of those terms are somewhat nebulous. To help elaborate I did some research on what exactly MongoDB is in greater terms.

# A "Document Oriented" NoSQL database

Because MongoDB is "horizontally scalable", it will allow for delivery of a greater amount of data at a faster performance. In essence, at any time you can create additional "nodes" for MongoDB. With my limited understanding of backend processes, what I think this means (and I might be wrong) is that a greater amount of data can be delivered at once because there is more than one "deliverer".

Data in MongoDB is stored as BSON (Binary JSON) "Documents" (Which are syntactically the same as objects) within a "Collection" (similar to a MySQL Table).

Here is an example of data stored in a Mongo database:

```
{
	"_id" : 1,
	"name" : { "first" : "John", "last" : "Backus" },
	"contribs" : [ "Fortran", "ALGOL", "Backus-Naur Form", "FP" ],
	"awards" : [
	           {
	             "award" : "W.W. McDowell Award",
	             "year" : 1967,
	             "by" : "IEEE Computer Society"
	           },
	           { "award" : "Draper Prize",
	             "year" : 1993,
	             "by" : "National Academy of Engineering"
	           }
	]
}
```

A few things to note:

-In MongoDB, an "ID" field is mandatory for all documents.
-As with in JSON, document fields can store objects and arrays.

# Using MongoDB

The following syntax can be used to retrieve data from a Mongo database and to connect to the Mongo database:

```
// Retrieve
var MongoClient = require('mongodb').MongoClient;

// Connect to the db
MongoClient.connect("mongodb://localhost:27017/exampleDb", function(err, db) {
});
```

Any of the following four will create a new collection in a Mongo DB:

```
db.collection('test', function(err, collection) {});

db.collection('test', {strict:true}, function(err, collection) {});

db.createCollection('test', function(err, collection) {});

db.createCollection('test', {strict:true}, function(err, collection) {});
```

The first one will not create a collection until a document is inserted into it.
The second one will create a collection, but only after checking to see that the collection does not already exist.
The third will first check to see if the collection already exists. If it does not, it will create the collection and return the object. Otherwise it will just return the object.
The fourth will make the method return an error if the collection already exists.


There are four main operations in MongoDB, and they are:

-Insert
-Update
-Remove
-Query

Here are some examples of valid insert operations:

```
var collection = db.collection('test');
var doc1 = {'hello':'doc1'};
var doc2 = {'hello':'doc2'};
var lotsOfDocs = [{'hello':'doc3'}, {'hello':'doc4'}];

collection.insert(doc1);

collection.insert(doc2, {w:1}, function(err, result) {});

collection.insert(lotsOfDocs, {w:1}, function(err, result) {});

```

As with collection creation, using "{w:1}"  will return errors in the event of an issue.


Here are some examples of valid update operations:

```
var collection = db.collection('test');
var doc = {mykey:1, fieldtoupdate:1};

collection.insert(doc, {w:1}, function(err, result) {
  collection.update({mykey:1}, {$set:{fieldtoupdate:2}}, {w:1}, function(err, result) {});
});

var doc2 = {mykey:2, docs:[{doc1:1}]};

collection.insert(doc2, {w:1}, function(err, result) {
  collection.update({mykey:2}, {$push:{docs:{doc2:1}}}, {w:1}, function(err, result) {});
});
```

Here are some examples of valid remove operations:

```
var collection = db.collection('test');
var docs = [{mykey:1}, {mykey:2}, {mykey:3}];

collection.insert(docs, {w:1}, function(err, result) {

collection.remove({mykey:1});

collection.remove({mykey:2}, {w:1}, function(err, result) {});

collection.remove();
});
```

Here are some examples of valid queries:

```
var collection = db.collection('test');
var docs = [{mykey:1}, {mykey:2}, {mykey:3}];

collection.insert(docs, {w:1}, function(err, result) {

  collection.find().toArray(function(err, items) {});

  var stream = collection.find({mykey:{$ne:2}}).stream();
  stream.on("data", function(item) {});
  stream.on("end", function() {});

  collection.findOne({mykey:1}, function(err, item) {});

});
```

Be aware that using the "find()" function does not retrieve data, instead it will allow you to select the method you would prefer to retrieve the data. For example, using "toArray" will push the data into an array.

For more information on using MongoDB, view the following: https://mongodb.github.io/node-mongodb-native/api-articles/nodekoarticle1.html
