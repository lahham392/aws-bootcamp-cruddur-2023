# Week 6 — Deploying Containers

Amazon Elastic Container Service (ECS) is a fully-managed container orchestration service offered by Amazon Web Services (AWS). ECS is designed to make it easier to run, deploy, and manage containerized applications at scale. One of the key features of ECS is the ability to operate in different network modes, which determine how containers within a task communicate with each other and with the external world.

**There are three network modes in ECS:**

**Bridge network mode:** In this mode, each container within a task gets its own network namespace and IP address. Containers can communicate with each other using the localhost interface, and can communicate with the outside world using the IP address of the host EC2 instance. This network mode is ideal for applications that don't need to be accessed from outside the host EC2 instance.

**Host network mode:** In this mode, each container within a task shares the same network namespace as the host EC2 instance. This means that containers can communicate with each other and with the outside world using the same IP address as the host. This network mode is ideal for applications that need to be accessible from outside the host EC2 instance, and that require high network performance.

**AWS VPC network mode:** In this mode, each task gets its own virtual private cloud (VPC), with its own IP address range. Containers within the task can communicate with each other using the VPC's private IP addresses and can communicate with the outside world using the VPC's internet gateway. This network mode is ideal for applications that require strong network isolation and security, and that need to be accessed from outside the host EC2 instance.
In AWS we can deploy the containers using different services, such as Lambda, Apprunner, ElasticBeanstalk, Fargate, ECS, and EKS. There is a lot of options for deployment.

**What about Fargate?**
Fargate considered as a serverless containers service and the great thing about containers is that you don’t have to manage the underlaying compute, also it is a good migration path for Kubernetes, but unfortunately Fargate doesn’t have a free tier.

**What about ECS ‘EC2’?**
It can be done by deploying our containers on the EC2, ECS can be used on a supported free tier, but we have to manage this instance.

**What is AWS AppMesh?**
It is a service used to set-up service mesh, AWS App Mesh is a service mesh that makes it easy to monitor and control microservices running on AWS. It provides a way to manage and visualize the communication between services within a distributed system, allowing you to identify and troubleshoot issues, and to implement advanced traffic management policies.

**What is AWS Cloud Map?**
AWS Cloud Map is a service discovery service that helps you discover and manage the location of your cloud resources. It enables you to easily register, discover, and manage the location of application resources such as microservices, databases, and other cloud resources within your network.

Cloud Map provides a single place to store and manage service information, including service names, resource IDs, IP addresses, and custom attributes. It also provides a simple API and console interface for managing and discovering services, making it easy for applications to locate the resources they need, even as they scale and change over time.

Cloud Map and App Mesh is extremely complex to configure and use also not recommended to be used.

**To make easy work with these service (Cloud Map, App Mesh):**
* The Service Connect option are features in AWS ECS (Elastic Container Service) for EC2 (Elastic Compute Cloud) instances.
* The Service Connect option in ECS allows you to securely connect your containerized services with other AWS services, such as AWS App Mesh or AWS Cloud Map, by leveraging AWS PrivateLink. With Service Connect, you can expose your containerized services to other services within the same VPC, without exposing them to the public internet or requiring a NAT gateway.
* Using Service Connect, you can create a VPC endpoint for your service and connect it to the other AWS service using the AWS console or the AWS SDK. This allows your containerized services to securely communicate with other services and simplifies the network configuration required to manage your application.

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/7e256186-55c8-4f7b-80ec-5cb2e27819b3"/>
</p>

**S** representing the Service “container”, and this deployed to EC2, and when they checked the logs they found that there is connection issue between the service and Cognito. Dealing with Cognito is by endpoint URL ‘Global URL’. And in the same time our service doesn’t have a public IP to communicate with Cognito.

Why it fail? Because ECS has different Network Mode and no one of these modes are working well with our containers.

To make the service able to talk to internet we can use NAT, so there is a service in AWS called NAT Gateway but the problem it cost money 32$ per month.

**What about NAT Instance?**
But you need to mange everything, also it use only Amazon Linux 1, and not supported by AWS any more.


## Week 6-7 - ECS Fargate (Part 1)

We decided to go with Fargate to manage our containers in AWS, yes it is not in the free tier but the cost of it is acceptable.

