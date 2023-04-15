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
  <img src="https://user-images.githubusercontent.com/82225825/226804699-1ab962b3-690c-45b5-9146-aeed1e428027.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226804719-ca4e5490-734b-427a-8188-e887420e816d.png" alt="Sublime's custom image"/>
</p>



Go to console and check if the RDS created or not:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226804758-1ebd3213-1dfa-40e4-8a27-19d500944df9.png" alt="Sublime's custom image"/>
</p>

RDS runs in EC2 instance, and we have ability to stop it “temporarily” and it will be stopped only for 7 days.



<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226804787-cf57e6f8-8dfd-4f3d-8359-a05d04c3831b.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226804812-c4cacbed-a47b-4e15-9a89-d83c4eae6ea2.png" alt="Sublime's custom image"/>
</p>



Now, check your docker compose file if it contains Postgres database definition:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226804834-8625fdda-d06a-40c3-8ea0-88806eb2d076.png" alt="Sublime's custom image"/>
</p>

## Connect to psql via client

Now, let’s make sure that we can access Postgres (make docker compose up) (this actually runs in our Gitpod environment not AWS RDS).
To connect to psql via the psql client cli tool remember to use the host flag to specific localhost.
```
psql -Upostgres --host localhost
```


now as mentioned above, without flaging to the specific host it will not work, because we working in container environment (we want to listen on localhost as supposed to docker):

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226804965-567ecb06-abe8-445c-831c-da80f447a46d.png" alt="Sublime's custom image"/>
</p>

Now after running full command:
```
psql -Upostgres --host localhost
```


Will ask for password: (password):
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226805051-c369b246-9984-45b3-bd2f-6584eaf10a39.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226805060-d9f7f081-f418-4eb6-9782-3f35940f6185.png" alt="Sublime's custom image"/>
</p>


Some common Postgres commands:

1-	\l used to list all databases:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226805129-3abcdde5-86e0-4879-b28a-f6e175b61ef6.png" alt="Sublime's custom image"/>
</p>

2-	\q used to exist from database


3- To drop the database
```
DROP database cruddur;
```

4- \dt To see the created tables
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226808513-b5dbbd0e-35e4-4ca7-b652-f4dae060e051.png" alt="Sublime's custom image"/>
</p>


Now we need to setup some tables, and generally when you have web framework you will have a file called schema.sql, it will contain all the stuff to setup the schema (structure of the database).
Now move to /backend-flask add new folder called db, make new file called schema.sql
Usually, we add in this file the needed extensions in our application. 

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226805322-1579e8b7-0a86-4c08-b1fb-064b1a1fc788.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226805765-4cc7baf3-223b-4e74-93e4-0b148262e3a2.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226805999-30b53fb5-3218-43a8-b452-632922e09bcc.png" alt="Sublime's custom image"/>
</p>
We got connection refuse, then I remember that my docker compose is down!




<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806038-74bde5d0-e7e0-423f-a66e-88d62d24b602.png" alt="Sublime's custom image"/>
</p>
Another error, which is my password does not correct.



<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806077-e7e09523-dd66-4092-bc19-e64e66b4a41d.png" alt="Sublime's custom image"/>
</p>
Another error in database name.


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806107-2bf283b1-fe8e-4993-94c0-25e92568408c.png" alt="Sublime's custom image"/>
</p>
Connected :D


Now let’s make it as environment variable in my Gitpod environment:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806153-122fee41-1a57-45dc-b291-9bdc7332e4cd.png" alt="Sublime's custom image"/>
</p>

Now to access it write:
```
psql $CONNECTION_URL
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806214-ab9183cf-638e-4f08-b91a-36740cf392c0.png" alt="Sublime's custom image"/>
</p>

I got an error, which I think it is because the comma, I will correct it and then delete old env and create a new one.


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806263-6940e7d0-1983-4f40-ac87-749520b312a0.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226806400-1eebff4e-7761-4b16-a5ed-04f333be26d6.png" alt="Sublime's custom image"/>
</p>

## Bash Scripting

Now we are going to create some scripts to make the create, drop, connection, and schema load to the database automatically.

Go to backend-flask and add a new folder and name its bin, which refers to binary and here we will put our bash script. 

Also add 2 files without extensions, db-create, and db-drop, and db-schema-load.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806698-bad367ba-848d-4869-9d21-ed881c701c10.png" alt="Sublime's custom image"/>
</p>

To know the path of your bash run this command: 
```
whereis bash
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806761-20c38e40-9c24-4723-aa99-a25a11d72d30.png" alt="Sublime's custom image"/>
</p>

