# Chapter 4: Advanced CRUD operations.

# Lecture: Query Operators - Comparison

-   Its time to upgrade our MQL skills and add some complexity to our queries. Let's start by introducing comparison operators to our toolbox.
-   We already used MQL operators in the previous lessons where we saw examples of:
    -   Update operators - `$set, $inc, $unset` - these enable us to modify data in the database
    -   Query operatirs provide additional way to locate data within the database. What query operators have in common with all kinds of operators is the dollar sign `$` that precedes the operator.
-   The dollar sign `$` is used for multiple things in mongo db: - Operators - Ex: `$inc` - Aggregation pipeline stages - Accessing field values
    > We will talk about aggregation basics later in the course but if you want to learn more on aggregation pipeline or use of variables when querying the mongo db, checkout the Aggregation pipeline course from Mongo DB University.
-   What do we have at our disposal and how do they work? Below we have some examples:
    -   `$eq` -> Equal to
    -   `$ne` -> Not equal to
    -   The above 2 are used to see if the specified values equal to or do not equal to each other.
    -   `$gt` -> Greater than
    -   `$lt` -> Less than
    -   `$gte` -> Greater than or equal to
    -   `$lte` -> Less than or equal to
    -   The above four are also similar to equal operator
-   All use the same syntax `{ <field>: { <operator>: <value> } }`
-   Let's see some of them in action and get a better understanding of how to use them.
-   Using the sample dataset, we have `trips` collection in `sample_training` database. This collection contains the data about. It s teh dataset of bike rental company in New York city. It contains a field called `tripduration` which tells how long the trip was (in secs).
-   To see if any trip took a minute or less, we can use the GUI and give the following expression in the filter `{"tripduration": {"$lte": 60}}`.
-   Let's add another condition to see if a customer is a subscriber or not by using the command `{"tripduration": {"$lte": 60}, "usertype": {"$ne": "Subscriber"}}`.
-   In CLI, the same commands would look like: `db.trips.find({"tripduration": {"$lte": 60}, "usertype": {"$ne": "Subscriber"}})`.
-   Using the `$eq` operator, the command would look like: `db.trips.find({"tripduration": {"$lte": 60}, "usertype": {"$eq": "Subscriber"}})`.
-   Notice that the `$eq` operator is implicit in the situations like these and hence the following query is equivalent to the previous one using `$eq`: `db.trips.find({"tripduration": {"$lte": 60}, "usertype": "Subscriber"})`
-   They both return the same result and the only differnce is that we omitted the equals operator.
-   To summarise:
    -   Query operators provide additional ways to locate data within the database.
    -   Comparison operators specifically allow us to find data within a certain range.
    -   The syntax for comparison operator is `{ <field>: { <operator>: <value> } }`.
    -   When comparison operator is not provided, `$eq` is used as the default operator.

# Lecture: Query Operators - Logic

-   As we have discussed comparison operators, let's look at the logic operators.
-   In MQL we have the standard set of 4 logical operators and you have guessed them right! :p:
    -   `$and` - matches **all** of the specified query clauses
    -   `$or` - **atleast one** of the query clauses is matched
    -   `$nor` - **fail to match** both given clauses
    -   `$not` - **negates** the query requirement and returns all the documents that do not match the query
-   `$and`, `$or`, `$nor` have similar syntax - `{<operator> : [{statement1}, {statement2},....]}`
-   `$not` is simple and just negates what is infront of it - `{$not: {statement}}`
-   `$and` is used as the default operator when an operator is not specified
    -   For example, `{sector: "Mobile", result: "Warning"}` is identical to `{"$and": [{sector: "Mobile"}, {result: "Warning"}]}`
    -   Another example can be seen when we apply multiple conditions to the same field. Assume we have data of students and we have to find sudents whose IDs are > 25 and < 100 in the same collection.
    -   We can do the above like: `{"$and": [{"student_id": {"$gt": 25}}, {"student_id": {"$lt": 100}}]}`. We can simplify it significantly - `{"student_id": {"$gt": 25, "$lt": 100}}`.

# Lecture: Expressive Query Operator

-   Let's take it up a notch and look at `$expr`, the expressive query operator.
-   The versatility of this operator is already clear from its name, `$expr`. It's expressive, meaning it can do more than one simple operation.
-   It allows the use of aggregation expressions within the query language, and it uses this syntax.

    -   `{ $expr: { <expression> } }`