### First, we are going to make some health check for our environment resources.

**Test RDS Connection:**
* Go to backend-flask/bin/db/ and create a new file called “test”
* Add the following code:

```sh
#!/usr/bin/env python3

import psycopg
import os
import sys

connection_url = os.getenv("CONNECTION_URL")

conn = None
try:
  print('attempting connection')
  conn = psycopg.connect(connection_url)
  print("Connection successful!")
except psycopg.Error as e:
  print("Unable to connect to the database:", e)
finally:
  conn.close()

```
* Make it executable “chmod u+x backend-flask/bin/db/test”
* Run it

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/9b5a86ef-492d-4c40-b5ad-659d0c73980f"/>
</p>

### Health Check for the Flask App

* Go to App.py
* Add the following code:

```sh
@app.route('/api/health-check')
def health_check():
  return {'success': True}, 200

```
* Now go to make a new script file in /backend-flask/bin/ and call it “flask”
* Add the following code:

```sh
#!/usr/bin/env python3

import urllib.request

response = urllib.request.urlopen('http://localhost:4567/api/health-check')
if response.getcode() == 200:
  print("Flask server is running")
  exit(0)
else:
  print("Flask server is not running")
  exit(1)

```
* Make it executable “chmod u+x backend-flask/bin/flask/health-check”
* Why is exit added here? Is because when we do the health check at the container level it is looking for the exits code.
* For best practice use the below code:

```sh
#!/usr/bin/env python3

import urllib.request

try:
  response = urllib.request.urlopen('http://localhost:4567/api/health-check')
  if response.getcode() == 200:
    print("[OK] Flask server is running")
    exit(0) # success
  else:
print("[BAD] Flask server is not running")
    exit(1) # false
# This for some reason is not capturing the error....
#except ConnectionRefusedError as e:
# so we'll just catch on all even though this is a bad practice
except Exception as e:
  print(e)
  exit(1) # false

```
* Execute the code.

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/9503f689-e112-4dea-a66a-2529637619a0"/>
</p>


### Creating CloudWatch Log Group

Write the following commands:

```
aws logs create-log-group --log-group-name "/cruddur/fargate-cluster"
```

```
aws logs put-retention-policy --log-group-name "/cruddur/fargate-cluster" --retention-in-days 1
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/50a7b689-49f1-4a8d-a371-fd50104835bc"/>
</p>

* Check the console.

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/ea7e9e45-55ba-402a-ab7a-8c55cf72baa4"/>
</p>


### Creating ECS Cluster

Run the following commands:

```sh
aws ecs create-cluster \
--cluster-name cruddur \
--service-connect-defaults namespace=cruddur

```

**What is service-connect?** When we activate it, it is going to make a namespace “which is actually a Cloud Map service” which is a way to dynamically name resources, when you create a namespace in your cloud map you will be able to add services under this namespace and instead of passing the endpoint URL in your connection URL string, you can pass that local namespace instead.

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/a605809d-826b-444b-8f13-54912cc423d0"/>
</p>

Check the console.

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/a69e9842-79b4-4d3d-be31-4bef4ace9d64"/>
</p>

### Preparing our Containers:

#### Create ECR repo and push image

We are going to make 3 different repositories, Python, Flask, and React.

1. Base image for Python:

* Run the following commands:

```sh
aws ecr create-repository \
  --repository-name cruddur-python \
  --image-tag-mutability MUTABLE

```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/c70c3b16-57f0-498e-b620-2c65f8a7b69b"/>
</p>

Check in Console:

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/20eb3068-3242-4b84-b937-6cf03b431069"/>
</p>

* Login to ECR by following command:

```sh
aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com"
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/73b80030-8cc2-4197-b4cf-e8f5194762b3"/>
</p>

* Set the repo URL:

```sh
export ECR_PYTHON_URL="$AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/cruddur-python"

echo $ECR_PYTHON_URL

````

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/9d8378c2-dba0-45ad-83dd-985f3066e9e5"/>
</p>

* Pull the Images:

```sh
docker pull python:3.10-slim-buster
```
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/6993d47a-9247-4f5e-a4f3-a4e86942f05d"/>
</p>

* Tag the image:

```sh
docker tag python:3.10-slim-buster $ECR_PYTHON_URL:3.10-slim-buster
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/db2f877d-0f71-4338-b625-22920ed64b47"/>
</p>

