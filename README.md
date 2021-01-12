# MongoDB Quick Guide

## MongoDB

MongoDB is a NoSQL database that stores data as JSON-like documents. Documents store related information together and use the MongoDB query language (MQL) for access.

```js
/*
Basic fundamentals of mongodb(syntax,structure).
CRUD operations(Insert , Update/Save , remove , find)
Aggregation Commands(Aggregate,distinct,count,etc.)
Aggregation pipeline.
Indexing,Limit,skip,sort etc.
Projection.
Use of Embedded/Sub-document.
Regex search and text search.
*/
```

### Query

```SQL
show dbs # Show Database

use flights # use flights database

db.users.find().pretty()
```

## CURD

#### Create

```js
insertOne(data, options);

db.users.insertOne({ name: "john", age: 23 });
db.users.insertOne({ name: "bro", age: 24 });
```

```js
insertMany(data, options);

db.users.insertMany([
  {
    name: "john",
    age: 23,
  },
  {
    name: "john",
    age: 24,
  },
]); // insert Multiple documents at once

/* 
mongo data was generally insert in odered way
the data you are trying to insert already is in the database. And then an unordered insert with ordered
false can be your solution because you don't care about any documents that fail,
they already are in the database,
that's fine for you
and you can add everything which is not in there yet.
*/
db.users.insertMany([
  {
    _id_: "1",
    age: 23,
  },
  {
     _id_: "1",  // skips with error
    age: 23, 
  },
  {
     _id_: "2",
    age: 24,
  }
],{ordered:false}); // 
```
WriteConcern: Data should be stored and you can control the level of guarantee of that to happen with the writeConcern option

#### READ

```js
find(filter,options)

db.user.find()                         //  return all data as a batch 20 items
db.users.find({gender:"M"}).pretty()   //  return all documents where gender = M
db.users.find({age:{$gt:24}}).pretty() //  return all documents where age greater than 24
db.users.find().limit(2).pretty()      //  return 2 documents in pretty format

```

```js
findone(filter,options)

db.users.findOne({gender:"M"}) // return the 1st documents where gender = M

```
##### Example Data which to work 
```js
{
   "_id":{
      "$oid":"5ffd3d910f5027b35addd771"
   },
   "id":1,
   "url":"http://www.tvmaze.com/shows/1/under-the-dome",
   "name":"Under the Dome",
   "type":"Scripted",
   "language":"English",
   "genres":[
      "Drama",
      "Science-Fiction",
      "Thriller"
   ],
   "status":"Ended",
   "runtime":60,
   "premiered":"2013-06-24",
   "officialSite":"http://www.cbs.com/shows/under-the-dome/",
   "schedule":{
      "time":"22:00",
      "days":[
         "Thursday"
      ]
   },
   "rating":{
      "average":6.5
   },
   "weight":91,
   "network":{
      "id":2,
      "name":"CBS",
      "country":{
         "name":"United States",
         "code":"US",
         "timezone":"America/New_York"
      }
   },
   "webChannel":null,
   "externals":{
      "tvrage":25988,
      "thetvdb":264492,
      "imdb":"tt1553656"
   },
   "image":{
      "medium":"http://static.tvmaze.com/uploads/images/medium_portrait/0/1.jpg",
      "original":"http://static.tvmaze.com/uploads/images/original_untouched/0/1.jpg"
   },
   "summary":"<p><b>Under the Dome</b> is the story of a small town that is suddenly and inexplicably sealed off from the rest of the world by an enormous transparent dome. The town's inhabitants must deal with surviving the post-apocalyptic conditions while searching for answers about the dome, where it came from and if and when it will go away.</p>",
   "updated":1529612668,
   "_links":{
      "self":{
         "href":"http://api.tvmaze.com/shows/1"
      },
      "previousepisode":{
         "href":"http://api.tvmaze.com/episodes/185054"
      }
   }
}
```
#### Methods,Filter and Operators

##### Query Selectors & Projection Operators

##### Query Selectors
1. Comparison
2. Evaluation
3. Logical
4. Array
5. Element
6. Comments
7. Geospatial 

#### Comparison

**Name**|**Description**
:-----:|:-----:
$eq|Matches values that are equal to a specified value.
$gt|Matches values that are greater than a specified value.
$gte|Matches values that are greater than or equal to a specified value.
$in|Matches any of the values specified in an array.
$lt|Matches values that are less than a specified value.
$lte|Matches values that are less than or equal to a specified value.
$ne|Matches all values that are not equal to a specified value.
$nin|Matches none of the values specified in an array.