Also we need to make all of these are executable by run:
```
chmod u+x bin/db-create
Chmod u+x bin/db-drop
Chmod u+x bin/db-schema-load

```
Before it will be like:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806823-858bcbf3-cebd-4db1-ac99-ab5462f75ff6.png" alt="Sublime's custom image"/>
</p>

After making it executable:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226806846-b105696e-a8f8-48b8-a0d7-d9c9aa448c21.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226807385-29576a0b-5b57-4642-a4f7-382f98e68b21.png" alt="Sublime's custom image"/>
</p>

The reason of that, when we use connection URL we are not only connecting postgres, we are connecting to a specific database ‘cruddur’, so we cant ask it to drop the database as we in it. 

So, we should exclude the database part ‘/cruddur’

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226807621-c8887a54-7f82-44cb-8b47-c05565f65520.png" alt="Sublime's custom image"/>
</p>


There are multiple tools for bash to manipulating texts such as sed:
```
NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226807666-08e7d4be-2131-4b50-87af-1e90a1db92ac.png" alt="Sublime's custom image"/>
</p>

Let’s try again: "WORKED!"
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226807712-a331a31f-5254-4ca1-8b94-f3b33aec4aaf.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226807793-caa06130-cf34-4742-86ed-89d9411fa2c2.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226807914-5faba76d-46a0-40d2-9b0a-891b2c1db271.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226808155-473136e5-4431-4a3c-a064-63c6f3b22f1e.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226808069-359e0017-7f2b-4f8f-983a-243ab9f0c793.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226808351-958519fc-8a60-47c9-a5e7-f2a712aca506.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226808368-e0eb6855-19cc-4b71-9b08-b77fdbf35b14.png" alt="Sublime's custom image"/>
</p>


## Add Database Seed file

Go to db and create seed.sql, and add on your schema.

```

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226808761-c12b386a-0a13-4d02-9acf-7e0052ccb3db.png" alt="Sublime's custom image"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/226809296-90e0db88-5681-43cb-a5c7-c0e9c167aa34.png" alt="Sublime's custom image"/>
</p>


## DB-Setup

Here in same /bin, we will make a shell to setup our database automatically.
```
#! /usr/bin/bash
-e # stop if it fails at any point

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-setup"
printf "${CYAN}====== ${LABEL}${NO_COLOR}\n"

bin_path="$(realpath .)/bin"

source "$bin_path/db-drop"
source "$bin_path/db-create"
source "$bin_path/db-schema-load"
source "$bin_path/db-seed"

```

chmod u+x bin/db-sessions &&& ./bin/db-sessions

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226880886-ffa61a54-5b6e-463d-a7c5-d787ee421899.png" alt="Sublime's custom image"/>
</p>

## Install Postgres Client (Drivers):

Move to backend-flask and go to requirements.txt and add the below:
```
psycopg[binary]
psycopg[pool]

```

then install it:
```
pip install -r requirements.txt
```


## DB Object and Connection Pool

Go to /lib and create db.py
Add:

```
from psycopg_pool import ConnectionPool
import os

def query_wrap_object(template):
  sql = '''
  (SELECT COALESCE(row_to_json(object_row),'{}'::json) FROM (
  {template}
  ) object_row);
  '''

def query_wrap_array(template):
  sql = '''
  (SELECT COALESCE(array_to_json(array_agg(row_to_json(array_row))),'[]'::json) FROM (
  {template}
  ) array_row);
  '''

connection_url = os.getenv("CONNECTION_URL")
pool = ConnectionPool(connection_url)

```

