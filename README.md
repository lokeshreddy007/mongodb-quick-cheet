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
```