```js
db.movies.find({runtime:60}).pretty()
db.movies.find({runtime:{$eq: 60}).pretty() 
db.movies.find({runtime:{$ne: 60}).pretty()
db.movies.find({runtime:{$lt: 60}).pretty() 
db.movies.find({runtime:{$lte: 60}).pretty() 
db.movies.find({runtime:{$gt: 60}).pretty() 
db.movies.find({runtime:{$gte: 60}).pretty() 
// Embedded Documents and Arrays
db.movies.find({"rating.average":{$gt: 7}}).pretty() 
db.movies.find({genres:"Drama"}).pretty() // return the document which consist of Drama
db.movies.find({genres:["Drama"]}).pretty() // return only with Drama genres 

db.movies.find({runtime:{$in: [30,42]}}).pretty() 
db.movies.find({runtime:{$nin: [30,42]}}).pretty()

// Count
db.movies.find({runtime:{$lt: 60}).count() 

```
#### Logical 

**Name**|**Description**
:-----:|:-----:
$and|Joins query clauses with a logical AND returns all documents that match the conditions of both clauses.
$not|Inverts the effect of a query expression and returns documents that do not match the query expression.
$nor|Joins query clauses with a logical NOR returns all documents that fail to match both clauses.
$or|Joins query clauses with a logical OR returns all documents that match the conditions of either clause.

```js
db.movies.find({$or: [{"rating.average": {$lt:5}},{"rating.average": {$gt:9.3}}]}).pretty() // less than 5 and greater than 9.3
db.movies.find({$or: [{"rating.average": {$lt:5}},{"rating.average": {$gt:9.3}}]}).count() 
db.movies.find({$nor: [{"rating.average": {$lt:5}},{"rating.average": {$gt:9.3}}]}).pretty() // reverse of $or [not less than 5 and not greater than 9.3]
db.movies.find({$and: [{"rating.average": {$gt:9}},{genres:"Drama"}]}).pretty() //  works same next one
db.movies.find({"rating.average": {$gt:9}},{genres:"Drama"})..pretty()          // works same as above one by default mongo db use and operator if there are multiple conditions
// AND Logical Operators is useful there is same name in object 

db.movies.find({genres:"Drama",genres:"Horror"}) ---> it becomes as db.movies.find({genres:"Horror"}) and we get only genres= horror in such cases we can use AND
db.movies.find({$and :[{genres:"Drama"},{genres:"Horror"}]})
db.movies.find({runtime:{$not: {$eq:60}}}).pretty() 

```
#### Elements
**Name**|**Description**
:-----:|:-----:
$exists|Matches documents that have the specified field.
$type|Selects documents if a field is of the specified type.

```js
db.users.find({age: {$exists: true}}).pretty()
db.users.find({age: {$exists: true, $gte: 30}}).pretty()
db.users.find({age: {$exists: true, $ne: null}}).pretty()
db.users.find({age: {$type: ["number"]}}).pretty()
db.users.find({age: {$type: ["number","string"]}}).pretty()
```
#### Evaluation
**Name**|**Description**
:-----:|:-----:
$expr|Allows use of aggregation expressions within the query language.
$jsonSchema|Validate documents against the given JSON Schema.
$mod|Performs a modulo operation on the value of a field and selects documents with a specified result.
$regex|Selects documents where values match a specified regular expression.
$text|Performs text search.
 |  

```js
db.movies.find({summary:{$regex: /musiacl/}}).pretty()
// Sales - Data for below example
{
    _id_: "1",
    volume: 89,
    target : 80
  },
  {
     _id_: "2", 
   volume: 200,
    target : 177
  },

db.sales.find({$expr:{$gt : ["$volume","$target"]}}).pretty() // get data were volume is greater than target
// Advance Query
db.sales.find({$expr: {$gt: [$cond: {if : {$gte: ["$volume",190]}, then: {subtract: ["$volume",30]}, else: "$volume"}},"$target"]}}).pretty() // cond -  condition if volume is > 190 then subtract volume by 30 else return volume then compare with target and return result

// Output 
{
    _id_: "1",
    volume: 89,
    target : 80
  },  //  89 > 80  so...
  // 2 document 200 is > 190 so it subtracted with 30 === 200 - 30 = 170
  // now 170 is not > 177 so this document is not returned
```
#### Array
**Name**|**Description**
:-----:|:-----:
$all|Matches arrays that contain all elements specified in the query.
$elemMatch|Selects documents if element in the array field matches all the specified $elemMatch conditions.
$size|Selects documents if the array field is a specified size.

