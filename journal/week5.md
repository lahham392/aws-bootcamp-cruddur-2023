# Week 5 — DynamoDB and Serverless Caching



<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226055-9eb84845-5d31-4c80-9a5f-c01c7c7c7939.png" alt="Sublime's custom image"/>
</p> This message group are created when you are talk to any user from your application (like twitter one to one), but the difference here that we will make it more flexible to add other users in the same message_group.
<pre>






</pre>
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226166-8bb7fcfd-0380-46ee-b02f-56b4607b383b.png" alt="Sublime's custom image"/>
</p>
When you click to the conversation, we going to have the message conversation, in this message we have the display username, created time, and the message itself.
<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226332-a3fdcd22-962f-4b2b-8e37-b6477a907154.png" alt="Sublime's custom image"/>
</p>
The idea is to update the backend server with the last messages, then in backend the message_group will be updated the last message, the last reply time, and the user.

Using this high level diagram, is important to make all plan clear, and to show what data I need, when do I need it, and how quickly do I need it.

## Deploying patterns 

### Installing Boto3

AWS SDK for Python used to create, configure, and manage AWS services such as EC2, and S3. Also, SDK provide an object oriented API as well as low-level access to AWS services.

* Go to requirements.txt file and add “boto3”.
* Back to your terminal and shift to backend-flask directory.
* Run following command “pip install -r requirements.txt”
* Make Docker compose up (we need all the containers run because we want to deal with DynamoDB local).


### Clean some staff

Here we are just renaming some of our pash scripts to make it more organized. Because we are going to add more scripts for DynamoDB.
Under /bin
/db for PostgreSQL
/ddb for DynamoDB staff
/rds for the RDS

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226564-8e658e26-77b1-405c-ae00-711a06065d8f.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226571-f2b49b6a-6bc8-4c51-85f6-41fc1ddf360f.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226578-be08d57a-92f7-4bfb-96c7-3e7b0b9d866c.png" alt="Sublime's custom image"/>
</p>

### Creating schema for DDB

In Dynamodb, we don’t create database we create a tables, so we jumped to create a schema, make a file and call it “schema-load”.

```sh
#!/usr/bin/env python3

import boto3
import sys

attrs = {
  'endpoint_url': 'http://localhost:8000'
}

if len(sys.argv) == 2:
  if "prod" in sys.argv[1]:
    attrs = {}

dynamodb = boto3.client('dynamodb', **attrs)
table_name = 'cruddur-messages'

# define the schema for the table
table_schema = {
  'AttributeDefinitions': [
    {
      'AttributeName': 'message_group_uuid',
      'AttributeType': 'S'
    },{
      'AttributeName': 'pk',
      'AttributeType': 'S'
    },{
      'AttributeName': 'sk',
      'AttributeType': 'S'
    }
  ],
  'KeySchema': [{
      'AttributeName': 'pk',
      'KeyType': 'HASH'
    },{
      'AttributeName': 'sk',
      'KeyType': 'RANGE'
    }
  ],
  'BillingMode': 'PROVISIONED',
  'ProvisionedThroughput': {
      'ReadCapacityUnits': 5,
      'WriteCapacityUnits': 5
  },
  'GlobalSecondaryIndexes':[{
    'IndexName':'message-group-sk-index',
    'KeySchema':[{
      'AttributeName': 'message_group_uuid',
      'KeyType': 'HASH'
    },{
      'AttributeName': 'sk',
      'KeyType': 'RANGE'
    }],
    'Projection': {
      'ProjectionType': 'ALL'
    },
    'ProvisionedThroughput': {
      'ReadCapacityUnits': 5,
      'WriteCapacityUnits': 5
    },
  }]
}

# create the table
response = dynamodb.create_table(
    TableName=table_name,
    **table_schema
)

# print the response
print(response)


```


make sure to un-comment DynamoDB stuff in the DockerCompose:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226676-52c2eba9-bc90-43ee-abed-782e1c0933e1.png" alt="Sublime's custom image"/>
</p>
<pre>






</pre>

Run again the Docker-compose file (notice port 8000 is running):
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226689-505c7a97-1afe-4456-b68c-65eb2b06fd47.png" alt="Sublime's custom image"/>
</p>