-   We haven't yet learned what aggregation expressions are so it might seem premature to talk about this operator. However, `$expr` also allows us to use variables and conditional statements.
-   So let's get started with that and see how it works. In the later lessons, we will also cover aggregation expressions so that you can utilize the power of `$expr` to its fullest.
-   When we learned about comparison operators, we were comparing a field's value to some number. But can we compare fields within the same document to each other?
-   It sounds crazy, I know. But it's not such a rare occurrence.
-   Let's say I'm trying to learn more about city bike users in New York City, and I want to know how many of them return the bicycle to the same station from which they rented it out.
-   `$expr` allows me to compare the `start station ID` value directly with the `end station ID` value within the same document, without specifying what those values should equal to on their own.
-   And this is how we do it - `{"$expr": {"$eq": ["$start station id", "$end station id"]}}`
-   Fantastic. I get results. If I scroll through a little bit, I see that these documents seem to match my query.
-   But what are these dollar signs everywhere? Are there `start station ID` and `end station ID` operators now too? Great question.
-   The dollar sign symbol has a lot of wonderful superpowers in MQL. One of them is to denote when you're using an operator.
-   Another one is to signify that you're looking at the value of that field, rather than just the field name itself.

```
{
    "start station id": 439,
    "start station name": "E 4th Street and 2nd Avenue"
}
```

-   In this example, given a document, when we issue an expression like this, the `$start station ID` means the value 439.
-   And if we were to use `$start station name`, that would mean E 4th Street and 2nd Avenue.
-   If we don't use the dollar sign in this case, we have to look for a specific field value in all documents, rather than compare a value that varies from document to document to another value that varies from document to document.
-   If we were to replace the ID with name - `{"$expr": {"$eq": ["$start station name", "$end station name"]}}` we should get the same exact results.
-   Another question that I have for this data set is how many of these people rented the bikes out for more than a couple of minutes?
-   For that I'll add another condition to this expression and move to the show to play around with this data more.
-   One thing to know before we switch from the Atlas interface, though, is that this particular collection contains 10,000 documents. So how many of these were just rides around the area that lasted longer than a few minutes, and returned to the starting point?
-   We're already connected to the Atlas cluster. If you're not, make sure to connect when you're trying this on your own. And we're going to be using the sample training database.
-   This is the initial query that we issued in the Atlas UI in the Data Explorer - `{"$expr": {"$eq": ["$start station id", "$end station id"]}}`
-   And we want to find out how many trips started and ended at the same station.
-   So there's a count at the end, and the final result is 360 trips, which is only about 3% of the total number of 10,000 trips.
-   Looks like New York City is living up to its reputation, and most people are, in fact, rushing to some other destination :p
-   So how many of these were just rides around the area that lasted longer than a few minutes, and returned to the starting point? For that, I issue this query - `{"$expr": {"$and": [{"$gt": ["$tripduration", 1200]}, {"$eq": ["$end station id", "$start station id"]}]}}`
-   And here, I am adding another condition where I want the trip duration to be greater than 1,200 seconds-- 173 is the result.
-   OK, let's break this query down to take a closer look at the syntax and what this query really does.
-   First, we added a greater than operator to our equals operator under the same `"$and"` umbrella.
-   Something here is still a bit off from how we learned to use comparison operators.
-   In MQL, comparison operator syntax uses the field name first and the comparison operator applied later.
    -   Like this `{ <field>: { <operator>: <value> } }`
-   This syntax, however, is using the aggregation operator instead.
    -   Like this `{ <operator>: { <field>, <value> } }`
-   It looks the same way, but the syntax is slightly different.
-   We will cover the aggregation pipeline and operators, I promise, later in this course. So stay with me.
-   This is it for the expressive operator and to conclude it:
    -   It allows for more complex queries and for comparing fields within a document.
    -   We also learned about another way that the dollar sign is used in the MongoDB query language, and
    -   Even got a peek at how to use comparison operators via the aggregation pipeline before we even got to learn about the aggregation pipeline.

# Lecture: Array Operators