```js
// users - Data for below example
{
    _id_: "1",
    name: "bro",
    age : 80,
    hobbies: ["playing","eating","watching"]
  },
db.users.find({hobbies: {size:3}}).pretty{} // get document where hobbies are equal to 3 
db.movies.find({genre:{all: ["action","horror"]}}).pretty() 
// it ensures that these two elements exist in a genre but it doesn't care about the order. And therefore it find both documents even though the order is different within genre.

// Demo data
{
    _id_: "1",
    name: "bro",
    age : 80,
    hobbies: [
      {
        title: "Sports",
        frequency : 3
      },
      {
        title: "Playing",
        frequency : 6
      },
    ]
  },
  {
    _id_: "2",
    name: "john",
    age : 24,
    hobbies: [
      {
        title: "Sports",
        frequency : 2
      },
      {
        title: "Playing",
        frequency : 3
      },
    ]
  },
// To get the data where hobbies Sport and frequency >=3
db.users.find({$and: [{"hobbies.title":"Sports"},{"hobbies.frequency":{$gte : 3}}]}).pretty() // it return both the documet which is we want
db.users.find({hobbies:{elemMatch:{title:"Sports",frequency:{$gte:3}}}}).pretty() // this return only the 1st Document
// 
```

#### Projection

Helps to get only required data from the document(Mogodb) which help can leave unnecessary data which is not required

```js
db.users.find({},{name:1}).prettty() // Will get data only with id,name by default other value are point to 0 and they are left out 
db.users.find({},{name:1,_id:0}).prettty() // will get data only with name, to neglect _id is we have to mention in the query
```

##### Projection Operators
**Name**|**Description**
:-----:|:-----:
$|Projects the first element in an array that matches the query condition.
$elemMatch|Projects the first element in an array that matches the specified $elemMatch condition.
$meta|Projects the available per-document metadata.
$slice|Limits the number of elements projected from an array. Supports skip and limit slices.

```js
db.movies.find({genres: "Drame"},{"genres.$":1}).pretty()
db.movies.find({genres: "Drame"},{genres:{$elemMatch:{$eq:"Horror"}}}).pretty()
db.movies.find({"rating.average":{$gt:9}},{geners: {$slice:2}, name:1}).pretty() // get documet where rating is >9 and get 1st two element from geners
db.movies.find({"rating.average":{$gt:9}},{geners: {$slice:[1,2]}, name:1}).pretty()  // skip 1 elemnt and get next 2 elemnt from geners
```
#### Update

```js
updateOne(filter,data,options)

db.users.updateOne({name:"john"},{$set : {mail: "john@gmail.com"}}) // add mail feild is not present or update mail value if present in user table where name=john
```

```js
updateMany(filter,data,options)

db.users.updateMany({},{$set: {gender: "M"}}) // updated all document to gender=m can also add filter condition
```
```js
replaceONe(filter,data,options)
```

```js
// Increment
db.users.updateOne({name:"john"},{$inc:{age:1}}) // increament age bu 1 where name = john
// Decrement
db.users.updateOne({name:"john"},{$inc:{age:-1}}) // Decrement age bu 1 where name = john
db.users.updateOne({name:"john"},{$inc:{age:1},{$set: {gender: "M"}}}) // increament age bu 1 where name = john and add gender = m
// Get rid og Fields
db.users.updateMany({gender:"M"},{$unset:{phone: ""}}) // removes phones filed in the document where gender = M

// Renaming Fields
db.users.updateMany({},{$rename: {age: "totalAge"}})

// upsert - if the document does not exist it creates
db.users.updateOne({name:"check"},{$set:{age:23}},{upsert: true}) // by default is false
// Update Matched Array Element
db.users.updateMany({hobbies:{$elemMatch: {title:"Sports",frequency:{$gte:3}}}},{$set:{"hobbies.$.highFrequency":true}}) // add highfrequecy where title is sport and frequcny is greater than 3

// updated all array element
db.users.updateMany({totalAge:{$gt:30}},{$inc:{"hobbies.$[].frequency":-1}})
// Addind element to Arrays
db.users.updateOne({name:"Maria"},{$push:{hobbies:{title:"Sports",frequency:2}}}) // can add duplicate element
db.users.updateOne({name:"Maria"},{$push:{hobbies:{title:"Sports",frequency:2}}})
db.users.updateOne({name:"Maria"},{$push:{hobbies:{$each : [{title:"Sports",frequency:1},{title:"Hiking",frequency:2}],$sort:{frequency:-1},$slice:1}}}) // sort and slice are avaiable if requried
// Removing elemnt from Array
db.users.updateOne({name:"john"},{$pull:{hobbies:{title:"Sports"}}})
// to remove the last element in the Array
db.users.updateOne({name:"john"},{$pop:{hobbies:1}} // 1 removes the last element use -1 to remove the fiest element)        
```
#### Delete

