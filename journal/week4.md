# Week 4 — Postgres and RDS


## Creating RDS Using AWS CLI

To create a RDS instance that runs Postgres database, just write the below command in your AWS CLI terminal, make sure that you are in frontend directory:

```
aws rds create-db-instance \
  --db-instance-identifier cruddur-db-instance \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version  14.6 \
  --master-username root \
  --master-user-password huEE33z2Qvl383 \
  --allocated-storage 20 \
  --availability-zone us-east-1a \
  --backup-retention-period 0 \
  --port 5432 \
  --no-multi-az \
  --db-name cruddur \
  --storage-type gp2 \
  --publicly-accessible \
  --storage-encrypted \
  --enable-performance-insights \
  --performance-insights-retention-period 7 \
  --no-deletion-protection

```

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226804699-1ab962b3-690c-45b5-9146-aeed1e428027.png)
" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226804719-ca4e5490-734b-427a-8188-e887420e816d.png)
" alt="Sublime's custom image"/>
</p>



Go to console and check if the RDS created or not:

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226804758-1ebd3213-1dfa-40e4-8a27-19d500944df9.png)
" alt="Sublime's custom image"/>
</p>

RDS runs in EC2 instance, and we have ability to stop it “temporarily” and it will be stopped only for 7 days.



<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226804787-cf57e6f8-8dfd-4f3d-8359-a05d04c3831b.png)
" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226804812-c4cacbed-a47b-4e15-9a89-d83c4eae6ea2.png)
" alt="Sublime's custom image"/>
</p>



Now, check your docker compose file if it contains Postgres database definition:

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226804834-8625fdda-d06a-40c3-8ea0-88806eb2d076.png)
" alt="Sublime's custom image"/>
</p>

## Connect to psql via client

Now, let’s make sure that we can access Postgres (make docker compose up) (this actually runs in our Gitpod environment not AWS RDS).
To connect to psql via the psql client cli tool remember to use the host flag to specific localhost.
```
psql -Upostgres --host localhost
```


now as mentioned above, without flaging to the specific host it will not work, because we working in container environment (we want to listen on localhost as supposed to docker):

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226804965-567ecb06-abe8-445c-831c-da80f447a46d.png)
" alt="Sublime's custom image"/>
</p>

Now after running full command:
```
psql -Upostgres --host localhost
```


Will ask for password: (password):
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226805051-c369b246-9984-45b3-bd2f-6584eaf10a39.png)
" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226805060-d9f7f081-f418-4eb6-9782-3f35940f6185.png)
" alt="Sublime's custom image"/>
</p>


Some common Postgres commands:

1-	\l used to list all databases:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226805129-3abcdde5-86e0-4879-b28a-f6e175b61ef6.png)
" alt="Sublime's custom image"/>
</p>

2-	\q used to exist from database
<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


3- To drop the database
```
DROP database cruddur;
```

4- \dt To see the created tables
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226808513-b5dbbd0e-35e4-4ca7-b652-f4dae060e051.png)
" alt="Sublime's custom image"/>
</p>


Now we need to setup some tables, and generally when you have web framework you will have a file called schema.sql, it will contain all the stuff to setup the schema (structure of the database).
Now move to /backend-flask add new folder called db, make new file called schema.sql
Usually, we add in this file the needed extensions in our application. 

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226805322-1579e8b7-0a86-4c08-b1fb-064b1a1fc788.png)
" alt="Sublime's custom image"/>
</p>



## UUID Extension

One of the needed extensions is UUID universally unique identifier, the idea that it will generate out ‘big, long id’, this id is random, so when you design applications, you don’t always want to use ids because it can be used to identify things. “Good practice to secure your ID”

Note, Postgres comes built-in extensions you can turn it on.


Now add the UUID extension to schema file using the below code:
This command will run only if this extension does not exist.
```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```



Now let’s run schema.sql file: (we should be in backend-flask directory):
```
psql cruddur < db/schema.sql -h localhost -U postgres
```

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226805765-4cc7baf3-223b-4e74-93e4-0b148262e3a2.png)
" alt="Sublime's custom image"/>
</p>


## Connection URL String

But it will be something annoying if every time we should write this code to make it up and create the extension, and then typing the password each time, we can make it easier using connection URL string.