Go to docker compose file and add:
```
CONNECTION_URL: "${CONNECTION_URL}"
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226881343-c7f6d78a-aaee-4faf-98fc-7fb2abca24c5.png" alt="Sublime's custom image"/>
</p>


In our home activities we'll replace our mock endpoint with real api call:
```
with pool.connection() as conn:
        with conn.cursor() as cur:
          cur.execute(sql)
          # this will return a tuple
          # the first field being the data
          json = cur.fetchone()
      return json[0]

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/226881439-de24d825-beb5-463a-a002-03961ac2a58a.png" alt="Sublime's custom image"/>
</p>

Now open your frontend page and go to check logs in backend container.

Now we got following:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227123264-d980c507-df3a-4bef-b7ec-6849fdfd3000.png" alt="Sublime's custom image"/>
</p>

After adding the queries:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227123274-595cca29-37f4-448a-96f2-5a47b0cb1a3d.png" alt="Sublime's custom image"/>
</p>


## Establish a connection to RDS Postgres database.

Here let’s start up RDS instance and make sure that database connection URL works.

We can check if our production connection URL is sace correctly, use below command:
```
Echo $PROD_CONNECTION_URL
```

Try to connect to RDS using:
```
psql $PROD_CONNECTION_URL
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227123510-cf244520-1e47-4538-a41a-0d2c917847f6.png" alt="Sublime's custom image"/>
</p>


As we can see it is hanging, this because the security group, we have to get Gitpod IP address and allow it from the security group of RDS instance:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227123525-a1c79874-a31e-41c7-a06c-b2e397ef8d1a.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227123681-68b1e23e-ae01-43e7-bf4f-7c6f2cbb4e2b.png" alt="Sublime's custom image"/>
</p>

And port and ip address below:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227123719-bd7cc668-e2cc-4de2-a180-f6c6922e72bf.png" alt="Sublime's custom image"/>
</p>

To know Gitpod ip address:
```
Curl ifconfig.me
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227123813-63895502-13d3-4bfe-8bff-12c7032d6de5.png" alt="Sublime's custom image"/>
</p>

To make it easier for finding:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227123843-688addb8-fbd5-44ee-b02b-4ee587e110b5.png" alt="Sublime's custom image"/>
</p>

Now try to connect again:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/227123911-f5389d5d-0a16-4229-8f75-d643c103f9e3.png)
" alt="Sublime's custom image"/>
</p>
It is connected…

Every time when we restart Gitpod environment, the IP address will change, and to make things easier we are going to make a script that updates security groups.

Use following:
```
aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"

```


We need 2 things from AWS environment:

Security Group ID which is sg-0addd****98851
And 
Security Rule Id which is 	sgr-0ee****35bb5ef5


We will make them as an environment variable using:
```
export DB_SG_ID="sg-0addd0d28e1d98851"
gp env DB_SG_ID="sg-0addd0d28e1d98851"
export DB_SG_RULE_ID="sgr-0ee46f23435bb5ef5"
gp env DB_SG_RULE_ID="sgr-0ee46f23435bb5ef5"

```

to apply it execute the following command:
```
aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"

```

To see if the changes applied or not:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/227123978-b49985e4-40a4-446c-b1a4-c5a570248c2b.png)
" alt="Sublime's custom image"/>
</p>

After running the script:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/227123991-96b4bf2e-7a05-45b2-8d72-60d2d5e1df40.png)
" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/227124014-cfc1076c-263e-4ffc-b474-5a272ee77773.png)
" alt="Sublime's custom image"/>
</p>


To add description:
```
aws ec2 modify-security-group-rules \
    --group-id $DB_SG_ID \
    --security-group-rules "SecurityGroupRuleId=$DB_SG_RULE_ID,SecurityGroupRule={Description=GITPOD,IpProtocol=tcp,FromPort=5432,ToPort=5432,CidrIpv4=$GITPOD_IP/32}"

```

Now we need to take effect automatically when we run Gitpod again 
""Go to gitpod.yml""