* Push the image:

```sh
docker push $ECR_PYTHON_URL:3.10-slim-buster
```
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/4d2efe51-9c02-4aaf-a9ee-cb32d4b58e95"/>
</p>

* Check your image in AWS console:

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/2e9849cb-339a-4e4c-93be-ff293707ab00"/>
</p>

**In Docker File**
In your flask dockerfile update the from to instead of using DockerHub's python image you use your own eg.

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/f6a71b7a-63c8-4f65-8c9b-be80384932d2"/>
</p>

To check if it runs, make your Docker compose up ‘select services’:

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/6da27269-fa91-44fd-b611-7a4085d6be5b"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/6e306c4a-1ce5-4b0b-8f74-b76058c974e5"/>
</p>

2. Base image for Flask

* Create a repository:

```sh
 aws ecr create-repository \
  --repository-name backend-flask \
  --image-tag-mutability MUTABLE

```

* Set the URL.

```sh
export ECR_BACKEND_FLASK_URL="$AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/backend-flask"

echo $ECR_BACKEND_FLASK_URL

```

* Build the image ‘make sure you are in backend-flask dir’.

```sh
docker build -t backend-flask .
```

* Tag the image

```sh
docker tag backend-flask:latest $ECR_BACKEND_FLASK_URL:latest
```

* Push the image

```sh
docker push $ECR_BACKEND_FLASK_URL:latest
```

3. For Frontend React

(Same what we did above for backend)


#### Deploy it in ECS
Now let’s try to deploy it on our ECS Fargate:

1. Create your parameter store.

* Write the following:

```sh
aws ssm put-parameter --type "SecureString" --name "/cruddur/backend-flask/AWS_ACCESS_KEY_ID" --value $AWS_ACCESS_KEY_ID

aws ssm put-parameter --type "SecureString" --name "/cruddur/backend-flask/AWS_SECRET_ACCESS_KEY" --value $AWS_SECRET_ACCESS_KEY

aws ssm put-parameter --type "SecureString" --name "/cruddur/backend-flask/CONNECTION_URL" --value $PROD_CONNECTION_URL

aws ssm put-parameter --type "SecureString" --name "/cruddur/backend-flask/ROLLBAR_ACCESS_TOKEN" --value $ROLLBAR_ACCESS_TOKEN

aws ssm put-parameter --type "SecureString" --name "/cruddur/backend-flask/OTEL_EXPORTER_OTLP_HEADERS" --value "x-honeycomb-team=$HONEYCOMB_API_KEY"

```
* In Console:
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/9f09021f-c143-488b-bda1-05431bdcd295"/>
</p>

2. Create policy for execution role:

* Make a new file /aws/polices/ and name it ‘service-assume-role-execution-policy.json” 

* Add the following:

```sh
{
  "Version":"2012-10-17",
  "Statement":[{
      "Action":["sts:AssumeRole"],
      "Effect":"Allow",
      "Principal":{
        "Service":["ecs-tasks.amazonaws.com"]
    }}]
}

```

* Again make a new file /aws/polices/ and name it ‘service-execution-policy.json” 

* Add the following:

```sh
{
    "Version":"2012-10-17",
    "Statement":[{
      "Effect": "Allow",
      "Action": [
        "ssm:GetParameters",
        "ssm:GetParameter"
      ],
      "Resource": "arn:aws:ssm:ca-central-1:387543059434:parameter/cruddur/backend-flask/*"
    }]
  }

```
* Pass the policy as Json to your AWS:
```sh
aws iam create-role \
    --role-name CruddurServiceExecutionRole \
    --assume-role-policy-document file://aws/json/policies/service-assume-role-execution-policy.json

```

<p align="center">
  <img src="![image](https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/0024db2f-a0dc-48bf-bf1a-d63cf443efcb)
"/>
</p>

* Put the policy in the role:

```sh
aws iam put-role-policy \
  --policy-name CruddurServiceExecutionPolicy \
  --role-name CruddurServiceExecutionRole \
  --policy-document file://aws/policies/service-execution-policy.json

```

