# Mongo DB Notes:
--------------------
 Key Features:
 
 	-- BSON Data Structure
	-- No Schema
	-- Documents can be of different structures in the same collection.

Mongo Ecosystem:

	-- Self Managed
	-- Atlas Cloud
	-- Mobile
	-- Compass
	-- BI connectors
	-- Mongo Charts
	
Mongo Serverless:

	-- Stitch
	-- Serverless Query API
	-- Serverless functions
	-- DB triggers

Commands:

    -- Check Mongo: mongo
    -- Start Mongo: mongod --dbpath "/Users/abinashpadhee/data/db"
                    sudo mongod --dbpath "/Users/abinashpadhee/data/db"
    -- Change port: mongod --port 27018
                    mongo --port 27017

Hands On:

	-- show dbs
	-- use shop // to switch to db 
	-- db.products.insertOne({name:"AspectJ Book", price:"15.99"})
	-- db.products.find()
	-- db.products.find().pretty()
	-- To use manual ID
	db.flightData.insertOne({departureAirport: "DEL", arrivalAirport:"CAL", distance: 1500, _id:"BBS_DVR"})
	-- To update One
	db.flightData.updateOne({departureAirport: "MUC"},{$set: {marker:"delete"}})
	-- To update many
	db.flightData.updateMany({},{$set: {marker:"todelete"}})
	-- To delete many
	db.flightData.deleteMany({marker: "todelete"})
	-- To find using filters
	
	db.flightData.find({intercontinental: true}).pretty()
	db.flightData.find({distance: 10000}).pretty()
	db.flightData.find({distance: {$gt:10000}}).pretty()
	db.flightData.findOne({distance: {$gt:800}})
	
	-- To update using Update
	db.flightData.update({_id:ObjectId("5e23fcfc7e087657578ecb20")},{$set: {delayed:true}})
	// Overwrite existing data as below
	db.flightData.update({_id:ObjectId("5e23fcfc7e087657578ecb20")},{delayed:true})
	-- To replace One
	
	-- Projection:
	db.passengers.find({}, {name: 1}).pretty()
	db.passengers.find({}, {name: 1, _id: 0}).pretty()
	
	-- Embedded Documents
	db.flights.updateMany({}, {$set: {status: {description: "delayed", lastUpdated: "1 hr ago"}}})
	-- Arrays inside documents
	db.flights.updateOne({departureAirport: "MUC"}, {$set: {discountCards: ["Axis","ICICI"]}})
	-- Query arrays
	db.flights.findOne({departureAirport: "MUC"}).discountCards
	db.flights.find({discountCards:"Axis"}).pretty()
	-- Query nested data
	db.flights.find({"status.description" : "delayed"}).pretty()
	
Hands On Schemas/Structures:

	-- Diff schemas possible in the same collection
		db.products.insertOne({name: "R D Sharma", price: 12.99})
		db.products.insertOne({title:"T Shirt", seller: {name: "Ravi", age: 27}})
		
		db.products.insertOne({name: "A Laptop", price: 260.99, details: {cpu: "i7 9th gen"}})
	
		Delete All
		db.products.deleteMany({})
		
	-- Drop database and collection
		db.dropDatabase()
		db.companies.drop() // Returns TRUE
		
	-- DATA TYPE Example
		db.companies.insertOne({ name: "Apple Inc.", isStartup: true, employees: 33, funding: 12345678901234567890, details: {ceo: "Spencer"}, tags: 		[{title:"super"}, {title: "perfect"}], foundingDate: new Date(), insertedAt: new Timestamp()})
		
	-- DB Stats
		db.stats()
		
	-- NumberInt function
		db.numbers.insertOne({a: NumberInt(1)})
		
		typeof db.numbers.findOne().a
		
MongoDB Relations:

	-- One to One
		db.patients.insertOne({name: "Max", age: 27, disSummary: "dis-sum-max-1"})
		db.disSummaries.insertOne({_id: "dis-sum-max-1", diseases: ["cold", "fever"]})
		
	-- One to Many
		db.qThreads.insertOne({creator: "Max", qstn: "How it works?", answers: [{text: "Just like that"}, {text: "Cool"}]})
		
		db.city.insertOne({name: "Hyderabad", country: "India"});
		db.citizens.insertMany([{name: "Tom", cityId: "<ObjectId of the city>"}, ...])
		
	-- Many to Many
		db.products.insertOne({name:"AspectJ Book", price:"15.99"})
		db.customers.insertOne({name: "Max", age: 27})
		db.customers.updateOne({_id: ObjectId("5e2db1d4648e8ebb1f50a13f")}, {$set: {orders:[{productId: ObjectId("5e2db1a0648e8ebb1f50a13e"), quantity: 2}]}})
		
		// insert many
		db.authors.insertMany([{name: "Max", age: 28, address: {street: "Corner ST"}}, {name: "Jon", age: 29, address: {street: "Baker St"}}])
		
	-- Aggregation and Lookup
		db.books.aggregate([{$lookup: {from: "authors", localField: "authors", foreignField: "_id", as: "creators"}}])
		
	-- Blog Example
		db.users.insertMany([{name: "Tanu", age: 28, email: "tanu@test.com"}, {name: "Manu", age: 29, email: "manu@test.com"}])
		
		db.posts.insertOne({title: "1st Test Post", text: "Hey how r u ?", tags: ["new", "trending"], creator: ObjectId("5e36604e702e436b8f399d5e"), comments: [{text: "Hey I am fine", author: ObjectId("5e36604e702e436b8f399d5f")}]})
		
		
		
