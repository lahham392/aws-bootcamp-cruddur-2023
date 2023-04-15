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
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226332-a3fdcd22-962f-4b2b-8e37-b6477a907154.png)
" alt="Sublime's custom image"/>
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
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226564-8e658e26-77b1-405c-ae00-711a06065d8f.png)
" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226571-f2b49b6a-6bc8-4c51-85f6-41fc1ddf360f.png)
" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226578-be08d57a-92f7-4bfb-96c7-3e7b0b9d866c.png)
" alt="Sublime's custom image"/>
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
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226676-52c2eba9-bc90-43ee-abed-782e1c0933e1.png)
" alt="Sublime's custom image"/>
</p>
<pre>






</pre>

Run again the Docker-compose file (notice port 8000 is running):
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226689-505c7a97-1afe-4456-b68c-65eb2b06fd47.png)
" alt="Sublime's custom image"/>
</p>

* Change execution mode, chmod u+x bin/ddb
* To check if the local ddb is work run “./bin/ddb/schema-load”. We can notice it outputted the table. 

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226715-52191d9b-3088-470a-90e7-9914afd5b040.png)
" alt="Sublime's custom image"/>
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
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226803-90f3f45f-b073-4bdd-898e-22baa91bb739.png)
" alt="Sublime's custom image"/>
</p>
<pre>






</pre>

To make it as a table add the below:

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226831-f42a520d-2ed3-47ef-86d8-f154b3e6fc8d.png)
" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226834-48833754-3d6b-4835-bed9-667f7c9ac958.png)
" alt="Sublime's custom image"/>
</p>
<pre>






</pre>
To remove TableNames tag add:

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226864-d76942f5-2021-44d7-8645-e773b2cc6294.png)
" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226870-02931876-51ff-4db8-be10-945f461225fa.png)
" alt="Sublime's custom image"/>
</p>

### Drop table for DDB

Here we are making a bash script to delete a table from Dynamodb. The code is written below:
* Change the mode to executable using “chmod u+x bin/ddb/drop”
* Run it and see if it works using “./bin/ddb/drop”

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226905-7667ad54-187a-4543-aeda-904b4ed3d60e.png)
" alt="Sublime's custom image"/>
</p>
<pre>






</pre>

After deleting:

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226925-589dad56-04b8-4a40-8cd0-4f6e14cdb3fc.png)
" alt="Sublime's custom image"/>
</p>

### Creating seed for DDB
In seed we basically will be our way of working through these tables and seeing what to get. 
* Make the file executable chmod u+x bin/ddb/seed

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226949-8f6a2445-980f-4539-bcf2-f32d61d48191.png)
" alt="Sublime's custom image"/>
</p>
<pre>






</pre>

Let’s understand the full code before copy it:


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226970-321268cf-fc8a-467b-b22e-0e330df1bf5f.png)
" alt="Sublime's custom image"/>
</p>
This conversation is a test data “simulated conversation to test our code”.
<pre>






</pre>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232226987-cc910210-931a-4099-aa8b-1b56d1f9eecf.png)
" alt="Sublime's custom image"/>
</p>
Convert above conversation to a lines, its takes the conversation and strips out any new line in the beginning, and strips out any trailing lines, and it will end up into an array for each of line as a cell.

<pre>






</pre>

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227010-96f1bf03-1992-43db-8350-e175c6155077.png)
" alt="Sublime's custom image"/>
</p>
Now we need to iterate through for loop,
* Line 1: For the length of line, how many lines are there? Iterate them and store it in the variable i. “to distinguish between person one and person two.”
* Lines 2,3,4: for the first line, if it starts with string “Person 1”, then we are going to strip out “Persion 1” part from it and assign the key to say this is who “the key is my_user”.
* Lines 6,7,8: is the else part, if it start with Person 2 replace it with the second key “other_user”.
* Lines 9,10,11: the else to catch the error.

<pre>






</pre>

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227049-ff70f921-c88e-47ac-97e6-4509db279ffe.png)
" alt="Sublime's custom image"/>
</p>
Here we are going to create a new function to create a message.
So when we are making a new function, always I write what I want the function to do then we fill it. Now we should ask ourselves if I am creating a new person I need, the “client” ‘which is DynamoDB’, “message”also we know that we need to pass a message along, also we need to know who the user is so use “user_uuid”, also we want to know the display name “display_name”, also the massege belong which group “message_group_id”

Line 1: we need to call the timing function called ‘now’ which will give you the exact time. Then we add to now something called timedelta “which is used to find the time difference between 2 times” and here we add I to make the difference between two iterates 1 minute. So the person1 will send the message and after 1 minute the person2 will replay. (finally it needed to be in iso format)

<pre>






</pre>

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227070-34146aa9-9bd8-4e54-920e-91691663fa66.png)
" alt="Sublime's custom image"/>
</p>
To use now, we should import the following.

<pre>






</pre>

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227092-3e51958e-0398-4b99-bbae-01c7b4a7cfdc.png)
" alt="Sublime's custom image"/>
</p>
We need to define the function.


<pre>






</pre>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227112-d9f1954e-97f9-48c0-84d4-2d355fc30722.png)
" alt="Sublime's custom image"/>
</p>
The idea is we need to have these 2 users, and for seed data to work it has to come from our database, so it is important to have these 2 records in Cognito and then to have the Cognito user id.
So what we will do is to query our database for those 2 users, and to do that we should bring in lib directory. Our “seed” is sit outside from lib directory.
* In line 1: we find the absolute path of the current file.
* Line 2: then we find the parents path, used (‘..’, ‘..’) to going up for two directories.
* Line 3: we append that path to our paths and then we can reference it locally (append is a pre-defined method used to add a single item to certain collection types.)

<pre>






</pre>

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227161-c14438ef-d76e-46a5-ab0a-e65363c859d0.png)
" alt="Sublime's custom image"/>
</p>
We are going to use SDK and we need to conditionally set. Like what we did before to select the prod db.
<pre>






</pre>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227181-604dfecf-c88e-4c4c-a945-ee1388707bd6.png)
" alt="Sublime's custom image"/>
</p>
So, what we are going to need to do is get the users uuid from the database, and the idea of this that if we know the names of the handles of the users we can just pass that in the query and then get back the uuid which is the information that we need.
<pre>






</pre>

We should make sure that our data is inserted on the database before.

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227213-7fc818d2-3860-4a49-a0f6-e9271e355233.png)
" alt="Sublime's custom image"/>
</p>
<pre>






</pre>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227227-e5923e1d-3a94-4700-b614-0e7f6a6cd15d.png)
" alt="Sublime's custom image"/>
</p>
To query the users, so when we pass in these two handles, we should get back all their relevant data such as (uuid, display name, etc).
<pre>






</pre>


<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227355-3ab896d1-2427-451d-990a-a86c7866fc84.png)
" alt="Sublime's custom image"/>
</p>
This just to massaging into that shape. In the return dictionary look for the item handle, so if this handle matched andrewbrown return it, and then save it in my_user variable.

<pre>






</pre>

Lets run and test: (We can notice that the data was returned successfully.)
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/232227376-29eea77d-96e7-4fde-a568-3bac25b1638c.png)
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