3. Create A Task Role

* Run the following command:

```sh
aws iam create-role \
    --role-name CruddurTaskRole \
    --assume-role-policy-document "{
  \"Version\":\"2012-10-17\",
  \"Statement\":[{
    \"Action\":[\"sts:AssumeRole\"],
    \"Effect\":\"Allow\",
    \"Principal\":{
      \"Service\":[\"ecs-tasks.amazonaws.com\"]
    }
  }]
}"

```

* Run Also:

```sh
aws iam put-role-policy \
  --policy-name SSMAccessPolicy \
  --role-name CruddurTaskRole \
  --policy-document "{
  \"Version\":\"2012-10-17\",
  \"Statement\":[{
    \"Action\":[
      \"ssmmessages:CreateControlChannel\",
      \"ssmmessages:CreateDataChannel\",
      \"ssmmessages:OpenControlChannel\",
      \"ssmmessages:OpenDataChannel\"
    ],
    \"Effect\":\"Allow\",
    \"Resource\":\"*\"
  }]
}
"

```

* Make an access to CloudWatch:

```sh
aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/CloudWatchFullAccess --role-name CruddurTaskRole
```
* Make an access to Daemon:

```sh
aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess --role-name CruddurTaskRole
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/e4c51b53-feb4-4cbf-9941-3cd3b0b07deb"/>
</p>

4. Create the Task definition:

* Create a new Folder in /aws and name it task-definition.

* Add the following:

```sh
 {
  "family": "backend-flask",
  "executionRoleArn": "arn:aws:iam::469104735461:role/CruddurServiceExecutionRole",
  "taskRoleArn": "arn:aws:iam::469104735461:role/CruddurTaskRole",
  "networkMode": "awsvpc",
  "cpu": "256",
  "memory": "512",
  "requiresCompatibilities": [ 
    "FARGATE" 
  ],
  "containerDefinitions": [
    {
      "name": "xray",
      "image": "public.ecr.aws/xray/aws-xray-daemon" ,
      "essential": true,
      "user": "1337",
      "portMappings": [
        {
          "name": "xray",
          "containerPort": 2000,
          "protocol": "udp"
        }
      ]
    },
    {
      "name": "backend-flask",
      "image": "469104735461.dkr.ecr.us-east-1.amazonaws.com/backend-flask",
      "essential": true,
      "healthCheck": {
        "command": [
          "CMD-SHELL",
          "python /backend-flask/bin/health-check"
        ],
        "interval": 30,
        "timeout": 5,
        "retries": 3,
        "startPeriod": 60
      },
      "portMappings": [
        {
          "name": "backend-flask",
          "containerPort": 4567,
          "protocol": "tcp", 
          "appProtocol": "http"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
            "awslogs-group": "cruddur",
            "awslogs-region": "us-east-1",
            "awslogs-stream-prefix": "backend-flask"
        }
      },
      "environment": [
        {"name": "OTEL_SERVICE_NAME", "value": "backend-flask"},
        {"name": "OTEL_EXPORTER_OTLP_ENDPOINT", "value": "https://api.honeycomb.io"},
        {"name": "AWS_COGNITO_USER_POOL_ID", "value": "us-east-1_yl5F8l4Ip"},
        {"name": "AWS_COGNITO_USER_POOL_CLIENT_ID", "value": "6mkiehfmgoeh7pm0j8k36ltfa2"},
        {"name": "FRONTEND_URL", "value": "*"},
        {"name": "BACKEND_URL", "value": "*"},
        {"name": "AWS_DEFAULT_REGION", "value": "us-east-1"}
      ],
      "secrets": [
        {"name": "AWS_ACCESS_KEY_ID"    , "valueFrom": "arn:aws:ssm:us-east-1:469104735461:parameter/cruddur/backend-flask/AWS_ACCESS_KEY_ID"},
        {"name": "AWS_SECRET_ACCESS_KEY", "valueFrom": "arn:aws:ssm:us-east-1:469104735461:parameter/cruddur/backend-flask/AWS_SECRET_ACCESS_KEY"},
        {"name": "CONNECTION_URL"       , "valueFrom": "arn:aws:ssm:us-east-1:469104735461:parameter/cruddur/backend-flask/CONNECTION_URL" },
        {"name": "ROLLBAR_ACCESS_TOKEN" , "valueFrom": "arn:aws:ssm:us-east-1:469104735461:parameter/cruddur/backend-flask/ROLLBAR_ACCESS_TOKEN" },
        {"name": "OTEL_EXPORTER_OTLP_HEADERS" , "valueFrom": "arn:aws:ssm:us-east-1:469104735461:parameter/cruddur/backend-flask/OTEL_EXPORTER_OTLP_HEADERS" }
      ]
    }
  ]
}

