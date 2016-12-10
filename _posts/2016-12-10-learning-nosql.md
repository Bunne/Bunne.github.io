---
title: Looking Into NoSQL
---

I'd never used NoSQL, so I figured it was time to learn.


## What Is NoSQL

A non-relational database stores its information in **documents**: data structures composed of key-value pairs. Essentially, it's a dictionary, most closely a **JSON object**. Documents may contain other documents, lists, and lists of documents, as shown below.

Also, there aren't really tables in the same sense as relational databases. **Collections** are the closest thing to this; these are documents with a unique **_id** field.

~~~json
{
   "_id" : ObjectId("54c955492b7c8eb21818bd09"),
   "address" : {
      "street" : "2 Avenue",
      "zipcode" : "10075",
      "building" : "1480",
      "coord" : [ -73.9557413, 40.7720266 ]
   },
   "borough" : "Manhattan",
   "cuisine" : "Italian",
   "grades" : [
      {
         "date" : ISODate("2014-10-01T00:00:00Z"),
         "grade" : "A",
         "score" : 11
      },
      {
         "date" : ISODate("2014-01-16T00:00:00Z"),
         "grade" : "B",
         "score" : 17
      }
   ],
   "name" : "Vella",
   "restaurant_id" : "41704620"
}
~~~

## Basics

To start, have a **mongod** instance running on the system and run **mongo** in the shell.


### Insert

~~~json
db.restaurants.insert(
   {
      "address" : {
         "street" : "2 Avenue",
         "zipcode" : "10075",
         "building" : "1480",
         "coord" : [ -73.9557413, 40.7720266 ]
      },
      "borough" : "Manhattan",
      "cuisine" : "Italian",
      "grades" : [
         {
            "date" : ISODate("2014-10-01T00:00:00Z"),
            "grade" : "A",
            "score" : 11
         },
         {
            "date" : ISODate("2014-01-16T00:00:00Z"),
            "grade" : "B",
            "score" : 17
         }
      ],
      "name" : "Vella",
      "restaurant_id" : "41704620"
   }
)
~~~


### Find

~~~ json
db.restaurants.find({ <field1>: <value1>, <field2>: <value2>, ... })
~~~

To specify a condition on a field within an embedded document, use the **dot notation**. Dot notation requires quotes around the whole dotted field name.

~~~ json
db.restaurants.find( { "address.zipcode": "10075" } )
~~~

Use the **pretty()** method to get the data in a readable format.

~~~ json
db.restaurants.find( { "address.zipcode": "10075" } ).pretty()
~~~


### Operators

~~~ json
{ <field1>: { <operator1>: <value1> } }

EX.
db.restaurants.find( { "grades.score": { $gt: 30 } } )
db.restaurants.find( { "grades.score": { $lt: 10 } } )
~~~


### Logical AND

Just use commas.

~~~ json
db.restaurants.find(
	{ "cuisine": "Italian", "address.zipcode": "10075"}
	)
~~~

### Logical OR

Use the **`$or`** query operator.

~~~ json
db.restaurants.find(
   { $or: [ { "cuisine": "Italian" },
   			{ "address.zipcode": "10075" } ]
   }
~~~

### Sorting

Use the **sort()** method, giving fields to sort by, in order, along with the sort type. **1** for ascending, **-1** for descending.

~~~json
db.restaurants.find().sort(
	{ "borough": 1, "address.zipcode": 1}
	)
~~~

### Updating

Updating takes the following syntax.

~~~json
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
~~~

Example:

~~~json
db.restaurants.update(
  { "address.zipcode": "10016", cuisine: "Other" },
  {
    $set: { cuisine: "Category To Be Determined" },
    $set: { "address.street": "East 31st Street" }
    $currentDate: { "lastModified": true }
  },
  { multi: true}
)
~~~

### Replacing

Just use the **update()** method and pass a document.

~~~json
db.restaurants.update(
   { "restaurant_id" : "41704620" },
   {
     "name" : "Vella 2",
     "address" : {
              "coord" : [ -73.9557413, 40.7720266 ],
              "building" : "1480",
              "street" : "2 Avenue",
              "zipcode" : "10075"
     }
   }
)
~~~


### Removing

Use the **remove()** method to delete documents. By default, the method removes all matching documents. Set the **justOne** option to only remove the first result.

~~~json
db.restaurants.remove(
	{ "borough": "Queens" },
	{ justOne: true }
	)
~~~
## Why NoSQL?

There are lots of pros and cons for using both relational and non-relational databases, I went through a lot of discussions on the issue, but it was [this](https://www.pluralsight.com/blog/software-development/relational-non-relational-databases) article that really put it into perspective.

Essentially, the decision to use non-relational databases really comes down to the scale and complexity of the data being managed.

Use **Relational** for:

* Complicated querying and database transactions.
* Routine analysis of data in a set of relatively constant size.

The biggest downside of this is the difficulty of Object Relational Mapping (ORM).

Use **Non-Relational** for:

* Extremely large or fluctuating data sets.
* Sets of records in the same collection that have different fields or attributes.
* Storing serialized arrays in JSON objects.
* Avoiding de-normalizing your database schema or coding around performance and horizontal scalability issues.

The downsides come with complex querying and database operations that necessitate a good deal of overhead precaution.

## For Me

While I will definitely continue to play with MongoDB, I can't think of any real reason I would use it currently. My small applications certainly don't necessitate it and are probably best suited for a relational database. I'll continye to play with it though where I can, I'm really enjoying the possibilities.

# References

* [MongoDB Getting Started: Shell](https://docs.mongodb.com/getting-started/shell/)
* [MongoDB Getting Started: JAVA](http://mongodb.github.io/mongo-java-driver/3.4/driver/getting-started/quick-start/)
* [MongoDB Manual](https://docs.mongodb.com/manual/)
* [ReadWrite: When NoSQL Databases are Good For You](http://readwrite.com/2013/03/25/when-nosql-databases-are-good-for-you/)
* [Pluralsight: SQL vs NoSQL](https://www.pluralsight.com/blog/software-development/relational-non-relational-databases)