-   So far we queried documents by fields that have string, numeric, and Boolean values. It's time that we look at more complex elements and see what MQL has to offer to query array fields.
-   We already learned one array operator, `$push`, which allows us to add an element to an array or turn a field into an array field if it was previously a different type of value.
-   Let's see what happens when we query for a value in an array field using the methods that we already learned for other types of fields.
-   I'm looking to start traveling, and I heard a lot about this Airbnb place. The `sample_airbnb` database contains an excellent example of array fields, plus it can help me figure out where and how I want to travel.
-   Each Airbnb listing has a field with an array of available amenities. Let's say that not having shampoo in the Airbnb is a deal breaker for me. So I query for `{"amenities":"Shampoo"}` to isolate all entries that have shampoo in them.
-   Notice that the resulting documents match this criterion in such a way that shampoo is **one** of the array elements, even though the query itself doesn't specify that `"amenties"` has to be an array.
-   So this looks like we're querying a field that could just have a string as a value. How can we differentiate and look for documents where amenities is specifically an array field?
-   Let's try a different approach. What if I place ["shampoo"] in square brackets - `{"amenities": ["Shampoo"]}`, making it an array.
-   Will this work? Nope.
-   You might think that the result will be documents where the amenities field is an array field. However, now MongoDB is looking for a document where the value of the array field is a **single element**, `shampoo`. Because without any additional operators, it will look for an exact match.
-   Now the question is, does the order of elements in an array field query matter?
-   To test this out, we'll just copy an array field from one of the documents and do a search that way.
-   As a result, we can either get all documents that contain the same amenities, regardless of their order in the array field, or all the documents that have the amenities listed in this exact order.
-   Hmm. The result is a single document with a listing of an apartment in Montreal, Canada.
-   To fully test which scenario we're observing, let's swap the order of the first two elements in this array.
-   If the order doesn't matter, we'll get the same document back. But if the order does matter, we'll get a different document, or nothing if no documents have that exact array in them.
-   And that's a zero. All right. It appears that the order does matter.
-   Of course, the order matters. It's an array. That's the whole point of them, you might say. Well, I wish you'd told me that earlier, and I didn't have to go through all this trouble testing things out.
-   So the question now is, how do we find all documents that contain more than one amenity without caring about the order of array elements?
-   MQL has a great operator for that called `"$all"`.
-   This query should return all documents that have at least these elements in the amenities field. Most matches will have these, and more.
-   Something tells me that the more amenities are listed, the more expensive the place is going to be. We're trying to travel on a budget, so we can either make our query limit the results by price, or we can learn more about array operators and limit the results by array length.
-   To limit the result cursor by restricting their ray length, add the `$size` operator to your query, like so.
-   This will only return documents that list exactly 20 amenities in this field and contain the amenities that we're looking for.
-   You can also just query by array length using the size operator as your only query condition. But we do not need it in this scenario.
-   Finally, I would like to make an informed choice about this journey. But the documents in this collection are just too big to quickly compare and choose my travel destination.
-   For that, let's switch to the Mongo shell and learn about **projection**.
-   As always, I'm already connected to the cluster. If you are not, make sure to be connected to it when you're following along with this lesson.
-   And I want to specify which database I'll be using for this exercise.
-   If we just issue this query as is - `db.listingsAndReviews.find({"amenities": {"$size": 20, "$all": ["Internet", "Wifi", "Kitchen", "Heating", "Family/kid friendly", "Washer", "Dryer", "Essentials", "Shampoo", "Hangers", "Hair Dryer", "Iron", "Laptop friendly workspace"]}})`, reading the results in the shell will be as cumbersome as it is reading them in the Atlas UI.
-   Because there's just too many fields and too much information. To mitigate this, we can add a projection to our Find query.
-   A projection allows us to decide which document fields will be part of the resulting cursor. We will cover projection in a future lesson.
-   For now, let's summarize what we learned about querying arrays in MongoDB.
    -   The `$size` array operator will return all documents where the specified array field is exactly the given length.
        -   `{ <array field> : { "$size": <number> } }`
    -   The `$all` array operator will return a cursor with all documents in which the specified array field contains all the given elements, regardless of their order in the array.
        -   `{ <array field> : { "$all": <array> } }`
    -   When querying an array field with an **array** match, MongoDB will look for an **exact** array match, unless specified otherwise.
    -   When querying an array field with a **single** element, MongoDB will return **all** documents where the specified array field contains this given element.

# Lecture: Array Operators and Projection

-   In this lesson, we'll cover more array operators, and the second argument in the Find command called **projection**.
-   When we look at the sample Airbnb data set, we see documents with lots of fields that often don't fit on the screen.
-   To mitigate this, we can add a projection to our Find queries, and only look at fields that we're interested in at the moment.
-   Here's a query that looks for documents in the sample Airbnb database.
    -   `db.listingsAndReviews.find({"amenities": {"$size": 20, "$all": ["Internet", "Wifi", "Kitchen", "Heating", ...]}}, {"price": 1, "address": 1}).pretty()`
