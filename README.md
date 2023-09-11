# nosql-challenge

 In this project we will evaluate some of the ratings data of food establishments.
 
In this project, we will see how to use NoSQL "Not Only SQL" to manipulate a database and retrieve data.

We will work on three parts:

1. Part 1: Database and Jupyter Notebook Set Up

2. Part 2: Update the Database
   
3. Part 3: Exploratory Analysis

## What we will learn from this project:

- How to import data provided in json file, create an instance of the Mongo Client and  loaded data properly
  
- How to insert, update, delete, retrieve and display information from the database


## Instructions:

Part1:

- Import the data provided in the establishments.json file from the Terminal: $ mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json

- List the databases in MongoDB. Confirm that the new created one is listed, list the collection in the database (we have one collection called establishment), and find and display one document.

Part2:

-  Add a new establishment
  
-  Update the new establishment to be coherent with the rest of the establishments in the database
  
-  Delete unusuful information

- Convert some fields type

Part 3:

- Retrieve and display information

## Program:

### Tools:

- Python: is a programming language.

- Pandas: is a Python library designed for data manipulation and analysis.

- MongoDB: is an open-source, NoSQL database management system.

- PyMongo: is a MongoDB driver for Python. It allows Python developers to interact with MongoDB databases programmatically.
  
- pprint: is a module in Python used to format and display data structures

### Code:

#### Part 1: Database and Jupyter Notebook Set Up

```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Import dependencies
from pymongo import MongoClient
from pprint import pprint
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create an instance of MongoClient
mongo = MongoClient(port=27017)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# confirm that our new database was created
print(mongo.list_database_names())
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# assign the uk_food database to a variable name
db = mongo['uk_food']
db
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# review a document in the establishments collection
document = db['establishments'].find_one()
pprint(document)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# assign the collection to a variable
establishments = db['establishments']
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```

#### Part2 : Database and Jupyter Notebook Set Up

##### Insert and retrieve data

```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create a dictionary for the new restaurant data
new_res={
    "BusinessName":"Penang Flavours",
    "BusinessType":"Restaurant/Cafe/Canteen",
    "BusinessTypeID":"",
    "AddressLine1":"Penang Flavours",
    "AddressLine2":"146A Plumstead Rd",
    "AddressLine3":"London",
    "AddressLine4":"",
    "PostCode":"SE18 7DY",
    "Phone":"",
    "LocalAuthorityCode":"511",
    "LocalAuthorityName":"Greenwich",
    "LocalAuthorityWebSite":"http://www.royalgreenwich.gov.uk",
    "LocalAuthorityEmailAddress":"health@royalgreenwich.gov.uk",
    "scores":{
        "Hygiene":"",
        "Structural":"",
        "ConfidenceInManagement":""
    },
    "SchemeType":"FHRS",
    "geocode":{
        "longitude":"0.08384000",
        "latitude":"51.49014200"
    },
    "RightToReply":"",
    "Distance":4623.9723280747176,
    "NewRatingPending":True
}
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Insert the new restaurant into the collection
establishments.insert_one(new_res)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Check that the new restaurant was inserted
query = {"BusinessName": "Penang Flavours"}
establishments.find_one(query)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Find the BusinessTypeID for "Restaurant/Cafe/Canteen" and return only the BusinessTypeID and BusinessType fields
query = {"BusinessType": "Restaurant/Cafe/Canteen"}
result = establishments.find(query,{"_id": 0,"BusinessTypeID":1, "BusinessType":1} )
for i in result:
    print(i)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Find the BusinessTypeID for "Restaurant/Cafe/Canteen" and return only the BusinessTypeID and BusinessType fields
query = {"BusinessType": "Restaurant/Cafe/Canteen"}
result = establishments.find(query,{"_id": 0,"BusinessTypeID":1, "BusinessType":1} )
for i in result:
    print(i)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
##### Update and retrieve data

```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Update the new restaurant with the correct BusinessTypeID
query1 = {"BusinessName": "Penang Flavours"}
query2 = {"$set": {"BusinessTypeID": 1}}
establishments.update_one(query1,query2)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Confirm that the new restaurant was updated
query = {"BusinessName": "Penang Flavours"}
result=establishments.find_one(query)
print('The value of the BusinessTypeId of the new restaurant is ', result["BusinessTypeID"])
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
##### Delete and retrieve data

```
# Find how many documents have LocalAuthorityName as "Dover"

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
query = {"LocalAuthorityName": "Dover"}
nbr=establishments.count_documents(query)
print('The number of Dover Local Authority is ', nbr) 
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Delete all documents where LocalAuthorityName is "Dover"
query = {"LocalAuthorityName": "Dover"}
establishments.delete_many(query)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Check if any remaining documents include Dover
query = {"LocalAuthorityName": "Dover"}
nbr=establishments.count_documents(query)
print('The number of Dover Local Authority after deleting is ', nbr)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Check that other documents remain with 'find_one'
pprint(establishments.find_one())
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
##### Update structure and retrieve data
```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Change the data type from String to Decimal for longitude and latitude
query={'$set':{ "geocode.longitude" : {'$toDecimal': "$geocode.longitude"}, "geocode.latitude" : {'$toDecimal': "$geocode.latitude"} }}
establishments.update_many({},[query])
pprint(establishments.find_one())
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Change the data type from String to Integer for RatingValue
query = {'$set': {'RatingValue': {'$toInt': '$RatingValue'}}}
establishments.update_many({},[query])
pprint(establishments.find_one({}))
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Check that the coordinates and rating value are now numbers
doc=establishments.find_one()
r=type(doc.get('RatingValue'))
la=type(doc.get('geocode').get('latitude'))
lo=type(doc.get('geocode').get('longitude'))
print('The type of RatingValue is ', r)
print('The type of Latitude is ', la)
print('The type of Longitude is ', lo)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
### Hint:
In order of verification and using the learned tools, I used MS Excel to verify with a sample from hawaii_measurements table.

Results of the Min Max and Average of the temperature between start date: 2010-02-08 and end date: 2010-02-09
<img src='start-end-flask.png' style ='width:700px;height:300px'/>  <img src='start-end-excel.png' style ='width:700px;height:300px'/>



Results of the Min Max and Average of the temperature from start date: 2017-08-01 to last date: 2017-08-23

<img src='start-flask.png' style ='width:700px;height:300px'/>  <img src='start-excel.png' style ='width:700px;height:300px'/>

##### Supports: Slack AskBCS Learning Assistant/Tutoring support