```

5. Register Task Definition:

* Run the following:

```sh
aws ecs register-task-definetion --cli-input-json file://aws/json/task-definitions/backend-flask.json
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/af677411-e3b6-416b-a327-904e176c5c31"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/97fd0f90-2b80-44c8-9125-81444be47949"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/9ecdd2c7-4f4e-4f9e-87ce-601dec1fb0f8"/>
</p>


#### Run the Container

1. Create the security group

* We need the default VPC ID (by run):
```sh
export DEFAULT_VPC_ID=$(aws ec2 describe-vpcs \
--filters "Name=isDefault, Values=true" \
--query "Vpcs[0].VpcId" \
--output text)

```

* Check it:
```sh
echo $DEFAULT_VPC_ID
```

* Create it:
```sh
export CRUD_SERVICE_SG=$(aws ec2 create-security-group \
  --group-name "crud-srv-sg" \
  --description "Security group for Cruddur services on ECS" \
  --vpc-id $DEFAULT_VPC_ID \
  --query "GroupId" --output text)
echo $CRUD_SERVICE_SG

```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/a8b02e77-8da4-475c-bf5f-eb9483fb5251"/>
</p>

2. Run the ECS container:

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/e86493bb-c5c2-493b-a99b-eafbd077976b"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/01a57afe-51bc-4a35-8bec-5c2adf37c699"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/fc34c81a-f6d1-4808-a5d1-9604ec643618"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/c3d0c1e0-d78b-4d2a-a63b-e01662d1c0c6"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/24ab2faa-15ec-4386-8eb0-733a8996a5f3"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/c9bcadbc-9476-418d-aae6-f9b44ad91967"/>
</p>


We can see the tasks and check if it done correctly or not:

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/7eccc789-6592-4262-9e5f-274d7dd099c7"/>
</p>


We can notice there is a problem in one of the tasks:
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/1a798732-c3aa-4f0e-99e9-d15d50ab75e5"/>
</p>


The problem is in the execution role, we need to add more authority for the ECR.
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/7747f0dc-2fb5-4b28-8e14-0eb024c928fe"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/b8116f9b-f8d0-42e3-9176-89e21e599364"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/0409dec2-76bf-45dc-9009-42b1d5bc814b"/>
</p>

Now it is working:
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/3bc76d27-67ee-48df-ba03-dd2778b23f12"/>
</p>

### Using Session Manager
It is used to enter to access the container shell.

* Install the Session Manager plugin.
```sh
curl "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_64bit/session-manager-plugin.deb" -o "session-manager-plugin.deb"
```
```sh
sudo dpkg -i session-manager-plugin.deb
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/f535adef-cfc3-48f7-b455-a7a1b8b1026d"/>
</p>

* Verify if it works.
```
Session-manager-plugin
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/6a4c4c8f-7ef1-4b3b-99c1-35fea5735525"/>
</p>

#### Script to run your service using CLI