* Change execution mode, chmod u+x bin/ddb
* To check if the local ddb is work run “./bin/ddb/schema-load”. We can notice it outputted the table. 

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226715-52191d9b-3088-470a-90e7-9914afd5b040.png" alt="Sublime's custom image"/>
</p>


### Creating list-tables

If we want to list out our tables, here what we are going to do is a bash script inside “list-tables” file.
<pre>


</pre>

Add the below script:
```sh
#! /usr/bin/bash

if [ "$1" = "prod" ]; then
  ENDPOINT_URL=""
else 
  ENDPOINT_URL="--endpoint-url=http://localhost:8000"
fi
aws dynamodb list-tables $ENDPOINT_URL

```

* Change the mode to executable using “chmod u+x bin/ddb/list-tables”
* Run it and see if it works using “./bin/ddb/list-tables”

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226803-90f3f45f-b073-4bdd-898e-22baa91bb739.png" alt="Sublime's custom image"/>
</p>
<pre>






</pre>

To make it as a table add the below:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226831-f42a520d-2ed3-47ef-86d8-f154b3e6fc8d.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226834-48833754-3d6b-4835-bed9-667f7c9ac958.png" alt="Sublime's custom image"/>
</p>
<pre>






</pre>
To remove TableNames tag add:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226864-d76942f5-2021-44d7-8645-e773b2cc6294.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226870-02931876-51ff-4db8-be10-945f461225fa.png" alt="Sublime's custom image"/>
</p>

### Drop table for DDB

Here we are making a bash script to delete a table from Dynamodb. The code is written below:
* Change the mode to executable using “chmod u+x bin/ddb/drop”
* Run it and see if it works using “./bin/ddb/drop”

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226905-7667ad54-187a-4543-aeda-904b4ed3d60e.png" alt="Sublime's custom image"/>
</p>
<pre>






</pre>

After deleting:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226925-589dad56-04b8-4a40-8cd0-4f6e14cdb3fc.png" alt="Sublime's custom image"/>
</p>

### Creating seed for DDB
In seed we basically will be our way of working through these tables and seeing what to get. 
* Make the file executable chmod u+x bin/ddb/seed

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226949-8f6a2445-980f-4539-bcf2-f32d61d48191.png" alt="Sublime's custom image"/>
</p>
<pre>






</pre>

Let’s understand the full code before copy it:


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226970-321268cf-fc8a-467b-b22e-0e330df1bf5f.png" alt="Sublime's custom image"/>
</p>
This conversation is a test data “simulated conversation to test our code”.
<pre>






</pre>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232226987-cc910210-931a-4099-aa8b-1b56d1f9eecf.png" alt="Sublime's custom image"/>
</p>
Convert above conversation to a lines, its takes the conversation and strips out any new line in the beginning, and strips out any trailing lines, and it will end up into an array for each of line as a cell.

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227010-96f1bf03-1992-43db-8350-e175c6155077.png" alt="Sublime's custom image"/>
</p>
Now we need to iterate through for loop,
* Line 1: For the length of line, how many lines are there? Iterate them and store it in the variable i. “to distinguish between person one and person two.”
* Lines 2,3,4: for the first line, if it starts with string “Person 1”, then we are going to strip out “Persion 1” part from it and assign the key to say this is who “the key is my_user”.
* Lines 6,7,8: is the else part, if it start with Person 2 replace it with the second key “other_user”.
* Lines 9,10,11: the else to catch the error.

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227049-ff70f921-c88e-47ac-97e6-4509db279ffe.png" alt="Sublime's custom image"/>
</p>
Here we are going to create a new function to create a message.
So when we are making a new function, always I write what I want the function to do then we fill it. Now we should ask ourselves if I am creating a new person I need, the “client” ‘which is DynamoDB’, “message”also we know that we need to pass a message along, also we need to know who the user is so use “user_uuid”, also we want to know the display name “display_name”, also the massege belong which group “message_group_id”