```js
deleteOne(filter,options)

db.users.deleteOne({name:"bro"}) // delete where name=john
```

```js
DeleteMany(filter,options)

db.users.deleteMany()                //  delete all users documents
db.users.deleteMany({},{gender:"M"}) //  delete where gender
// drop collection
db.users.drop()
```


#### Embedded Documents

```js
// Insert Data to users table
 db.users.insertOne({name:'john',age:23,address:{street:"1756 Hood",city:"san diego",state:'CA',zipcode:92117}})

 // Output data 
 {
	"_id" : ObjectId("5ffc5ae6ceff1b7524c0e870"),
	"name" : "john",
	"age" : 23,
	"address" : {
		"street" : "1756 Hood",
		"city" : "san diego",
		"state" : "CA",
		"zipcode" : 92117
	}
}

 // Even Array
  db.users.insertOne({name:'bro',age:23,address:[{street:"1756 Hood",city:"san diego",state:'CA',zipcode:92117},{hobbies:["playing,watching"]}]})

 // Output data 
  {
	"_id" : ObjectId("5ffc5be6ceff1b7524c0e871"),
	"name" : "john",
	"age" : 23,
	"address" : [
		{
			"street" : "1756 Hood",
			"city" : "san diego",
			"state" : "CA",
			"zipcode" : 92117
		},
		{
			"hobbies" : [
				"playing,watching"
			]
		}
	]
}
// to get embedded Document

db.users.find({"address.zipcode":92117})

```

#### Date Types

```js
Text
Boolean
Number -> Integer(int32),NumberLong(int64),NumberDecimal(12.99)
ObjectId
Date ->ISODate, TImeStamp
Embedded Documnets
Array
```

#### Cursors
```js
db.movies.find().count()
// save cursor and use next method
const data  = db.movies.find()
data.next()
// Sorting  1= asc -1 = desc
db.movies.find().sort({"rating.average": -1}).pretty()
db.movies.find().sort({"rating.average": 1, runtime: 1}).pretty()
// Skip and limit
db.movies.find().sort({"rating.average": -1}).skip(10).limit(10).pretty()


```
#### Relationship 

1. One To One Relations - Embedded
1. One To One - Using References
2. One To Many - Embedded
2. One To Many - Using References
3. Many To Many - Embedded
3. Many To Many - Using References

#### Schema Validation
```js
db.createCollection('posts', {
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  }
});
```
#### Updated Schema Validation
```js
db.runCommand({
  collMod: 'posts',
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  },
  validationAction: 'warn'
});
```
##### Run mongod as services
```bash
# To start 
sudo mongod --fork --logpath /user/log/log.log
# To stop
db.shutdownServer()
```

#### Help

```bash
# use help in mongo shell
help

# to get help that can run on the databse run
db.help()

# to get help that can run on the collection run
db.user.help()
```

#### Import Data to Mongo

```bash
# tv.json is data file
# -d is the Database name
# -c Collection Name
# --jsonArray tv.json contain an array of document so we jsonArray [it inform to mongo that there are multiple documents]
# --drop if this collection already exist it drop and readded
mongoimport tv.json -d movieData -c movies --jsonArray --drop
# Output

#2021-01-12T11:37:27.444+0530	connected to: localhost
#2021-01-12T11:37:27.515+0530	imported 240 documents


```

#### Indexes
Indexes support the efficient execution of queries in MongoDB. Without indexes, MongoDB must perform a collection scan, i.e. scan every document in a collection, to select those documents that match the query statement. If an appropriate index exists for a query, MongoDB can use the index to limit the number of documents it must inspect.

Indexes are special data structures [1] that store a small portion of the collection’s data set in an easy to traverse form. The index stores the value of a specific field or set of fields, ordered by the value of the field. The ordering of the index entries supports efficient equality matches and range-based query operations. In addition, MongoDB can return sorted results by using the ordering in the index.