* Create a new file unde aws/json and call it “service-backend-flask.json”.
* Add the following code:
```sh
{
    "cluster": "cruddur",
    "launchType": "FARGATE",
    "desiredCount": 1,
    "enableECSManagedTags": true,
    "enableExecuteCommand": true,
    "loadBalancers": [
      {
          "targetGroupArn": "arn:aws:elasticloadbalancing:ca-central-1:387543059434:targetgroup/cruddur-backend-flask-tg/87ed2a3daf2d2b1d",
          "containerName": "backend-flask",
          "containerPort": 4567
      }
    ],
    "networkConfiguration": {
      "awsvpcConfiguration": {
        "assignPublicIp": "ENABLED",
        "securityGroups": [
          "sg-04bdc8d5443cc8283"
        ],
        "subnets": [
          "subnet-0462b87709683ccaa",
          "subnet-066a53dd88d557e05",
          "subnet-021a6adafb79249e3"
        ]
      }
    },
    "serviceConnectConfiguration": {
      "enabled": true,
      "namespace": "cruddur",
      "services": [
        {
          "portName": "backend-flask",
          "discoveryName": "backend-flask",
          "clientAliases": [{"port": 4567}]
        }
      ]
    },
    "propagateTags": "SERVICE",
    "serviceName": "backend-flask",
    "taskDefinition": "backend-flask"
  }

```
* Deploy the service:

```sh
aws ecs create-service –cli-input-json file://aws/json/service-backend-flask.json
```
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/2e2d48f5-8485-4d3b-923b-959cd0128b49"/>
</p>

* Connect to container by running the following command:
```sh
aws ecs execute-command  \
--region $AWS_DEFAULT_REGION \
--cluster cruddur \
--task e9245e867cc44f56bcea0612b32fea3a \
--container backend-flask \
--command "/bin/bash" \
--interactive
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/a18f10ec-f5ce-4c41-9767-9bfcaab83aee"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/c3dca162-f499-4cc6-8432-d49015535597"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/9fa735c8-ac9a-47c5-aad0-dd1c86b3ba3b"/>
</p>

* In Gitpod.yaml add the following:

* Check if the backend is running:

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/6c5cc8fc-f70c-4dc1-868a-e2b6ba8eed1b"/>
</p>

#### Connect Service with RDS

We should make sure that the security group has access to RDS…
* Go to default security group.
* Add the new rule as shown “the service SG”:

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/5f498132-67d2-4c7c-8cfe-6f0cfaa7ded4"/>
</p>

* Test the connection:
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/07d36ed6-6900-4067-9c9a-b7a48fb6b55a"/>
</p>

#### Using Service Connect

* Go to console and create a new service.
* Turn on service connect.

<p align="center">
  <img src="![image](https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/4839d848-827c-474d-97c7-46f07cb51c83)
"/>
</p>

* Choose port mapping setting.
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/6a46634b-ebbd-4578-8767-602d6564975b"/>
</p>

* Change network settings and Run…
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/40de2637-d046-43e3-ab46-8c927214e495"/>
</p>

#### Set-up load balancer

1- Select the type (ALB):
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/0dd90f60-6f51-4501-b489-81d64e63c41b"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/6a6cd6cb-5d63-4754-915b-a77d900ab025"/>
</p>

2-	Select all the subnets of VPC.
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/3c17a1d8-e725-47b1-ab77-dd320c46413d"/>
</p>

3-	Create the Security Group:
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/4c69fda2-80d5-458f-a4a8-433ebdd0b883"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/55c1fd91-ce7b-4930-8880-808cb39f1a27"/>
</p>

4-	Give the access to ECS:
So, the traffic will go only through the load balancer.
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/518ccaa2-5bd1-48dd-9311-6b60cbd5cb2b"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/a70a2bdc-a22c-4b9a-a8b1-1ea49d000877"/>
</p>

5-	Create Target Group:
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/13198574-5732-4035-9e94-33b4388339a0"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/e3493d65-ef8a-4eea-9313-9c1c0a435246"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/288745a6-8f79-4e13-962b-0b7ac776e504"/>
</p>
** Make the same settings but for frontend on port 3000.


6-	Add the target group to your load balancer:
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/7d8f10c0-f901-4731-9289-2394f547950e"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/859853d1-31aa-4147-8d5a-ffd392c6a367"/>
</p>

7-	Create the Load Balancer
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/35404a0b-c601-4973-aa7b-5caafff7ae5e"/>
</p>


Edit the service create file to add load balancer

* Move to service-backend-flask.json
<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/b2bbd9d2-aaa9-4887-9f70-67fbf9fa5fc7"/>
</p>

* Try to provision the service.
```sh
aws ecs create-service --cli-input-json file://aws/json/service-backend-flask.json
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/19988975-34e9-42f0-90c5-05f48b35896a"/>
</p>
Need to be set to port 4567 and 3000


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/d204c82e-f82f-49e3-abe0-54b91d94df8e"/>
</p>


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/b754feed-e10c-45bd-99c2-581812a7ab60"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/fc0ff4a9-090d-4ff7-9948-a6e0c81969da"/>
</p>


