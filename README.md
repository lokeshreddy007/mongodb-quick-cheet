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

###### Query Selectors
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
###### Project Operators
1. $
2. $elemMatch
3. $meta
4. slice

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
#### Delete

```js
deleteOne(filter,options)

db.users.deleteOne({name:"bro"}) // delete where name=john
```

```js
DeleteMany(filter,options)

db.users.deleteMany()                //  delete all users documents
db.users.deleteMany({},{gender:"M"}) //  delete where gender
```

#### Projection

Helps to get only required data from the document(Mogodb) which help can leave unnecessary data which is not required

```js
db.users.find({},{name:1}).prettty() // Will get data only with id,name by default other value are point to 0 and they are left out 
db.users.find({},{name:1,_id:0}).prettty() // will get data only with name, to neglect _id is we have to mention in the query
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