```js
// import persons.json
db.contacts.explain().find({"dob.age": {$gt:60}})
/*
The Above query gives which scan mogodb used
*/
db.contacts.explain("executionStats").find({"dob.age": {$gt:60}})
/*
The Above query gives which scan mogodb used and will get the exection time it took and how to document it has looked
*/

// Create Index
db.contacts.createIndex({"dob.age":1}) // 1 - asc and -1 - desc
// After creating a index the execute time is less compare to above one
db.contacts.explain("executionStats").find({"dob.age": {$gt:60}})

// Building Indexes in Background
db.contacts.createIndex({"dob.age":1},{background:true}) // not lock database , which good for production

// drop index
db.contacts.dropIndex({"dob.age":1})

// Create Compound Index
db.contacts.createIndex({"dob.age":1,gender: 1}) // it will not create two indexes, it only one index  with paires of age and gender like this-- 23 M

// to get data using index
db.contacts.explain().find({"dob.age": {$gt:60},gender:"male"}) // index scan
db.contacts.explain().find({"dob.age": {$gt:60}}) // index scan
// you can only use compound Index from left to right
db.contacts.explain().find({gender:"male"}) // coll  scan

// Sorting using Indexes
db.contacts.explain().find({"dob.age": 60}).sort({gender:1}) // asc oder because 1 

// Partial Filters
db.contacts.createIndex({"dob.age":1},{partialExpression: {gender:"male"}}) // index create only for the gender with male and left remaining one

// To keep this in mind while use Partail Filter
/*
since we say nothing about the gender in our query here, it would be too risky to use
the index for that because the index is a partial index and mongodb as a top priority ensures
that you don't lose any data.
*/
db.contacts.explain().find({"dob.age":{$gt:60}} // Coll Scan 

/*
The difference is that for the partial index, the overall index simply is smaller, there really are only the ages of males stored in there, the female keys are not stored in the index and therefore, the index size is smaller leading to a lower impact on your hard drive, if you insert a new female, that will never have to be added to your index.
*/
db.contacts.explain().find({"dob.age":{$gt:60},gender:"Male"} // Index scan


db.contacts.createIndex({"dob.age":1},{partialExpression: {"dob.age":{$gt:60}}}) // index create only for the age > 60 and left remaining one

// Text Indexes
{
  title: "A ship book",
  description: "This book is awesome, i like this one"
}
db.products.createIndex({description: "text"})

// get Data using Text Indexes
db.products.find({$text: {$search: "book"}}) // search as word
db.products.find({$text: {$search: "\"book is awesome\""}}) // search as a sentences

// to print data 1st with high meta scroce which given by mogodb itself
db.products.find({$text: {$search: "book awesome"}},{scroe: {$meta: "textScore"}})

// sort by meta Score
db.products.find({$text: {$search: "book awesome"}},{scroe: {$meta: "textScore"}}).sort({score:{$meta: "textScore"}})

// Exclude Text 
db.products.find({$text: {$search: "book -awesome"}}) // excilude text awesome

// Combined Text Indexes
db.products.createIndex({title:"text",description:"text"})

// Language and using weight
db.products.createIndex({title:"text",description:"text"}{default_language:"english",weights:{title:1,description:10}}) // lang - english by default weight to important

// Get data using Combined Text Indexes
// get document if the text matches in title or description
db.products.find({$text: {$search: "ship"}}) // get example document


// To get Indexes
db.contacts.getIndexes() // removes all the stop word amd stores all the keyword in array
```

#####  Index Restrictions
```js
if you have a query that will return a large portion or the majority of your documents, an index can actually be slower because you then just have an extra step to go through your almost entire index list and then you have to go to the collection and get all these documents, so you then just have an extra step because if you do a full collection scan, it can be faster.

the idea of index is to quickly let you get to a narrow subset of your document list and not to the majority of that.
```
#### Time to Live Index

```js
// It can be used single field indexes, does not work on compound indexes. it works on data objects
db.seesions.createIndex({createdAt:1},{expireAfterSeconds: 10})
```
#### Covered Query. 
```js
A covered query is a query that can be satisfied entirely using an index and does not have to examine any documents. An index covers a query when all of the following apply: all the fields in the query are part of an index, and. all the fields returned in the results are in the same index.

// Example
// user collection data 

{
  name : "bro"
},
{
  name: "john"
}
// Create a index
db.users.createIndex({name:1})

// find using index 
db.contacts.explain("executionStats").find({name:"bro"}) // Index scan but not covered query because it examined document

// Covered Query of about example
db.contacts.explain("executionStats").find({name:"bro"},{_id:0,name:1}) // covered query beacuse the name is already present in indexed 
```
