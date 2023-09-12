# nosql-challenge

 In this project we will evaluate some of the ratings data of food establishments.
 
In this project, we will see how to use NoSQL "Not Only SQL" to manipulate a database and retrieve data.

We will work on three parts:

1. Part 1: Database and Jupyter Notebook Set Up

2. Part 2: Update the Database
   
3. Part 3: Exploratory Analysis

## What we will learn from this project:

- How to import data provided in json file, create an instance of the Mongo Client and load data properly
  
- How to insert, update, delete, retrieve and display information from the database


## Instructions:

Part1:

- Import the data provided in the establishments.json file from the Terminal: $ mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json

- List the databases in MongoDB. Confirm that the new created one is listed, list the collection in the database (we have one collection called establishment), and find and display one document

Part2:

-  Add a new establishment
  
-  Update the new establishment to be consistent with the rest of the establishments in the database
  
-  Delete unnecessary infonmation

- Convert some field types

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
import pandas as pd
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

#### Part2: Update the Database

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
# for i in result:
#     print(i)
result[0]
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
##### Update structure (types) and retrieve data
```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Change the data type from String to Decimal for longitude and latitude
query={'$set':{ "geocode.longitude" : {'$toDouble': "$geocode.longitude"}, "geocode.latitude" : {'$toDouble': "$geocode.latitude"} }}
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
print('the type of RatingValue is ', r)
print('the type of Latitude is ', la)
print('the type of Longitude is ', lo)
[doc for doc in establishments.find({}, ['geocode.longitude', 'geocode.latitude','RatingValue'])]
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
### Part 3: Exploratory Analysis

```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Find the establishments with a hygiene score of 20
query = {"scores.Hygiene": 20}

# Use count_documents to display the number of documents in the result
x = establishments.count_documents(query)
print('The number of establishment with a hygiene score of 20 is: ', x)

# Display the first document in the results using pprint
res = establishments.find(query)
pprint(res[0])

# Convert the result to a Pandas DataFrame
hygien_score20_df = establishments.find(query)
df1= pd.DataFrame(hygien_score20_df)

# Display the number of rows in the DataFrame
print("The number of establishments with a hygiene score of 20: ", len(df1))

# Display the first 10 rows of the DataFrame
df1.head(10)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Find the establishments with London as the Local Authority and has a RatingValue greater than or equal to 4.
query = {'LocalAuthorityName': {'$regex':'London'},'RatingValue': {'$gte': 4} }

# Use count_documents to display the number of documents in the result
x = establishments.count_documents(query)

# Display the first document in the results using pprint
res = establishments.find(query)
print('The number of establishment with London as the Local Authority and has a RatingValue greater than or equal to 4 is ',x)
pprint(res[0])

# Convert the result to a Pandas DataFrame
london_Rate_g4_df  = establishments.find(query)
df2= pd.DataFrame(london_Rate_g4_df)

# Display the number of rows in the DataFrame

print('The number of establishment with London as the Local Authority and has a RatingValue greater than or equal to 4 in the dataframe is ',len(df2))
# Display the first 10 rows of the DataFrame
df2.head(10)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Find the establishments with London as the Local Authority and has a RatingValue greater than or equal to 4.
query = {'LocalAuthorityName': {'$regex':'London'},'RatingValue': {'$gte': 4} }

# Use count_documents to display the number of documents in the result
x = establishments.count_documents(query)

# Display the first document in the results using pprint
res = establishments.find(query)
print(x)
pprint(res[0])
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Search within 0.01 degree on either side of the latitude and longitude.
# Rating value must equal 5
# Sort by hygiene score

degree_search = 0.01
# Search within 0.01 degree on either side of the latitude and longitude.
# Rating value must equal 5
# Sort by hygiene score
degree_search = 0.01
la= 51.49014200
lo= 0.08384000

query = {'geocode.latitude': {'$gte': la - degree_search, '$lte': la + degree_search},
        'geocode.longitude': {'$gte': lo - degree_search, '$lte': lo + degree_search},
        'RatingValue':5 }

sort=[('scores.Hygiene',1)]

# Print the results
results = establishments.find(query).sort(sort).limit(5)
pprint(list(results))

# Convert result to Pandas DataFrame
df3 = pd.DataFrame(establishments.find(query).sort(sort).limit(5))
df3
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create a pipeline that: 
# 1. Matches establishments with a hygiene score of 0
match_query = {'$match': {'scores.Hygiene': 0}}

# 2. Groups the matches by Local Authorityz
group_query = {'$group': {'_id': "$LocalAuthorityName", 'count': { '$sum': 1 }}}

# 3. Sorts the matches from highest to lowest
sort_values = {'$sort': { 'count': -1 }}

# Print the number of documents in the result
pipeline = [match_query, group_query, sort_values]
results = list(establishments.aggregate(pipeline))
print("Number of establishment  in result: ", len(results))

# Print the first 10 results
pprint(results[0:10])

# Convert the result to a Pandas DataFrame
df4= pd.DataFrame(results)
# Display the number of rows in the DataFrame
print('Number of establishment  in the dataframe: ', len(df))

# Display the first 10 rows of the DataFrame
df4.head(10)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
##### Supports: Slack AskBCS Learning Assistant