Line 1: we need to call the timing function called ‘now’ which will give you the exact time. Then we add to now something called timedelta “which is used to find the time difference between 2 times” and here we add I to make the difference between two iterates 1 minute. So the person1 will send the message and after 1 minute the person2 will replay. (finally it needed to be in iso format)

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227070-34146aa9-9bd8-4e54-920e-91691663fa66.png" alt="Sublime's custom image"/>
</p>
To use now, we should import the following.

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227092-3e51958e-0398-4b99-bbae-01c7b4a7cfdc.png" alt="Sublime's custom image"/>
</p>
We need to define the function.


<pre>






</pre>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227112-d9f1954e-97f9-48c0-84d4-2d355fc30722.png" alt="Sublime's custom image"/>
</p>
The idea is we need to have these 2 users, and for seed data to work it has to come from our database, so it is important to have these 2 records in Cognito and then to have the Cognito user id.
So what we will do is to query our database for those 2 users, and to do that we should bring in lib directory. Our “seed” is sit outside from lib directory.
* In line 1: we find the absolute path of the current file.
* Line 2: then we find the parents path, used (‘..’, ‘..’) to going up for two directories.
* Line 3: we append that path to our paths and then we can reference it locally (append is a pre-defined method used to add a single item to certain collection types.)

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227161-c14438ef-d76e-46a5-ab0a-e65363c859d0.png" alt="Sublime's custom image"/>
</p>
We are going to use SDK and we need to conditionally set. Like what we did before to select the prod db.
<pre>






</pre>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227181-604dfecf-c88e-4c4c-a945-ee1388707bd6.png" alt="Sublime's custom image"/>
</p>
So, what we are going to need to do is get the users uuid from the database, and the idea of this that if we know the names of the handles of the users we can just pass that in the query and then get back the uuid which is the information that we need.
<pre>






</pre>

We should make sure that our data is inserted on the database before.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227213-7fc818d2-3860-4a49-a0f6-e9271e355233.png" alt="Sublime's custom image"/>
</p>
<pre>






</pre>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227227-e5923e1d-3a94-4700-b614-0e7f6a6cd15d.png" alt="Sublime's custom image"/>
</p>
To query the users, so when we pass in these two handles, we should get back all their relevant data such as (uuid, display name, etc).
<pre>






</pre>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227355-3ab896d1-2427-451d-990a-a86c7866fc84.png" alt="Sublime's custom image"/>
</p>
This just to massaging into that shape. In the return dictionary look for the item handle, so if this handle matched andrewbrown return it, and then save it in my_user variable.

<pre>






</pre>

Lets run and test: (We can notice that the data was returned successfully.)
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227376-29eea77d-96e7-4fde-a568-3bac25b1638c.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227632-731be5f1-ff5a-4dac-8066-1a80226c3d03.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227634-606ec574-cd56-4ed4-a5cf-462686dee838.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227644-72ab6253-fb8f-49f5-bdf0-1fff555cc836.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227652-eb166d2b-2e5b-4f0f-8a66-c4a40fe5b4ba.png" alt="Sublime's custom image"/>
</p>
To create a message group.
<pre>






</pre>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227688-37d27e1d-5160-41f1-8276-87dd1b505a42.png" alt="Sublime's custom image"/>
</p>
Create the function to create the message_group, and lets make a comparison between this function and our data model:
<pre>






</pre>


Lets load the data into our Dynamodb table “cruddur-messages” and see, first run your schema file, and after that seed:
Now all data loaded to the table.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227730-a0c2ce96-5ff9-4e3a-8c05-d61ec2d5de13.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

### How to check if it there? By creating a scan


Go to ddb and create a file call it Scan:

```sh
#!/usr/bin/env python3

import boto3

attrs = {
  'endpoint_url': 'http://localhost:8000'
}
dynamodb = boto3.resource('dynamodb',**attrs)
table_name = 'cruddur-messages'

table = dynamodb.Table(table_name)
response = table.scan()
items = response['Items']
for item in items:
    print(item)

```
<pre>






</pre>

change mode and run it: (we are getting the data)

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227800-9e2ac709-aaa4-4cce-a41d-9ff70433c439.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

Now lets query the data in a structure we want. Create a new folder in ddb and call it ‘pattern’ and we will create 2 patterns:

1- get-conversation:

```sh
#!/usr/bin/env python3

import boto3
import sys
import json

attrs = {
  'endpoint_url': 'http://localhost:8000'
}

if len(sys.argv) == 2:
  if "prod" in sys.argv[1]:
    attrs = {}

dynamodb = boto3.client('dynamodb',**attrs)
table_name = 'cruddur-messages'

brown_user_uuid = "1506fa16-d775-4808-9f46-456661029af2"

# define the query parameters
query_params = {
  'TableName': table_name,
  'KeyConditionExpression': 'pk = :pkey',
  'ExpressionAttributeValues': {
    ':pkey': {'S': f"GRP#{brown_user_uuid}"}
  },
  'ReturnConsumedCapacity': 'TOTAL'
}

# query the table
response = dynamodb.query(**query_params)

# print the items returned by the query
print(json.dumps(response, sort_keys=True, indent=2))


```


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227852-0b3b23fd-3dfe-44a3-813e-1ef2f799255b.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227864-c3bf889b-ea5b-4bce-aa1d-1947aee35b72.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

We need to show the data oldest to the newest add:


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227886-97939c77-4441-4e47-9955-7493897aa854.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227890-06f35347-5108-4788-b6ba-1eab129c2772.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

### How to be filtering the queries:

We are trying to make a way of getting the conversation (for each year) or (between certain dates).


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227912-0e02702d-c4ee-4ae6-bce1-d1e114633f39.png" alt="Sublime's custom image"/>
</p>

OR

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227916-2ecca584-1a7e-47f9-9aab-0de70264d454.png" alt="Sublime's custom image"/>
</p>


<pre>






</pre>


2- list-conversation:

```sh
#!/usr/bin/env python3
import boto3
import sys
import json
attrs = {
  'endpoint_url': 'http://localhost:8000'
}

if len(sys.argv) == 2:
  if "prod" in sys.argv[1]:
    attrs = {}
dynamodb = boto3.client('dynamodb',**attrs)
table_name = 'cruddur-messages'
brown_user_uuid = "1506fa16-d775-4808-9f46-456661029af2"

# define the query parameters
query_params = {
  'TableName': table_name,
  'KeyConditionExpression': 'pk = :pkey',
  'ExpressionAttributeValues': {
    ':pkey': {'S': f"GRP#{brown_user_uuid}"}
  },
  'ReturnConsumedCapacity': 'TOTAL'
}
# query the table
response = dynamodb.query(**query_params)
# print the items returned by the query
print(json.dumps(response, sort_keys=True, indent=2))

```

<pre>






</pre>

the user uuid is hardcoded here, and we should add ours:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227964-51641f63-3619-4125-b654-e8864edfea69.png" alt="Sublime's custom image"/>
</p>


<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227969-8e14305e-0563-440b-8512-c509c6ae2e3b.png" alt="Sublime's custom image"/>
</p>


<pre>






</pre>

So, we can see the data 
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232227990-1cb3f594-f100-4e80-b897-a8ec2fa90b7a.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>


To make list-conversation query the uuid instead of hardcode it:

* Go to db.py and add the following:
```sh
def query_value(self,sql,params={}):
    self.print_sql('value',sql,params)

    with self.pool.connection() as conn:
      with conn.cursor() as cur:
        cur.execute(sql,params)
        json = cur.fetchone()
        return json[0]


```



<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228080-f95b6f3b-f72d-4427-8cd2-f1a90bb3c76d.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

* Add to your code the below function:

```sh
def get_my_user_uuids():
  sql = """
    SELECT 
      users.uuid,
    FROM users
    WHERE
      users.handle =%(handle)s,
  """
  uuid = db.query_value(sql,{
    'handle':  'andrewbrown',
  })
  return uuid
  print(f"mu-uuid: {my_user_uuid}")
my_user_uuid = get_my_user_uuids()


```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228226-48a8c54a-5f4c-48b7-a069-9fba94d9fa8a.png" alt="Sublime's custom image"/>
</p>
it is print out the template and then passes the parameters as a second one and print them.


<pre>