MongoDB Shell Options:

    -- mongod --dbpath "/Users/abinashpadhee/data/db" --logpath "/Users/abinashpadhee/Development/MongoDB/logs/log.log"
    
    Run as a background service
    -- mongod --fork --logpath "/Users/abinashpadhee/Development/MongoDB/logs/log.log"
    -- use admin
    -- db.shutdownServer()
    
    Using a config file
    -- mongod -f /Users/abinashpadhee/Development/MongoDB/mongodb-macos-x86_64-4.2.2/bin/mongod.cfg
    
MongoDB Create Operations:

    -- db.hobbies.insertMany([{_id: "driving", name: "driving"}, {_id: "cooking", name: "cooking"}, {_id: "sleeping", name: "sleeping" }])
    
    Unordered Inserts
    -- db.hobbies.insertMany([{_id: "driving", name: "driving"}, {_id: "cooking", name: "cooking"}, {_id: "sleeping", name: "sleeping" }], {ordered: false})
    
    Write Concern
    -- db.persons.insertOne({name: "Rancho", age: 36}, {writeConcern: {w: 1}})
    -- db.persons.insertOne({name: "Ian", age: 33}, {writeConcern: {w: 1, j: true}})
    
    Importing Data
    -- mongoimport /Users/abinashpadhee/Development/MongoDB/learn-mongo/tv-shows.json -d movieData -c movies --jsonArray --drop

MongoDB Read Operations:

    -- db.movies.find({runtime: {$lt:60}}).pretty()
    
    Embedded Documents
    -- db.movies.find({"rating.average": {$gt:8}}).pretty()
    
    Arrays
     -- db.movies.find({"genres": "Drama"}).pretty()
     -- db.movies.find({"genres": ["Drama"]}).pretty()
     
    In and Nin
    -- db.movies.find({"runtime": {$in: [40, 50]}}).pretty()
    -- db.movies.find({"runtime": {$in: [40, 50]}}).pretty()
    
    Or and Nor
    -- db.movies.find({$or: [{"rating.average": {$lt:5}}, {"rating.average": {$gt:9}}]}).count()
    
    And
    -- db.movies.find({$and: [{genres: "Drama"}, {"rating.average": {$gt:9}}]}).count()
    
    Element Operators
    -- db.movies.find({age: {$exists: true}}).pretty()
    -- db.movies.find({age: {$exists: true, $gt: 30}}).pretty()
    -- db.movies.find({age: {$exists: true, $ne: null}}).pretty()
    
    Type Operator
    -- db.movies.find({age: {$type: number}}).pretty()
    
    Regex Operator
    -- Not very performance efficient
    -- db.movies.find({summary: {$regex: /musical/}}).pretty()
    
    Expr operator
    -- db.monthlyBudget.find( { $expr: { $gt: [ "$spent" , "$budget" ] } } )
    
    Querying Arrays
    -- db.users.find({"hobbies.title": "Sports"}).pretty()
    -- db.users.find( { hobbies: { $size: 2 } } );
    -- db.inventory.find( { tags: { $all: [ "appliance", "school", "book" ] } } )
    
    The following query matches only those documents where the results array contains at least one element that is both 
    greater than or equal to 80 and is less than 85.
    -- db.scores.find(
          { results: { $elemMatch: { $gte: 80, $lt: 85 } } }
       )
    -- db.survey.find(
          { results: { $elemMatch: { product: "xyz", score: { $gte: 8 } } } }
       )
       
    Applying Cursors
    -- 
    
Update Operations

    -- db.users.find({"hobbies.title": "Sports"}).pretty()
    -- db.users.updateMany({"hobbies.title": "Sports"}, {$set: {isSporty: true}})
    -- db.users.updateOne({name: "Manuel"}, {$inc: {age: 3}})
    -- db.users.updateMany({},{$rename: {"age": "totalAge"}})
    
    Upsert Operation
    -- db.users.updateOne({name: "Maria"}, {$set:{age: 32, hobbies: [{title: "Cooking", frequency: 3}, {title: "Reading", frequency: 5}], phone: 1234567890}}, {upsert: true})
    
    Update Arrays
    -- db.users.updateMany({hobbies: {$elemMatch: {title: "Sports", frequency: {$gte: 3}}}}, {$set: {"hobbies.$.highFrequency": true}})
    -- db.users.updateMany({totalAge: {$gt: 30}}, {$inc: {"hobbies.$[].frequency": -1}})
    -- db.users.updateMany({"hobbies.frequency": {$gt: 2}}, {$set: {"hobbies.$[el].goodFrequency": true}}, {arrayFilters: [{"el.frequency": {$gt: 2}}]})
    
MongoDB Indexes

    -- 
    
Geospatial Data

    -- 