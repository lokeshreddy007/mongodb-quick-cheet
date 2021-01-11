# MongoDB Quick Guide

## MongoDB

MongoDB is a NoSQL database that stores data as JSON-like documents. Documents store related information together and use the MongoDB query language (MQL) for access.

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
    age: 23,
  },
]); // insert Multiple documents at once
```

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