</pre>

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228397-7901bc89-e12b-4b43-b7b7-6798e2646952.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228424-cd7f6c83-62a8-45df-846d-b6dbf024ab51.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228445-f477500f-22e4-49f0-a519-1f4c062991fe.png" alt="Sublime's custom image"/>
</p>

### Updating the requirements.txt automatically

* Go to gitpod.yaml and add the below code:

```sh
- name: flask
    command: |
      cd backend-flask
      pip install -r requirements.txt

```
<pre>






</pre>

### fix some bugs:

when we start up	Docker compose, and we attempt to do our setup script it will fail because (it will attempt to drop a database that we already not exist).

By adding IF EXIST

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228525-5343d1bd-76fb-479b-ae28-93cb4196931d.png" alt="Sublime's custom image"/>
</p>


### Reflect the changes to the App

* Now go and start the containers:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228556-b5892aa0-8a7a-4723-bf12-6005443d71ec.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

* Log in:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228579-eb6504cd-38be-4838-b442-8eef5e279649.png" alt="Sublime's custom image"/>
</p>


<pre>






</pre>

* Go to messages tap:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228599-ac87765a-660f-47dc-86f7-bcbd11d0d37c.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>


Go to lib and create ddb.py add the code:

```sh
import boto3
import sys
from datetime import datetime, timedelta, timezone
import uuid
import os

class Ddb:
  def client():
    endpoint_url = os.getenv("AWS_ENDPOINT_URL")
    if endpoint_url:
      attrs = { 'endpoint_url': endpoint_url }
    else:
      attrs = {}
    dynamodb = boto3.client('dynamodb',**attrs)
    return dynamodb

  def list_message_groups(client,my_user_uuid):
    table_name = 'cruddur-messages'
    query_params = {
      'TableName': table_name,
      'KeyConditionExpression': 'pk = :pkey',
      'ScanIndexForward': False,
      'Limit': 20,
      'ExpressionAttributeValues': {
        ':pkey': {'S': f"GRP#{my_user_uuid}"}
      }
    }
    print('query-params')
    print(query_params)
    print('client')
    print(client)

    # query the table
    response = client.query(**query_params)
    items = response['Items']
    
    results = []
    for item in items:
      last_sent_at = item['sk']['S']
      results.append({
        'uuid': item['message_group_uuid']['S'],
        'display_name': item['user_display_name']['S'],
        'handle': item['user_handle']['S'],
        'message': item['message']['S'],
        'created_at': last_sent_at
      })
    return results

  def list_messages(client,message_group_uuid):
    table_name = 'cruddur-messages'
    query_params = {
      'TableName': table_name,
      'KeyConditionExpression': 'pk = :pkey',
      'ScanIndexForward': False,
      'Limit': 20,
      'ExpressionAttributeValues': {
        ':pkey': {'S': f"MSG#{message_group_uuid}"}
      }
    }

    response = client.query(**query_params)
    items = response['Items']
    
    results = []
    for item in items:
      created_at = item['sk']['S']
      results.append({
        'uuid': item['message_uuid']['S'],
        'display_name': item['user_display_name']['S'],
        'handle': item['user_handle']['S'],
        'message': item['message']['S'],
        'created_at': created_at
      })
    return results
  # creates message_group and message
  def create_message_group(client, message,my_user_uuid, my_user_display_name, my_user_handle, other_user_uuid, other_user_display_name, other_user_handle):
    table_name = 'cruddur-messages'
    
    message_group_uuid = str(uuid.uuid4())
    message_uuid = str(uuid.uuid4())
    now = datetime.now(timezone.utc).astimezone().isoformat()
    last_message_at = now
    created_at = now

    message_group = {
      'pk': {'S': f"GRP#{my_user_uuid}"},
      'sk': {'S': last_message_at},
      'message_group_uuid': {'S': message_group_uuid},
      'message': {'S': message},
      'user_uuid': {'S': other_user_uuid},
      'user_display_name': {'S': other_user_display_name},
      'user_handle':  {'S': other_user_handle}
    }

    message = {
      'pk':   {'S': f"MSG#{message_group_uuid}"},
      'sk':   {'S': created_at },
      'message': {'S': message},
      'message_uuid': {'S': message_uuid},
      'user_uuid': {'S': my_user_uuid},
      'user_display_name': {'S': my_user_display_name},
      'user_handle': {'S': my_user_handle}
    }

    items = {
      table_name: [
        {'Put': {'Item': message_group}},
        {'Put': {'Item': message}}
      ]
    }
    return {
      'message_group_uuid': message_group_uuid,
      'uuid': my_user_uuid,
      'display_name': my_user_display_name,
      'handle':  my_user_handle,
      'message': message,
      'created_at': created_at
    }

    try:
      # Begin the transaction
      with dynamodb_resource.meta.client.transact_write_items(RequestItems=items) as transaction:
        print('Transaction started.')
        # Commit the transaction
        response = transaction.commit()
        print('Transaction committed.')
        print(response)
    except ClientError as e:
      # Handle any errors
      print(e)

  def create_message(client,message_group_uuid, message, my_user_uuid, my_user_display_name, my_user_handle):
    now = datetime.now(timezone.utc).astimezone().isoformat()
    created_at = now
    message_uuid = str(uuid.uuid4())

    record = {
      'pk':   {'S': f"MSG#{message_group_uuid}"},
      'sk':   {'S': created_at },
      'message': {'S': message},
      'message_uuid': {'S': message_uuid},
      'user_uuid': {'S': my_user_uuid},
      'user_display_name': {'S': my_user_display_name},
      'user_handle': {'S': my_user_handle}
    }
    # insert the record into the table
    table_name = 'cruddur-messages'
    response = client.put_item(
      TableName=table_name,
      Item=record
    )
    # print the response
    print(response)
    return {
      'message_group_uuid': message_group_uuid,
      'uuid': my_user_uuid,
      'display_name': my_user_display_name,
      'handle':  my_user_handle,
      'message': message,
      'created_at': created_at
    }


```

