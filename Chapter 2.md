# Lecture: How does MongoDB store data?

-   We already discussed that Mongo DB uses documents to store data. Now, let's look at the documents a bit closer.
-   How are documents represented in memory? and what is the correct syntax for these objects that we call documents?
-   When you view or update documents in the mongo shell, you are working in **JSON** which stands for **JavaScript Standard Object Notation**.
-   For a document to comply with the JSON format, you need to:
    -   Start and end with curly braces `{}`
    -   Separate each **key** and **value** with a colon `:`
    -   Separate each **key:value** pair with a comma `,`
    -   Include quotation marks around the keys `""` - keys are also known as fields in mongo db
-   | Pros of JSON | Cons of JSON    |
    | ------------ | --------------- |
    | Friendly     | Text-based      |
    | Readable     | Space-consuming |
    | Familiar     | Limited         |
-   Explanation for cons:
    -   JSON is text based format and text parsing is very slow.
    -   JSON's readable format is far from space efficient - another database concern.
    -   Json supports only limited number of data types.
-   So, mongo db decided to address these drawbacks. If you look at your data the way it is stored in memory, you will see it in binary format.
-   This format is called **BSON** which stands for **Binary JSON**.
-   Why BSON? - well, BSON was invented to bridge the gap - a binary representation to store data in JSON format. It is optimized for:
    -   Speed
    -   Space
    -   Flexibility
-   The goal was to achieve **High performance** and **General-purpose focus**.
-   Since its initial formulation, BSON has been extended to add some optional, non-JSON native data-types like dates and binary data.
-   The additional data-types allow for easier communication between the database and applications that can be written in a variety of languages - each of which uses its own variety of data-types.
-   Mongo DB stores data in BSON internally and over the network. But that doesn't mean you can think of mongo db as a JSON database.
-   Anything you can represent in JSON can be natively store in mongo db just as easily in JSON.
-   BSON provides additional features - speed, space and flexibility which is important to keep in mind when working with mongo db.

# Lecture: Importing & Exporting data

-   Now that we have spoken about how daat is stored, we can start working with it.
-   As a first step, let's learn how to import and export data. We've alerady loaded some data into Atlas cluster, say we want to export our data to say our local machine or a different system entirely. Is that possible? Absolutely!
-   But first, let's decide what format we are going to use.
-   As mentioned in the above lecture, data in mongo db is stored in BSON, but is viewed in JSON.
-   BSON is great but isn't really human-readable. If I'm looking to just store this data and maybe transfer it to the different system or cluster, my best bet would be to export in BSON. It's lighter and faster.
-   However, if I plan on viewing this data and reading through it locally after I export it, then a human-readable JSON is a better choice.
-   In this lecture, we will look at 4 commands:
    -   2 that have to do with importing data and exporting data in JSON. `mongoimport` and `mongoexport`.
    -   2 that have to do with importing and exporting data in BSON. `mongorestore` and `mongodump`.
-   Once the choice of format is made, we can proceed to export the data.
-   ```
    mongodump --uri "<Atlas Cluster URI>"

    // exports data in BSON format
    ```

-   ```
    mongoexport --uri "<Atlas Cluster URI>"
                --collection=<collection name>
                --out=<filename>.json

    // exports data in JSON
    ```

## Let's look at the above commands in action.

### Exporting

-   You may recognize the URI string from when we first connected to our atlas cluster. URI stands for Uniform Resource Identifier.
-   In mongo db's case we are using an srv string to connect to the atlas cluster which is a more specific connection string format that is used to establish a secure connection between your application and your mongo db instance. `mongodb+srv://user:password@clusterURI.mongodb.net/database`.
-   Ths string has been updated from the last time you saw it. Username and password are now part of the string. You can use the URI connection string in this format in the future when connecting to your atlas cluster.
-   Then, an `@` sign directs us to our unique atlas cluster. And finally, we must specify which database we are looking to export - it is included at the end of the URI string.
-   Finally, the command looks like this (example) `mongodump --uri "mongodb+srv://m001-student:m001-mongodb-basics@sandbox.******.mongodb.net/sample_supplies"`.
-   The above command generates a folder called `dump` and inside that you can see another folder named `sample_supplies`.
-   There you can find the bson file. If you try to open it, you cannot understand cuz its in binary format.
-   Time to try the export where we have the luxury to choose a different format for the data.
-   `mongoexport --uri="mongodb+srv://m001-student-mongodb-basics@sandbox.******.mongodb.net/samples_supplies" --collection=sales --out=sales.json`.
-   As you can see, we are still needed to authenticate (specify the username & password) and specify the database of interest.
-   But this time we get to choose a specific collection and the file name in which this collection will be stored.
-   In this example, I'm using `sample_supplies` database, the `sales` collection and using the filename `sales.json`.
-   It creates a file with name `sales.json` and if you open the file, you can view the data in the human readable format.

### Importing

-   Importing data works just like exporting it does. You need to have either a BSON or JSON file that you are looking to import and depending on what format is available, you can use `mongorestore` or `mongoimport`.
-   `mongorestore` loads data from a binary database dump - created by `mongodump`. So using the 2 in conjunction can be a backup strategy if you want to do manual backup and restore at some point.
-   Let's import the binary data that we imported in the above step using the following command `mongostore --uri "mongodb+srv://m001-student:m001-mongodb-basics@sandbox.*******.mongodb.net/sample_supplies" --drop dump`.
-   In this case, we are still using the URI string that contains the username, password and our target database (in this case, it is `sample_supplies`).
-   Notice the flag `drop` which removes the existing database in that location. This helps prevent getting a lots of errors about duplicate entries in the database. We have successfully created a database from the BSON dump.
-   Let's import from JSON file (created in the above section) using the command `mongoimport --uri="mongodb+srv://m001-student:m001-mongodb-basics@sandbox.*********.mongodb.net/sample_supplies" --drop sales.json`.
-   This works similar to the `mongorestore` command where we specify the username, password & target database.
-   The dataset can be in JSON or other supported data formats such as CSV.
-   You can have additional options with this command where you specify the name of the collection that will contain this data. If you don't specify, the collection will take on the name of the file as you saw in the previous example.
-   The table concludes it all and you can use the URI string as the connection string:
    | Operation | JSON |BSON |
    | ------------ | --------------- | --------------- |
    | import | mongoimport |mongorestore |
    | export | mongoexport |mongodump |

# Lecture: Find Command

-   Let's query the database from the CLI.
