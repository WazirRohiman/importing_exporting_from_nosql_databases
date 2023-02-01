Install the ‘couchimport’ and ‘couchexport’ tools to move data in and out of the Cloudant database
```
npm install -g couchimport@1.4.0
```

Verify installation - If you get a version number then you are good to move to go
```
couchimport --version
```

Install the ‘mongoimport’ and ‘mongoexport’ tools to move data in and out of the mongodb database
```
wget https://fastdl.mongodb.org/tools/db/mongodb-database-tools-ubuntu1804-x86_64-100.3.1.tgz
tar -xf mongodb-database-tools-ubuntu1804-x86_64-100.3.1.tgz
export PATH=$PATH:/home/project/mongodb-database-tools-ubuntu1804-x86_64-100.3.1/bin
echo "done"
```

Verify installation - If you get a version number then you are good to move to go
```
mongoimport --version
```
------------------------------------------------------------------------------------------------------

## Cloundant Import/Export Data

First set up the environment variable to CLOUDANTURL with you api url using the following syntax
```
export CLOUNDANTURL="API_URL_HERE"
```

To see all the documents in the ‘diamonds’ database printed in csv format.
```
couchexport --url $CLOUDANTURL --db diamonds --delimiter ","
```

To see all the documents in the ‘diamonds’ database printed in json format(one per line).
```
couchexport --url $CLOUDANTURL --db diamonds --type jsonl
```

To export data from the ‘diamonds’ database into json format and save to a file named ‘diamonds.json’.
```
couchexport --url $CLOUDANTURL --db diamonds --type jsonl > diamonds.json
```

To export data from the ‘diamonds’ database into csv format and save to a file named ‘diamonds.csv’.
```
couchexport --url $CLOUDANTURL --db diamonds --delimiter "," > diamonds.csv
```

------------------------------------------------------------------------------------------------------

## MongoDB Import/Export Data

Start MongDB Server. Note down your username and password.

Import data from the file ‘diamonds.json’ into a database named ‘training’ and a collection named ‘diamonds’.
```
mongoimport -u root -p PASSWORD_GOES_HERE_NO"" --authenticationDatabase admin --db training --collection diamonds --file diamonds.json
```

Login to MongoDB server to check if import was successfull

Export data from the ‘training’ database, ‘diamonds’ collection into a file named ‘mongodb_exported_data.json
```
mongoexport -u root -p PASSWORD_GOES_HERE --authenticationDatabase admin --db training --collection diamonds --out mongodb_exported_data.json
```

Export the **fields _id,clarity,cut,price** from the ‘training’ database, ‘diamonds’ collection into a file named ‘mongodb_exported_data.csv
```
mongoexport -u root -p PASSWORD_GOES_HERE --authenticationDatabase admin --db training --collection diamonds --out mongodb_exported_data.csv --type=csv --fields _id,clarity,cut,price
```

------------------------------------------------------------------------------------------------------

## Cassandra Import/Export Data

Start Cassandra Server, then use the provided CommandLine prompt to login to cqlsh

Create a keyspace name 'training'
```
CREATE KEYSPACE training  
WITH replication = {'class':'SimpleStrategy', 'replication_factor' : 3};
```

Create a table named ‘diamonds’ with the below schema in the 'training' keyspace
```
    id - primary key (use ‘id’ as the primary key; Cassandra does not allow you to create a column starting with underscore(_))
    clarity - text
    cut - text
    price - integer.
```

```
USE training;
CREATE TABLE diamonds (id int PRIMARY KEY, clarity text, cut text, price int);
```

Import the diamonds.csv into the diamonds table in the training keyspace
```
use training;
COPY training.diamonds(id,clarity,cut,price) FROM 'mongodb_exported_data.csv' WITH DELIMITER=',' AND HEADER=TRUE;
```

Export diamonds table to csv file called 'cassandra-diamonds.csv'
```
COPY diamonds TO 'cassandra-diamonds.csv';
```

Create an index on Cassandra
```
create index price_index on diamonds(price);
```