-   The first part of the `find` query describes the content that we're looking for, though we didn't write out the whole list on this slide.
-   The second is a projection, describing specifically which fields we're looking for. This way, the cursor doesn't have to include every single field in the result set.
-   At this point in my travel search, I want to know the `price` and `address` information only, so that is what I specify in my projection.
-   Let's issue this query and see what we get.
-   Now I can just look at the address and the price for that address for every single listing that matches my query. My initial theory about Airbnb listings was, if there is a shorter list of amenities present, then the booking will be cheaper.
-   It looks like my theory about the number of amenities correlating with the price was wildly off. The prices and the results set ranged from $45 in Barcelona, Spain, to $999 in Bronte, Australia.
-   I guess I'm going to have to do some other kind of search to find the perfect travel destination. Searching by price might be a better option, but we already know how to do that.
-   So let's learn more about projection, instead.
-   When using projection, you can specify which fields you do or do not want to see in the resulting cursor.
-   General syntax - `db.<collection name>.find( { <query> }, { <projection> } )`
-   Use `1` to specify the fields that you want to see, and `0` to specify the fields that you don't want to see.
-   You cannot mix zeros and ones in a single projection.
-   If you're using ones, then you'll only get the fields that you specified, plus the `_id` fields.
-   If you're using zeros, then you'll get all the fields except for the ones that you specifically excluded.
-   The only time when you can mix ones and zeros is when you're specifically asking to exclude the `_id` field, because it will be included by default otherwise.
-   So this is a valid projection - `db.<collection name>.find( { <query> }, { <field1>: 1, "_id": 0 } )`, because the 0 is used to explicitly exclude the default `_id` value, but this - `db.<collection name>.find( { <query> }, { <field1>: 1, <field2>: 0 } )` is not.
-   We get an error when running it. Plus it seems redundant to specify that a field shouldn't be included (by using 0 for field2 in the projection) when it is already not included (adding 1 to field1 implicitly means to exclude field2).

## Now let's look at some more advanced projection that is specific to array fields.

-   Before I started working at Mongo DB, I was a high school teacher teaching 160 to 190 students a day.
-   Going through student data can be a grueling task, unless, of course, you're good at querying data.
-   Say I'm looking for all students who took `class 431` and got an 85 or higher for any type of assessment.
-   First, let's look at the documents and the grades collection to remind ourselves what type of fields we will be querying. Here we see that the Scores field is an array of documents.
-   We don't care about the type of assessment, we only care about the score. How can we access elements in these sub documents of an array field?
-   There is a handy array operator, `$elemMatch`, which we will use for this query.
    -   `db.grades.find({"class_id":431}, {"scores":{"$elemMatch":{"score":{"$gt":85}}}})`
-   Here it is in the query itself. Let's see what result we get, and then see how this works.
-   The results shows all the documents that match the query, but we're not getting every field value for every document.
-   For some documents, we're not getting anything other than the `_id` value, since that is the default projection behavior.
-   For other documents, we're getting the `_id` value, and the element of the array that matches our `$elemMatch` condition, which is that the score field has a value of greater than 85, just like it says in the query.
-   Since array elements, in this case, are documents, we're getting the full document that matches the condition. So not only the score, but also the type of the evaluation.
-   Here `$elemMatch` is used in the projection part of the query, but it can also be used in the query part of the `find` command as well.
-   To try that out, let's find every student who received extra credit for any class.
-   So I'm looking for anyone who has, in the `scores` array, a field that is called `type` and the value for the type is `extra credit`. There is no projection in this query.
-   This student really needed extra credit, so it's a good thing that they got it for this class.
-   Since `$elemMatch` is being used in the query part, the result is the full document that matches the condition for at least one of the elements in the array, and that is to contain the field, `type` with the value, `extra credit`.

### Conclusion

-   In this lesson, we learned that we can use projection to specify the fields that we want to see in documents when the cursor returns what matches the query condition.
-   You can use ones and zeros to specify whether a field should or shouldn't be included, but you can not mix and match, including and excluding fields, with the exception of explicitly excluding the `_id` field, which is present by default.
-   We also learned about `$elemMatch`, an array operator that can be used both in query and projection part of the Find command.
-   `$elemMatch` matches documents that contain an array field with at least one element that matches all the specified query criteria, or projects only the array elements with at least one element that matches the specified criteria. Isn't that cool?

# Lecture: Array Operators and Sub-Documents