#### Front-End ECS Service

* Create a new file under /aws/task-defenitions/ and call it frontend-reacr-js.json 


<p align="center">
  <img src="![image](https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/2563b708-922d-4169-be29-bc9190cf0a9c)
"/>
</p>

* Build the image:
```sh
docker build \
--build-arg REACT_APP_BACKEND_URL="http://cruddur-alb-1422044236.us-east-1.elb.amazonaws.com:4567" \
--build-arg REACT_APP_AWS_PROJECT_REGION="$AWS_DEFAULT_REGION" \
--build-arg REACT_APP_AWS_COGNITO_REGION="$AWS_DEFAULT_REGION" \
--build-arg REACT_APP_AWS_USER_POOLS_ID="us-east-1_yl5F8l4Ip" \
--build-arg REACT_APP_CLIENT_ID="6mkiehfmgoeh7pm0j8k36ltfa2" \
-t frontend-react-js \
-f Dockerfile.prod \
.

```


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/54fd5ee8-54e8-48d3-8005-f63510d63a87"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/d914348b-af6b-4c32-9368-f6471f6f4dfb"/>
</p>

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/c61f6a8d-1336-471c-9e0a-87ffcd1c655f"/>
</p>

* Make ECR repository for frontend:
```sh
aws ecr create-repository \
  --repository-name frontend-react-js \
  --image-tag-mutability MUTABLE

```


<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/c0b455b3-f80a-471e-abe3-984397f1fc45"/>
</p>


* Set-URL:
```sh
export ECR_FRONTEND_REACT_URL="$AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/frontend-react-js"

echo $ECR_FRONTEND_REACT_URL

```

* Tag Image:
```sh
docker tag frontend-react-js:latest $ECR_FRONTEND_REACT_URL:latest
```

* Push image:
```sh
docker push $ECR_FRONTEND_REACT_URL:latest
```

* Create frontend service:
```sh
{
    "cluster": "cruddur",
    "launchType": "FARGATE",
    "desiredCount": 1,
    "enableECSManagedTags": true,
    "enableExecuteCommand": true,
    "loadBalancers": [
        {
            "targetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:469104735461:targetgroup/cruddur-frontend-react-js/d3c11cb94926301a",
            "containerName": "backend-flask",
            "containerPort": 3000
        }
      ],
    "networkConfiguration": {
      "awsvpcConfiguration": {
        "assignPublicIp": "ENABLED",
        "securityGroups": [
          "sg-0e810b3978451bb34"
        ],
        "subnets": [
            "subnet-0a7ef1dc7b49d70bf",
            "subnet-0e17ed4d7c8da2e7a",
            "subnet-058a44b62ba119a93",
            "subnet-0481b09b9452db39f",
            "subnet-0d94b5dd576e4e523",
            "subnet-06a563c7c3cc8401e"
        ]
      }
    },
    "propagateTags": "SERVICE",
    "serviceName": "frontend-react-js",
    "taskDefinition": "frontend-react-js",
    "serviceConnectConfiguration": {
      "enabled": true,
      "namespace": "cruddur",
      "services": [
        {
          "portName": "frontend-react-js",
          "discoveryName": "frontend-react-js",
          "clientAliases": [{"port": 3000}]
        }
      ]
    }
  }

```

* Register the task definition:
```sh
aws ecs register-task-definition --cli-input-json file://aws/task-definitions/frontend-react-js.json
```

* Create the service:
```sh
aws ecs create-service --cli-input-json file://aws/json/service-frontend-react-js.json
```

<p align="center">
  <img src="https://github.com/lahham392/aws-bootcamp-cruddur-2023/assets/82225825/eca997fd-14d0-4f1d-977d-4736dbcdba2e"/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>


<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>


<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>


<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>


<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>


<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>


<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>

<p align="center">
  <img src=""/>
</p>
