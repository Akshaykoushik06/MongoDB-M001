# Chapter 3: Creating and manipulating documents.

# Lecture: Inserting New Documents - ObjectId

-   We now know how to search existing documents. It would be great to also know how to add new documents to collections. Let's find out how to do just that.
-   We can do it using the Atlas GUI. Using the `Insert Document` button at the top right to do so.
-   You can do it either in JSON view or list view. You can notice that no matter what view we select, the `_id` field is already populated. This is very important to know and let's have a look at this one.
-   Every mongo db document has one thing in common and that is that every mongo db document must have an `_id` field and that field must have a unique value from the rest of the documents in the collection.
-   The rest of the fields and values are not enforced by default in any way. So in theory, you can have a collection where all the documents have the same exact `field:value` pairs in each document with the only difference between them being the `_id` field. This won't throw an error or cause any issues. But, it's definately not a very good usage of memory space in your database.
-   Likewise, you can have a collection where each document is so distinctly different from the other that they don't have the same shape or any field names in common. This also won't throw an error or cause any issues in the database. But this seems like a very odd way to organise data and a rather a good way to **disorganise** data instead.
-   While nothing in mongo db inherently prevents you from doing either of those things, neither is a good practice nor a good way to organise data and get best value from using mongo db.
-   To learn about best practices in how to organise data within mongo db, go through the course **M320: Data Modeling**.
-   We will also cover some basics of data modeling in a later chapter. For now, let's return to inserting a new document into our collection.
-   When we insert a new document, mongo db populates the `_id` field with a value that is of type `ObjectId()`. It need not be of type `ObjectId()`. It is just that is created by default to ensure unique values for each document. If you already know of unique values that you can use for each document, you can use them in the `_id` value instead.
-   If you want to read more about the ObjectId data type, and the ObjectId() function, which generates ObjectId values, check out [this](https://docs.mongodb.com/manual/reference/method/ObjectId/#objectid) documentation page.
-   This was about inserting a new document through GUI. Now let's do it through mongo shell (CLI).

# Lecture: Inserting new documents - insert() and errors

-   How can we insert new documents into a collection using the Mongo shell?
-   Now that we learned about the ObjectId and its role, we can backtrack a little bit and talk about a scenario where we insert a lot of documents at a time, such as `mongoimport` command.
-   When I try to insert a collection to a database that already contains the same documents, we get a lot of the same error. If we read what the error says, things may become a little clearer.
-   The error says `duplicate key error` followed by a namespace for the collection and the ID value of a document that we attempted to insert.
-   The insertion did not succeed because a document with this exact ID value already exists. This is why we need to add the `drop` while using any of the import commands option.
-   This way we remove the whole collection before inserting it back, thus eliminating the duplicate key issue.
-   Of course, importing entire collections is not the only way to insert documents using the Mongo shell. Another way is to use the insert command.
-   As my first try, I want to see if I can replicate the duplicate ID error.

    -   Step one, connect to the Atlas cluster.
    -   Step two, navigate to the database that we need.
    -   Step three, get a random document from a collection using `findOne()`.

        > This is our first time using findOne. This function is good to have when you're looking for some document that matches a certain query, or to get a general idea about the shape of documents in a collection.
        > This is a rare case, because most of the time, when a collection is queried, the goal is to get all of the documents that match the query, not just one.
        > Plus, when you get just one document, you don't know if this is the only document that matches the query or if there are others.
        > But this function is excellent for the purpose of this example, which is why you're seeing it now.

    -   Step four, copy this random document.
    -   Finally, let's try to insert it into the collection. See if we get a duplicate key error.

    -   It worked. We have a duplicate key error.

-   The response tells us that the number of inserted documents after this command was zero. And there was a write error, meaning that writing this document to the collection did not succeed.
-   Great. This means that we cannot insert documents with identical `_id` values into the collection.
-   What happens if we remove the `_id` field and try to insert this document again?

    -   I just hit the up arrow on my keyboard to get the previously issued command.
    -   Then, I navigate all the way to the \_id field and its value, delete this part of the document that I'm trying to insert, and hit Enter.
    -   This worked. And the response from the database is that the number of inserted documents is one, which is exactly how many documents we tried to insert.

-   Let's investigate. I'm going to create a find query looking for all inspections with this ID and certificate number, just to be safe.
-   Let's not forget to ask for the output to be pretty. The two documents look identical, except for the `_id` value.
-   But we didn't add the `_id` field when we were inserting the document, you might say.
-   That is correct. We did not.
-   However, it got added automatically upon insertion, and it got assigned a generated `ObjectId` value.

### Summary

-   MongoDB allows you to have documents identical in their content, as long as the `_id` values are different between those documents.
-   MongoDB also allows you to prevent inserting identical documents if you choose to manage your database that way.
-   To place restrictions on the document content that is being inserted, you can check out the MongoDB schema validation functionality, which is unfortunately not part of this course.
-   The main idea behind the way that insertion and document structure, in general, work in MongoDB, is that there is flexibility in how you choose to use it.
-   And that's the beauty of it.

# Lecture: Inserting new documents - insert() order

-   We looked at inserting a document successfully and with a duplicate key error. But we only inserted one document at a time. Can we insert multiple documents at a time and how will that work?
-   Here is one example to do so `db.<collection name>.insert([{"test":1}, {"test":2}, {"test":3}])`.
-   I am using the `insert` command and using the array of documents. Each document has one field `test`. You can add multiple fields as well.
-   Now if you run the above command again, we do not see the duplicate ID error cuz we did not prove the `_id` and mongo db generates them. Each of the 6 inserted documents have the unique `_id` values.
-   Now lets try the following command `db.<collection name>.insert([{"_id": 1, "test":1}, {"_id": 1, "test":2}, {"_id": 3, "test":3}])`.
-   This command fails. Well, partially :p
-   One document is still inserted and which was it?
-   If we print the documents, we see that only the 1st one is inserted - the one with `"test": 1`.
-   There were 2 documents with unique `_id` and why did one of them got inserted?
-   If you closely look at the error, you can see that the problem document was `"test": 2` and it knows nothing about `"test": 3`.
-   This is because when multiple documents are inserted, the default behaviour is to insert them in the order in which they are listed in the array. But we can change it.
-   Add the ordered command in the insert command as shown `db.<collection name>.insert([{"_id": 1, "test":1}, {"_id": 1, "test":2}, {"_id": 3, "test":3}], {"ordered": false})`.
-   Now, all documents with the unique `_id` values will be inserted and the documents with duplicate `_id` values will produce errors.
-   To conclude we can say that, when the default ordered insert happens, the moment there is a duplicate key error, the insert operation holds and even if the rest of the documents have unique IDs, they won't get a chance to be inserted. If the insert is unordered, then every document that has a unique `_id` value gets added to the collection.

## Note

-   If you misspell a collection name, say you gave it as `inspection` when the actual name of the collection was `inspections`, mongo db does not complain about this. This behaviour is by design.
-   Mongo db wants it to be simple to create collections and hence if you run an `insert` command against a new collection name that does not exist in the current database, you are basically creating a new collection and are inserting the data in it.
-   The same goes for a database as well. But there is a catch. If you misspell a database name or give a new database name, mongo db will **NOT** create the new database. It just switches to the new one. And if you insert a document in a collection, then the database will be created.
-   So be careful while specifying the database and collection names.

# Lecture: Updating documents - Data explorer (GUI)

-   Things change, and quite often, we need to update the data that we have in our database. Let's see what tools we have to perform an update.
-   For this lesson, we'll use the grades collection from the sample training data set.
-   The document and this collection seem pretty small compared to what we saw before. But here, we see an example of a different field type.
-   The `inspections` collection introduced us to the concept of sub-documents, where the address field was an object, aka, a document, with fields and values for the street name, city, and so on.
-   In the case of the grades collection, we have an array field. Let's expand it and see what that is about.
-   Looks like an array of objects, is that possible?
-   Why not? MongoDB has a flexible document model, which means that you can store your data however it makes sense for your application.
-   An array of objects is a common way to store data in certain applications. Let's expand further and see what these objects look like.
-   It appears that student zero took a class with the unique ID of 339, and had four assessments in that class, two homework assignments, one quiz, and one exam.
-   We're going to pretend to be a teacher of class 339. And we know for a fact that a student with ID one five one has done some extra credit work at the end of the semester.
-   Let's update that student's record for the class.
-   When we hover over the document that matches this query, there are a number of actions that we can choose to do, edit, copy, clone, and delete.
-   To update one document, we select the edit button that looks like a pencil. You may recognize this view from when you inserted our first document into the collection.
-   Here, we want to update the scores field by adding another score.
-   Since this is an array field that contains sub-documents, aka, object types, when selecting to add a new element, we get a choice of where to nest it.
-   In this case, we want to add an object to an array, so we select that. And change the value type from the default string to object. We want to follow the structure of other objects in this array for our own consistency and sanity.
-   So the first field should be type and the second should be score. The type is extra credit and the score is 100.
-   Looking at the other scores that the student has, it's a good thing that they submitted some extra credit :p
-   We must remember to change the score value type from string to double. This way, if we plan on doing some calculations down the line, we wouldn't have to convert any values from string to numeric types.
-   Finally, we can hit update.
-   Voila! Now the student has a chance of passing class ID 339.

# Lecture: Updating documents using the mongo shell

-   Time to switch to the Mongo shell and see what kind of document updates we can perform using the **MongoDB Query Language**, also known as **MQL**.
-   In this lesson, we'll talk about two operations used to update documents in the Mongo shell, `updateOne` and `updateMany`.
-   Earlier, we saw an example of using _findOne_, which returns the first document that happens to match the given query.
-   This is different from _find_, which returns a _cursor_ with all the documents that correspond to the given query.
-   Likewise, with `updateOne`, if there are multiple documents that match a given criteria, only one of them will be updated, whichever one this operation finds first. Whereas using `updateMany` will update all documents that match a given query.
-   With that said, let's see both commands in action.
-   As always, I must connect to my Atlas cluster. For this lesson, we'll use the `sample_training zips` collection.
-   This data is a few years old, which means that the population count is no longer accurate.
-   It's safe to assume that the population of most cities in this collection has increased by at least 10 people.
-   In most cases, the population increased by much, much more, sometimes even doubling.
-   But we'll go with the safe assumption that at least 10 more people were born or moved to every city in the past two years.
-   This data set is based on US cities, which makes it uniquely excellent for something like updateMany, and here's why.
-   If we look up a document using its zip value, it will yield one document.
-   Remember, zip codes are like postal codes for the rest of the world. In this case, we're looking at the city of Hudson in New York state.
-   However, if we look for all documents where the city is Hudson, regardless of the state or zip code, we get many more entries. 16 to be precise.
-   Let's update all of them sing the command `db.zips.updateMany({"city": "HUDSON"}, {"$inc": {"pop": 10}})`.
-   The first argument here specifies the query which will determine which documents will be updated.
-   The second argument specifies the update that needs to happen.
-   We're using `$inc`, which is an MQL update operator. It increments the value of a specified field by the given amount.
-   So in this case, we're looking to increment the "pop" field by 10 in every document which lists Hudson as the city.
-   When the operation is complete, we get a summary of whether it succeeded. 16 documents matched our query, and 16 were updated.
-   `$inc` syntax allows us to update multiple fields at the same time by listing the fields and their increment value separated by a comma.
-   `$inc` is not the only update operator in the MongoDB query language.
-   Let's use updateOne and see if we can try out a different update operator with it.
-   After thorough googling, I found out that the city of Hudson in New York state currently has a population of 17,630 people. So we can update this document to be more accurate.
-   If we try to use the `$inc` operator, then we'll have to find the current population value, then subtract that from the actual population, and then increment by that amount.
-   That sounds exhausting.
-   Instead, we'll use the `$set` operator.
-   When `$set` is used, it updates the value of the given field with a specified value.
-   Check out what happens if we make a typo in the field name.
-   No errors, but we don't have a population field, you might say. Well, at this point, this behavior should almost be expected.
-   Just like the implicit creation of databases in a collection, there is a precedent for implicit creation of fields.
-   The idea is that if the field doesn't already exist and you're issuing this update, it means that you want to add this field to the document. And so the field gets added.
-   Now this document has the `pop` and `population` field and both have the same value.
-   You can also use `$set` to set the value for multiple fields, much in the same way that the `$inc` operator does it.
-   When we updated the student record via the Data Explorer UI, we updated an array field.
-   It would be great to know how to do that via the Mongo shell as well.
-   To add an element to an array field, one of the options is to use the `$push` operator, which has the following syntax:
    -   `{ $push: { <field1>: <value1>, ...} }`
-   Just like with the `$set` operator, if the field that you specify doesn't exist in the document, then `$push` will add an array field to the document with a specified value.
-   Let's see the `$push` operator in action.
-   First, we should look at the document that we updated via the UI. There, we see the extra credit score that we added earlier.
-   Let's give some extra credit to another student in that class. This student looks like they could use a little bit of an extra credit.
-   We'll do the same thing that we did in Data Explorer and add an extra credit score to the `scores` array.
    `db.grades.updateOne({"student_id": 250, "class_id": 339}, {"$push": {"scores": {"type": "extra credit", "score": 100}}})`. This modifies the `scores` array by adding another element to it.
-   In this case, the element added is a document with two `field:value` pairs, type: extra credit and score: 100.
-   And there it is. The student got 100 for their extra credit. Hopefully that will improve their class average.
-   We will cover other update operators in the following lessons, but we won't cover all of them.
-   To learn more about all available update operators in MQL, visit [documentation](https://docs.mongodb.com/manual/reference/operator/update/#id1) page.

# Lecture: Deleting documents and collections

-   Now that we've added a new collection & some new documents and, updated a few documents along the way, it's time learn how to delete documents and collections in mongo db.
-   If we are looking to perform such operations through the data explorer, the handy **garbage can** button will do the trick for most cases. We can delete a database, a collection or a document by clicking on the garbage can sign.
-   However, if we are looking to do it through the mongo shell, our options in how things get deleted vary a little more.
-   Firstly we have the `deleteOne()` and `deleteMany()` options which work in a similar way to `updateOne()` and `updateMany()` except in this case, the only update that happens is that the document gets removed from the database.
-   With that beig said, I want to emphasize that the only times when `deleteOne()` is a good approach to deleting documents is when we are querying by the `_id` value, thus guranteeing that this is the only document matching this query. Otherwise, we are running the risk of deleting one of the few or even one of the many documents that we needed to delete.
-   We can't consistenty rely on `findOne()`, `updateOne()` & `deleteOne()` to always return the same document when multiple fit the search query unless we are querying by the `_id` value.
-   Now, let's connect to the Atlas cluster and delete some stuff.
-   First it would be good to delete the test documents that we created when working with the `inspections` collection.
-   If we run the find query, we can find the `"test": 1` and `"test": 3` documents. There are 6 documents that we want to remove.
-   In this case we have to use `deleteMany()` since we just did a search and found that there are multiple documents that match the search criteria.
-   First we delete the ones that match the `"test": 1` criteria by running `db.inspections.deleteMany({"test": 1})`.
-   We can do the same with the `"test": 3` criteria.
-   To delete a **collection** we need to use `drop` which is a command that we used earlier when importing new collections. We can drop a collection by using `db.<collection name>.drop()`.
-   To summarise:
    -   To delete a collection, you can use the `drop()` function.
    -   Individual documents can be deleted using the `deleteOne()` & `deleteMany()` commands.
    -   And finally, after any of these commands are used, this data is **GONE** for good. Please be mindful and 100% sure when issuing these commands.

### Note

-   Does removing all collections in a database also remove the database?
-   The answer is YES. When all collections are dropped from a database, the database no longer appears in the list of databases when you run show dbs.