Which is a way of providing all the details that needs to authenticate your server.

Here we can find the URL format: 
https://stackoverflow.com/questions/3582552/what-is-the-format-for-the-postgresql-connection-string-url

```
postgresql://[user[:password]@][netloc][:port][/dbname][?param1=value1&...]
```


Now we are going to write our extension:
```
export CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"
```

Now let’s check if its work by run the following:
```
postgresql://postgres:password@localhost:5432/cruddur
```

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226805999-30b53fb5-3218-43a8-b452-632922e09bcc.png)
" alt="Sublime's custom image"/>
</p>
We got connection refuse, then I remember that my docker compose is down!




<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806038-74bde5d0-e7e0-423f-a66e-88d62d24b602.png)
" alt="Sublime's custom image"/>
</p>
Another error, which is my password does not correct.



<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806077-e7e09523-dd66-4092-bc19-e64e66b4a41d.png)
" alt="Sublime's custom image"/>
</p>
Another error in database name.


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806107-2bf283b1-fe8e-4993-94c0-25e92568408c.png)
" alt="Sublime's custom image"/>
</p>
Connected :D


Now let’s make it as environment variable in my Gitpod environment:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806153-122fee41-1a57-45dc-b291-9bdc7332e4cd.png)
" alt="Sublime's custom image"/>
</p>

Now to access it write:
```
psql $CONNECTION_URL
```

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806214-ab9183cf-638e-4f08-b91a-36740cf392c0.png)
" alt="Sublime's custom image"/>
</p>

I got an error, which I think it is because the comma, I will correct it and then delete old env and create a new one.


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806263-6940e7d0-1983-4f40-ac87-749520b312a0.png)
" alt="Sublime's custom image"/>
</p>

To save this environment variable in case we restart the gitpod:
```
gp env CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"
```


Now we will setup connection URL for our production postgres data base “which in RDS”
```
PROD_CONNECTION_URL="postgresql://root:huEE33z2Qvl383@cruddur-db-instance.c1ykylodd1xi.us-east-1.rds.amazonaws.com:5432/cruddur"
```

```
export PROD_CONNECTION_URL="postgresql://root:huEE33z2Qvl383@cruddur-db-instance.c1ykylodd1xi.us-east-1.rds.amazonaws.com:5432/cruddur"
```

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806400-1eebff4e-7761-4b16-a5ed-04f333be26d6.png)
" alt="Sublime's custom image"/>
</p>

## Bash Scripting

Now we are going to create some scripts to make the create, drop, connection, and schema load to the database automatically.

Go to backend-flask and add a new folder and name its bin, which refers to binary and here we will put our bash script. 

Also add 2 files without extensions, db-create, and db-drop, and db-schema-load.

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806698-bad367ba-848d-4869-9d21-ed881c701c10.png)
" alt="Sublime's custom image"/>
</p>

To know the path of your bash run this command: 
```
whereis bash
```

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806761-20c38e40-9c24-4723-aa99-a25a11d72d30.png)
" alt="Sublime's custom image"/>
</p>

Also we need to make all of these are executable by run:
```
chmod u+x bin/db-create
Chmod u+x bin/db-drop
Chmod u+x bin/db-schema-load

```
Before it will be like:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806823-858bcbf3-cebd-4db1-ac99-ab5462f75ff6.png)
" alt="Sublime's custom image"/>
</p>

After making it executable:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226806846-b105696e-a8f8-48b8-a0d7-d9c9aa448c21.png)
" alt="Sublime's custom image"/>
</p>


To execute the file:
```
./bin/db-create 
```
or
```
source bin/db-create
```



1-	For db-drop we will add the below bash script to test it:
```
#! /usr/bin/bash

psql $CONNECTION_URL -c "drop database cruddur;"

```

and let’s execute it: "we got an error!"
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226807385-29576a0b-5b57-4642-a4f7-382f98e68b21.png)
" alt="Sublime's custom image"/>
</p>

The reason of that, when we use connection URL we are not only connecting postgres, we are connecting to a specific database ‘cruddur’, so we cant ask it to drop the database as we in it. 

So, we should exclude the database part ‘/cruddur’

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226807621-c8887a54-7f82-44cb-8b47-c05565f65520.png)
" alt="Sublime's custom image"/>
</p>