But first go to /bin and create file called “rds-update-sg-rule” and add the following script:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227124058-50d8c55d-d9be-43f3-b1af-e7d84c08259e.png" alt="Sublime's custom image"/>
</p>

run and check if it works correctly or not:
```
chmod u+x bin/rds-update-sg-rule
```

before:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227124326-f34c0c9d-3c13-4cf8-849a-02c73566e88b.png" alt="Sublime's custom image"/>
</p>

After: "Error"

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227124497-6c9ea87f-706b-43d7-a1f8-1028e39d4885.png" alt="Sublime's custom image"/>
</p>

When we checked the returned value from our script we notice that it doesn’t return the GITPOD_IP value…
We need to export it 
```
export GITPOD_IP=$(curl ifconfig.me)
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227124618-c3bc99ea-cb19-45a1-ad25-2bf860eb53a5.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227124670-81cf259b-9d06-448c-a3e5-7abbd7342761.png" alt="Sublime's custom image"/>
</p>

Now back to gitpod.yml and add:
```
command:
      export GITPOD_IP=$(curl ifconfig.me)
      source  "$THEIA_WORKSPACE_ROOT/backend-flask/bin/rds-update-sg-rule"

```

Now let’s test
Before:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227124818-804ca888-7f46-4b44-b298-b8115792cf5a.png" alt="Sublime's custom image"/>
</p>

Close your Gitpod and re open it again:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227124925-df4cff03-6b24-4f55-89ce-d45bbbd437a0.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227124943-e7585916-d4f9-47fb-823e-da7a7d8975ec.png" alt="Sublime's custom image"/>
</p>

Try to connect to RDS:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227125039-a70768d7-76f4-4e72-9799-0b92ecc7c717.png" alt="Sublime's custom image"/>
</p>

Now we have to add the schema, our application will not work:
Go to docker compose:
And add the following env:
```

```


## Cognito Post Confirmation Lambda

Here we need to create a custom authorizer for Cognito, the reason we need to do this is that when we are setting up our data ‘in seed.sql’ we will notice that we need to have a user for our activities but we also have to have Cognito User ID ‘if we don’t have this then there is no way for us to verify our users’.


### Setup Cognito post confirmation lambda

Here we are going to create a handler function:

1-	Create lambda in same vpc as rds instance Python 3.8

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227125385-0a2649f7-6591-41f5-bd01-6089a70857bb.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227125492-a7815ae9-741c-4ddb-bc16-24cd09fa2076.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227125516-85711236-744a-4388-bfaa-9b8d89190d78.png" alt="Sublime's custom image"/>
</p>

The Function code (put it in /aws/json/lambdas and name it cruddur-post-confirmation.py):
```
import json
import psycopg2

def lambda_handler(event, context):
    user = event['request']['userAttributes']
    print('userAttributes')
    print(user)
    user_display_name = user['name']
    user_email = user['email']
    user_handle = user['preferred_username']
    user_cognito_id = user ['sub']
    try:
        conn = psycopg2.connect(os.getenv('CONNECTION_URL'))
        cur = conn.cursor()

        sql = f"""
          "INSERT INTO users (
            display_name,
            email,
            handle,
            cognito_user_id
            )
          VALUES(
            {user_display_name},
            {user_email},
            {user_handle}
            {user_cognito_id}
          )"
        """
        cur.execute(sql)
        conn.commit() 

    except (Exception, psycopg2.DatabaseError) as error:
        print(error)
        
    finally:
        if conn is not None:
            cur.close()
            conn.close()
            print('Database connection closed.')

    return event