-   MongoDB has a flexible model for storing data, which means that developers get to decide how to best store their data.
-   So it's common to see sub-documents or arrays of documents stored in MongoDB. Let's learn more about querying those fields.
-   I'm already connected to my Atlas cluster, and I'm choosing to use the sample training database. In this lesson, we'll learn to query sub-documents and specific array elements.
-   We'll start with the trips collection.
-   Each document in the collection has two perfect fields for our purposes, the `start station location` and the `end station location`.
-   Each field contains a document, and each document contains an array.
-   First, we want to know how to get to the array field in those nested documents. For that, MQL uses something called dot-notation.
-   Let's look at it in action.
-   All documents in this collection match this query. Every document has a station location where there is a field `type`, with value `point`.
-   But we are using `findOne`, so we only get one document back.
-   The top level field of the document called `start station location` stores an object which is a sub document.

    -   ```
        {
            . . .
            . . .

            "start station location": {
                "type" : "Point",
                "coordinates": [
                    -73.457975,
                    40.8454684
                ]
            }

            . . .
            . . .
        }
        ```

-   This sub document has two fields, `type` and `coordinates`.
-   To get the value of either field, I can use dot-notation - `db.<collection name>.find({"start station location.type": "Point"})`
-   The field name from the sub document follows the top-level field separated by a dot or a period, and the whole thing is included in quotes.
-   You can think of it as a path to the field that you're looking for almost like a namespace for a collection - where you go from a database level to the collection level, using a dot to separate the two objects.
-   This notation can be used to go as deep in the document as needed. So if you have a field that has a document as a value, and that document has a field with another document as a value, you can still use dot notation to get the last atomic non-document value in that hierarchy.
-   Here's an example using the company's collection in our class `sample_training` data set.
-   Let's examine the documents in that data set for a moment.
-   The `relationships` array contains objects, and each object describes the `title`, current status with relation to the company title, and personal details, like first and last name.
-   All personal info is stored in a nested document in that array element.
-   This query - `db.companies.find({ "relationships.0.person.last_name": "Zuckerberg"}, {"name": 1})` looks at the first element in the relationships array using dot notation.
-   Array elements in most languages and data structures are enumerated starting from zero.
-   MongoDB is no different, so the first element is at position zero. And that document is the value of the `person` field. So we use dot notation for that, as well. Finally, we are only interested in Zuckerberg, so we search for his last name.
-   Because the documents in this collection are so big, we add a projection for the company name. Now, we're searching for anyone with the last name Zuckerberg in the first element of the relationships array.
-   Let's try it out in the shell using the following command:
    -   `db.companies.find({"relationships.0.person.last_name": "Zuckerberg"}, {"name":1})`
-   It looks like the result is just one company, and it is the expected Facebook.
-   The relationships array lists higher-level company executives. Let's see how many of them are CEOs whose first name is Mark and who are listed as the first relationship in this array for their company's entry.
-   To do this, we need to slightly modify this query - change the last name to first name, and change Zuckerberg to Mark.
-   And then, add another condition where the title in this element contains the string CEO.
-   For this, I can use a regex operator to specify the string that I'm trying to match.
    -   Finally, the query becomes - `db.companies.find({"relationships.0.person.first_name": "Mark", "relationships.0.title": {"$regex": "CEO"}}, {"name": 1})`
-   We run the query to find how many Mark's are CEOs and are the first to be listed in this array field.
-   Looks like we have 52 Mark's.
-   And when we take a peek at the first 20, using the `pretty` function, we see Facebook, Bitly, and a company called Are You Watching This? Which is a great name, in my opinion :p
-   Now that we have this new found wisdom on querying embedded documents and array elements, it would be a good time to put this knowledge together in a query that looks for all Marks who used to be in the senior company leadership array, a.k.a. the relationships array, but are no longer with the company.
-   This means that we will be searching through every array element in every document.
-   There is a field called `is_past`, which can tell us whether this person is still with the company. So if they left, the value should be `true`.
-   And the first name field value, under the person field in the array element, should equal to Mark - `{"person.first_name: Mark"}`
-   Thankfully, we have a handy `$elemMatch` operator which can look through every array element and match these conditions.
-   So our find command, in this case, is going to be much shorter than the previous two.
    -   ```
        db.companies.find({"relationships": {"$elemMatch":{"is_past": true, "person.first_name": "Mark"}}}, {"name":1})
        ```
-   Here, we say that we're looking for elements in the relationships array where there is `past_field` is `true`, and the `person.first_name` field is `Mark`.
-   We can also take a peek at the company names that match these criteria, or we can count them. All right 256 - lovely number.
-   Now that we've got through all the nested documents and giant array fields, it is time to summarize what we learned.
    -   To query an array field by a specific element location or to query an element in sub-documents, MQL uses dot-notation to specify the address of these elements in the doc.
    -   You can use dot-notation to go as deep into the nested document as you wish.
    -   To use dot-notation with arrays, specify the position of the element in the array.