There are multiple tools for bash to manipulating texts such as sed:
```
NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
```
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226807666-08e7d4be-2131-4b50-87af-1e90a1db92ac.png)
" alt="Sublime's custom image"/>
</p>

Let’s try again: "WORKED!"
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226807712-a331a31f-5254-4ca1-8b94-f3b33aec4aaf.png)
" alt="Sublime's custom image"/>
</p>



2-	For database creation, go to db-create and add the following:
```
#! /usr/bin/bash

echo "db-create"
NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
psql $NO_DB_CONNECTION_URL -c "create database cruddur;"

```

WORKED!!
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226807793-caa06130-cf34-4742-86ed-89d9411fa2c2.png)
" alt="Sublime's custom image"/>
</p>


3-	For schema extension add the below:
```
#! /usr/bin/bash

echo "db-schema-load"

schema_path="$(realpath .)/db/schema.sql"
echo $schema_path

psql $CONNECTION_URL cruddur < $schema_path

```

But notice that if we are not in the correct path, we will get an error “No such file or directory”
So, here we will use something called Real Path, 


'it is only works when we are in backend-flask directory.'
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226807914-5faba76d-46a0-40d2-9b0a-891b2c1db271.png)
" alt="Sublime's custom image"/>
</p>


Now to make a choice where to add the schema (prod or local) we should add the below changes:
```
#! /usr/bin/bash

echo "db-schema-load"
schema_path="$(realpath .)/db/schema.sql"
echo $schema_path

if [ "$1" = "prod" ]; then
  echo "using production"
  CON_URL=$PROD_CONNECTION_URL
else
  CON_URL=$CONNECTION_URL
fi

psql $CON_URL cruddur < $schema_path

```

test it: worked, but because we still not running the production RDS postgres.
<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



4-	Create a new file called db-connect:

```
#! /usr/bin/bash
psql $CONNECTION_URL

```

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226808155-473136e5-4431-4a3c-a064-63c6f3b22f1e.png)
" alt="Sublime's custom image"/>
</p>


## Adding some colors in our echo 

Use the below commands:
```
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-schema-load"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

```

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226808069-359e0017-7f2b-4f8f-983a-243ab9f0c793.png)
" alt="Sublime's custom image"/>
</p>

## Setup tables in schema.sql

Go to schema.sql and add the following codes:

```
CREATE TABLE public.users (
  uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  display_name text,
  handle text,
  cognito_user_id text,
  created_at TIMESTAMP default current_timestamp NOT NULL
);

```

```
CREATE TABLE public.activities (
  uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  message text NOT NULL,
  replies_count integer DEFAULT 0,
  reposts_count integer DEFAULT 0,
  likes_count integer DEFAULT 0,
  reply_to_activity_uuid integer,
  expires_at TIMESTAMP,
  created_at TIMESTAMP default current_timestamp NOT NULL
);

```

Add also:

```
DROP TABLE IF EXISTS public.users;
DROP TABLE IF EXISTS public.activities;
```

And let’s try to run it:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226808351-958519fc-8a60-47c9-a5e7-f2a712aca506.png)
" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226808368-e0eb6855-19cc-4b71-9b08-b77fdbf35b14.png)
" alt="Sublime's custom image"/>
</p>


## Add Database Seed file

Go to db and create seed.sql, and add on your schema.

```

```
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226808761-c12b386a-0a13-4d02-9acf-7e0052ccb3db.png)
" alt="Sublime's custom image"/>
</p>



## See what connections we are using:

We will go and create in bin a new file called db-sessions
```
#! /usr/bin/bash

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-sessions"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

if [ "$1" = "prod" ]; then
  echo "using production"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL

fi

NO_DB_URL=$(sed 's/\/cruddur//g' <<<"$URL")
psql $NO_DB_URL -c "select pid as process_id, \
       usename as user,  \
       datname as db, \
       client_addr, \
       application_name as app,\
       state \
from pg_stat_activity;"

```
```
chmod u+x bin/db-sessions 

./bin/db-sessions

```

now we can see all the connections of the database:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/226809296-90e0db88-5681-43cb-a5c7-c0e9c167aa34.png)
" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>