```

Go to your schema.sql: (and add email to it ‘email text;’)

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227125809-2d8ab2bd-c9c4-41d8-8a82-bade149ddecd.png" alt="Sublime's custom image"/>
</p>

Now add the function in Lambda and deploy it:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227125858-024843ed-6215-42f6-a960-9c42f8748b8a.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227125883-e5d4d4b2-7241-491d-8e9d-aee50fd7a9d5.png" alt="Sublime's custom image"/>
</p>

Now set the environment variables:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227125925-8a9585c2-4510-482f-ada5-f2774e3242dc.png" alt="Sublime's custom image"/>
</p>

Add a new one:
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/227126158-c4857455-60f9-4bcf-ace0-05c5834eaf22.png)
" alt="Sublime's custom image"/>
</p>

### Adding Lambda Layer

This is a custom compiled psycopg2 C library for Python. Due to AWS Lambda missing the required PostgreSQL libraries in the AMI image, we needed to compile psycopg2 with the PostgreSQL libpq.so library statically linked libpq library instead of the default dynamic link.


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227126255-f2827283-3e63-4ffe-a948-615089b8f165.png" alt="Sublime's custom image"/>
</p>

And add the following ARN:
```
arn:aws:lambda:us-east-1:898466741470:layer:psycopg2-py38:2
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227126474-a7089d55-62b3-477c-b1f9-9e8580e78fa4.png" alt="Sublime's custom image"/>
</p>


Now we need to add a trigger ‘which is Cognito’:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227126535-128a2dbd-58f7-4f08-b487-bd855830d6b0.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227126666-826db638-fe3c-417b-b1d7-118de911e12e.png" alt="Sublime's custom image"/>
</p>

Now add the new trigger setting:
We want it to happen on sign-up:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227126713-37636ae1-5426-4038-af6c-f382e550e069.png" alt="Sublime's custom image"/>
</p>

We need it to happen on post confirmation:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227126746-f38ab9ba-306e-4289-80e4-e5ef80e303e6.png" alt="Sublime's custom image"/>
</p>

Choose targeted Lambda function:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227126972-a1f7a04f-d908-4841-83fd-42ab7b73cf18.png" alt="Sublime's custom image"/>
</p>

Now to check if it is working well, go to Lambda function console and select Monitor: Select CloudWatch Logs

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127062-81beffda-2d8d-4df2-b91c-2b8edad645fb.png" alt="Sublime's custom image"/>
</p>

Add the required Role in your Lambda to allow the access to the RDS Instance:

```

```


Default role is:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127138-39b30ff4-3362-44a5-9e27-ab7794349655.png" alt="Sublime's custom image"/>
</p>

Click in the role and edit it:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127175-4289bef6-dd0d-4c5c-8461-eef2e2791040.png" alt="Sublime's custom image"/>
</p>

Create a new policy:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127190-2ab093eb-4c5f-4c52-b049-53a62cc26b20.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127225-7dd21c03-d815-4d75-9848-21765d97bf45.png" alt="Sublime's custom image"/>
</p>

Attach the policy to the Role:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127247-fb009956-3013-4d92-8ab9-8a47b9714aa6.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127429-e00d6573-03cd-4cc4-9202-c3f6b5618f2e.png" alt="Sublime's custom image"/>
</p>

Now add the Lambda function in the same VPC of RDS:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127476-6871f651-b98d-4b5e-9fcf-b5195667a8e5.png" alt="Sublime's custom image"/>
</p>

Click Edit:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127572-c7a742be-5686-4e58-9a5f-8785a6f3395e.png" alt="Sublime's custom image"/>
</p>

Now go to your application and Sign-Up!
I got this error:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127593-1c615410-b5ab-4962-9bf1-6ce6c714b148.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127623-f2aae278-4995-4c53-8b6c-3410cbfc93ce.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127739-ba7a0401-a25b-445b-80b0-e6af4f833572.png" alt="Sublime's custom image"/>
</p>

((indentation mistake))
Its works

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127821-f70667d2-9b09-451a-b729-6795340405c1.png" alt="Sublime's custom image"/>
</p>

But still data not shown in the database, and also we got another error in CloudWatch Logs:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227127961-67b43e29-d323-4fc8-9c95-cea1d133c5aa.png" alt="Sublime's custom image"/>
</p>
*******This because the double quote

 Make sure to apply your schema again!!! ###
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227128082-ea771550-f1a0-406a-b530-418bf1a868cd.png" alt="Sublime's custom image"/>
</p>

Now try again:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/227128106-540a47b4-19dc-440d-a17a-482835ddb36b.png" alt="Sublime's custom image"/>
</p>




<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>
