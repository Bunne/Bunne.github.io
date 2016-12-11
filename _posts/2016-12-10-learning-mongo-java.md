---
title: MongoDB and Java
---

Diving deeper into MongoDB and how to use it programmatically.



## Being Smarter About Indexes

I didn't fully understand Indexes last time, they make much more sense now that I'm getting into constructing queries.

### Purpose of Indexing

From the [MongoDB](https://docs.mongodb.com/manual/indexes/) site:

*Indexes support the efficient execution of queries in MongoDB. Without indexes, MongoDB must perform a collection scan, i.e. scan every document in a collection, to select those documents that match the query statement. If an appropriate index exists for a query, MongoDB can use the index to limit the number of documents it must inspect.*

*Indexes are special data structures that store a small portion of the collection’s data set in an easy to traverse form. The index stores the value of a specific field or set of fields, ordered by the value of the field. The ordering of the index entries supports efficient equality matches and range-based query operations. In addition, MongoDB can return sorted results by using the ordering in the index.*

### Details of Indexing

Indexes can be **ascending** or **descending** and include multiple fields (**compund indexes**). 

~~~java
collection.createIndex(Indexes.ascending("name"));
collection.createIndex(Indexes.ascending("stars", "name"));
~~~

For more details in compound indexes:

~~~java
collection.createIndex(
	Indexes.compoundIndex(
		Indexes.descending("stars"),
		Indexes.ascending("name")));
~~~

#### Text

For text search of string content. Text indexes can include any field whose value is a string or an array of string elements. You can only have one per collection.

~~~java
collection.createIndex(Indexes.text("name"));
~~~

#### Hashed

Hashed indexes maintain entries with hashes of the values of the indexed field.

~~~java
collection.createIndex(Indexes.hashed("_id"));
~~~


#### Unique

A unique index ensures that the indexed fields do not store duplicate values; i.e. enforces uniqueness for the indexed fields. By default, MongoDB creates a unique index on the _id field during the creation of a collection.

~~~java
IndexOptions indexOptions = new IndexOptions().unique(true);
collection.createIndex(Indexes.ascending("name", "stars"), indexOptions);
~~~


## Querying

Helper function:

~~~java
Block<Document> printBlock = new Block<Document>() {
    @Override
    public void apply(final Document document) {
        System.out.println(document.toJson());
    }
};
~~~

### Details of Querying

Basic querying everything and then filtering.

~~~java
collection.find().forEach(printBlock);

collection.find(eq("name", "456 Cookies Shop"))
            .forEach(printBlock);
~~~

#### Filters and Helpers

Find documents that match this:

~~~java
collection.find(
    new Document("stars", new Document("$gte", 2)
          .append("$lt", 5))
          .append("categories", "Bakery")).forEach(printBlock);
~~~

Better, use the `Filters` helper methods:

~~~java
collection.find(
	and(
		gte("stars", 2),
		lt("stars", 5),
		eq("categories", "Bakery")))
	.forEach(printBlock);
~~~

### FindIterable

The `find()` method returns an instance of the FindIterable interface.

#### Projections

By default, queries in MongoDB return all fields in matching documents. To specify the fields to return in the matching documents, you can specify a projection document.

Either:

~~~java
collection.find(and(gte("stars", 2), lt("stars", 5), eq("categories", "Bakery")))
                .projection(new Document("name", 1)
                     .append("stars", 1)
                     .append("categories",1)
                     .append("_id", 0))
                .forEach(printBlock);
~~~

Or streamline it with the `Projections` class:

~~~java
collection.find(and(gte("stars", 2), lt("stars", 5), eq("categories", "Bakery")))
                .projection(fields(include("name", "stars", "categories"), excludeId()))
                .forEach(printBlock);
~~~

#### Sorting

Use the `FindIterable.sort()` method and `Sorts` helpers:


~~~java
collection.find(and(gte("stars", 2), lt("stars", 5), eq("categories", "Bakery")))
                .sort(Sorts.ascending("name"))
                .forEach(printBlock);
~~~


## Writing

### Creating

Create a document and use the `insertOne()` method:

~~~java
Document document = new Document("name", "Café Con Leche")
               .append("contact", new Document("phone", "228-555-0149")
                                       .append("email", "cafeconleche@example.com")
                                       .append("location",Arrays.asList(-73.92502, 40.8279556)))
               .append("stars", 3)
               .append("categories", Arrays.asList("Bakery", "Coffee", "Pastries"));

collection.insertOne(document);
~~~

To insert more, just create an `ArrayList<Document>`, fill it with `Document` objects, and use the `insertMany()` method.

### Updating

* **filter** - a document describing the query filter, which may not be null.
* **update** - a document describing the update, which may not be null. The update to apply must include only update operators.

The `Updates` class helps out here.

* **Updates.set** - Set specified key to specified value.
* **Updates.currentDate** - Modify the lastModified field to the current date. If the lastModified field does not exist, the operator adds the field to the document.

To update, use the `updateOne()` method:

~~~java
collection.updateOne(
                eq("_id", new ObjectId("57506d62f57802807471dd41")),
                combine(set("stars", 1), set("contact.phone", "228-555-9999"), currentDate("lastModified")));
~~~

Or to update many use the `updateMany()` method:

~~~java
collection.updateMany(
              eq("stars", 2),
              combine(set("stars", 0), currentDate("lastModified")));
~~~

### Replacing

Pass a new document into the `replaceOne()` method.

~~~java
collection.replaceOne(
                eq("_id", new ObjectId("57506d62f57802807471dd41")),
                new Document("name", "Green Salads Buffet")
                        .append("contact", "TBD")
                        .append("categories", Arrays.asList("Salads", "Health Foods", "Buffet")));
~~~

### Delete

Use the `deleteOne()` or `deleteMany()` methods, passing in filters.

~~~java
collection.deleteOne(eq("_id", new ObjectId("57506d62f57802807471dd41")));

collection.deleteMany(eq("stars", 4));
~~~

## Aggregation

Building data processing pipelines.

Use the `MongoCollection.aggregate()` method and the `Aggregates` helper class.

### Perform Aggregation

* First uses a `$match` stage to filter for documents whose categories array field contains the element Bakery.
* Then, uses a `$group` stage to group the matching documents by the stars field, accumulating a count of documents for each distinct value of stars. The `Accumulators` helper class is used.

~~~java
collection.aggregate(
      Arrays.asList(
              Aggregates.match(Filters.eq("categories", "Bakery")),
              Aggregates.group("$stars", Accumulators.sum("count", 1))
      )
).forEach(printBlock);
~~~

For best use, combine with [projections](#projections):

~~~java
collection.aggregate(
      Arrays.asList(
          Aggregates.project(
              Projections.fields(
                    Projections.excludeId(),
                    Projections.include("name"),
                    Projections.computed(
                            "firstCategory",
                            new Document("$arrayElemAt", Arrays.asList("$categories", 0))
                    )
              )
          )
      )
).forEach(printBlock);
~~~


--------------------------------------------------

# Sources

* [MongoDB: Java Driver Tutorial](https://mongodb.github.io/mongo-java-driver/3.4/driver/)
* [MongoDB: Indexes](https://docs.mongodb.com/manual/indexes/)
* [MongoDB: Compund Indexes](https://docs.mongodb.com/manual/core/index-compound/)
* [MongoDB: Hashed Indexes](https://docs.mongodb.com/manual/core/index-hashed/)
* [Hello-World Example](http://www.mkyong.com/mongodb/java-mongodb-hello-world-example/)