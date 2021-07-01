# Chapter 1: What is Mongo DB?

# Lecture: What is the MongoDB Database?

### So what is the MongoDB Database?

-   A database, meaning a structured way to store and access data.
-   More specifically, it is a NoSQL database.

> NoSQL is a very generic term used to describe any data store that doesn't use legacy approach of related tables of data.
> This means that you're storing your data in an organized way, but not in rows and columns An example of NoSQL databases can be anything from a library card catalog, to a more sophisticated data store like MongoDB.

Since the range of what NoSQL means is so wide, we must narrow our definition further.

**MongoDB is a NoSQL document database.**

-   This means that data in MongoDB is stored as **documents**.
-   We will cover what a document is in the next lesson.
-   These documents are in turn stored in what we call **collections** of documents.
-   That's why MongoDB is categorized as a **NoSQL document database**.

In conclusion, the MongoDB database is a NoSQL document database.

# Lecture: What is a Document in MongoDB?

-   Let's take a closer look at what documents are and how they're used to store data in a structured way.
-   A document is a way to organize and store data as a set of **field-value pairs**.
-   Just like this, where the **field is a unique identifier for some data point**, and the **value is data related to a given identifier**.

```
{
    <field>: <value>,
    <field>: <value>,
    "name": "Akshay",
    "title": "SDE",
    "age": 22
}

// In this example, there is a field name, and the value is Akshay.
// We will look closer at documents and their structures once we get our hands on an actual data set.
// But for now, this is a simple example of a document.
```

-   A **collection** would contain many documents similar to this one.
-   And a **database** would contain multiple collections.

In this lecture, we defined what document and collection mean in MongoDB.

In the next chapters, we will start querying collections for documents and interact with the database in general.

# Lecture: What is MongoDB Atlas?

-   Atlas is a cloud database with MongoDB at its core.
-   It has many different tools and services available within it, all of which use MongoDB database for data storage and retrieval.
-   Atlas users can deploy clusters which are groups of servers that store the data. These servers are configured in what is called as a **replica set** which is a set of a few connected MongoDB instances that store the same data.
-   An **instance** is a single machine locally or in the cloud, running a certain software - in this case, it is the MongoDB database which is run in the cloud.
-   This setup ensures that if something happens to one of the machines in the replica set, the data will remain intact and availabe to be used by the application by the remaining working members of the replica set.
-   So everytime you make changes to a document or a collection, redundant copies of that data are stored within the replica set.