#### Code explanation:



<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228674-30f210d9-23df-402b-97dd-a96b058ac5f2.png" alt="Sublime's custom image"/>
</p>
Needed import statements.

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228685-41dd2429-7686-4771-b773-c4267175f851.png" alt="Sublime's custom image"/>
</p>
Bringing the class definition and client

<pre>






</pre>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228713-9ceebda3-2d94-424e-bbfa-661a87f97129.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228716-8a634867-5826-404c-b161-3dbdb0313e6d.png" alt="Sublime's custom image"/>
</p>

This code list message groups.
* Line 1 we create the function that will list the messages group.
* Line 2 we hardcoded the table name.
* Line 3-15 this like what we did in list-conversations file, we query the data from dynamodb and same them in variables.

<pre>






</pre>


### Go to app.py and lets start calling ‘ddb.py’ code:


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228776-8eac7bea-084a-42ee-9da0-53feb5c7fc73.png" alt="Sublime's custom image"/>
</p>

Notice that we hardcoded the user handle, here will grap all these values automatically. In Cognito we have something called User Id ‘which is represent uuid’, so we will use it to extract all relevant information that we need.
<pre>






</pre>
To match up a user from their Cognito used id, is that we need to store that id in our database. 
And if we check our seed, we have MOCK data here and the idea is that we need actually populate this with the actual Cognito user id to get the additional information like handle.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228820-9cbd173e-4bc2-4c47-ac3c-3e2451566ded.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

* Go to /bin and create a new folder call it cognito, and create a file call it list_users…

* In this we are going to make SDK to call cognito to find the user id and put it in the seed.

**Before that make sure to save the cogntio user pool id**

```sh
export AWS_COGNITO_USER_POOL_ID= “us-east-1_yl***4Ip”
gp env AWS_COGNITO_USER_POOL_ID= “us-east-1_yl***l4Ip”

```
<pre>






</pre>

Update your docker compose file with this new env, because it will be used in the back-end:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232228949-07b1b9a5-54f8-4f26-a5a2-597ad6fef2a1.png" alt="Sublime's custom image"/>
</p>

add the following code:

```sh
#!/usr/bin/env python3

import boto3
import os
import json

userpool_id = os.getenv("AWS_COGNITO_USER_POOL_ID")
client = boto3.client('cognito-idp')
params = {
  'UserPoolId': userpool_id,
  'AttributesToGet': [
      'preferred_username',
      'sub'
  ]
}
response = client.list_users(**params)
users = response['Users']

print(json.dumps(users, sort_keys=True, indent=2, default=str))

dict_users = {}
for user in users:
  attrs = user['Attributes']
  sub    = next((a for a in attrs if a["Name"] == 'sub'), None)
  handle = next((a for a in attrs if a["Name"] == 'preferred_username'), None)
  dict_users[handle['Value']] = sub['Value']

print(dict_users)

```
<pre>






</pre>
change mode and run it: we got what we need…
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229028-eb4eb1dd-8dd1-4928-89ce-c8811cf3c4a7.png" alt="Sublime's custom image"/>
</p>


<pre>






</pre>

New script in order to update those Cognito user ids in our database, so we will make a new script called update Cognito, this file will be in /bin/db:

```sh
#!/usr/bin/env python3

import boto3
import os
import sys

current_path = os.path.dirname(os.path.abspath(__file__))
parent_path = os.path.abspath(os.path.join(current_path, '..', '..'))
sys.path.append(parent_path)
from lib.db import db

def update_users_with_cognito_user_id(handle,sub):
  sql = """
    UPDATE public.users
    SET cognito_user_id = %(sub)s
    WHERE
      users.handle = %(handle)s;
  """
  db.query_commit(sql,{
    'handle' : handle,
    'sub' : sub
  })

def get_cognito_user_ids():
  userpool_id = os.getenv("AWS_COGNITO_USER_POOL_ID")
  client = boto3.client('cognito-idp')
  params = {
    'UserPoolId': userpool_id,
    'AttributesToGet': [
        'preferred_username',
        'sub'
    ]
  }
  response = client.list_users(**params)
  users = response['Users']
  dict_users = {}
  for user in users:
    attrs = user['Attributes']
    sub    = next((a for a in attrs if a["Name"] == 'sub'), None)
    handle = next((a for a in attrs if a["Name"] == 'preferred_username'), None)
    dict_users[handle['Value']] = sub['Value']
  return dict_users

users = get_cognito_user_ids()

for handle, sub in users.items():
  print('----',handle,sub)
  update_users_with_cognito_user_id(
    handle=handle,
    sub=sub
  )


```

<pre>






</pre>


Reflect the changes to the setup folder:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229126-c96ca72e-c365-4a45-8aba-ce7b42ab9b7d.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

Change the mode of the file.
Run setup to seed the data, error is shown!

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229146-4befb1e7-66c9-40f4-88be-b3e9135998a3.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

Add the below to db.py:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229173-7de528e9-6666-4fd7-bb44-b1d2cc432d07.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

Now when run the file alone we can see that it passes the data.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229372-2ef60597-bed9-46dd-bb36-4cff1f697dbf.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

Now back to app.py to edit the hardcoded user handle:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229388-67338da3-6707-4fb9-9541-e8e38792559c.png" alt="Sublime's custom image"/>
</p>

To solve this issue in our Postgresql database, and to resolve this issue change the line in setup from
source "$bin_path/db/update_cognito_user_ids"

to

python "$bin_path/db/update_cognito_user_ids"
The error is caused by trying to run a Python script as a Bash script.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229430-0bb60de1-dfcf-400d-857c-cf2d28c6a6b3.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229440-b2b13303-107d-4282-946a-b1074ea8b436.png" alt="Sublime's custom image"/>
</p>


<pre>






</pre>

In our table in database:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229462-45ffa1fe-e56a-4734-8713-ff3cd7d3cad4.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

Also make sure to add AWS endpoint url as an environment variable in the Docker Compose file:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229475-fce69764-3e32-4b03-9f88-082021f90b12.png" alt="Sublime's custom image"/>
</p>

<pre>






</pre>

Now we can see some messages from our DynamoDB are shown:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/232229501-96e64d1b-2562-4df3-a452-149fa6ffcf20.png" alt="Sublime's custom image"/>
</p>

page 275
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
