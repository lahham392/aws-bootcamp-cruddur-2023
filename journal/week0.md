# Week 0 — Billing and Architecture

## C4 Architecture Models
C4 models (also known as Context, Containers, Components, and Code) is a software architecture model that provides a way to visualize and organize the structure of a software system. It is based on the idea of breaking down a system into four distinct aspects: context, containers, components, and code. The C4 model provides a way to communicate software architecture by providing a common language for describing the structural elements of a system and their relationships.


## Conceptual Design using Lucid Chart
### Modification for Lucid
* First of all to add AWS Icons go to &rarr; Import Data &rarr; Shapes &rarr; in the left hand side choose “AWS Architecture 2021”.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219865559-ebabd715-5083-4aad-9bcc-8c26f7d0a89e.png" alt="Sublime's custom image"/>
</p>


* If you are messy man and you want to get rid of diagram boundaries got to &rarr; File &rarr; Page Setting &rarr; select “infinite canvas mode”.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219865722-2712d9f5-6325-43c4-9f27-54d6badd39f5.png" alt="Sublime's custom image"/>
</p>


* To remove the Grid &rarr; go to upper bar &rarr; select View &rarr; select Grid &rarr; turn off show grid.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219865846-f151628e-23b4-46c8-ba30-2e098640bd1c.png" alt="Sublime's custom image"/>
</p>

<br/>
<br/>

### How to make a Napkin design? And what should we put in it?
1- First we start with the user we want to know what the user able to do in our project, add it to your diagram and change the name:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219865970-683ce90a-12a1-40dc-bb0e-67bbf10da811.png" alt="Sublime's custom image"/>
</p>

2- We want to create a Twitter killer, from conceptual perspective we want to know what is the things that we want this user to be able to do. (it is very high level we just put a block of processes).
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866133-04a60415-6f76-40b3-be19-f3f5d480f8c9.png" alt="Sublime's custom image"/>
</p>

3- Here we will asks our selves one dump question (do we want to have the users be unique? Is every user will have their own identity?), the answer yes. So we need to have a place that stores stateful data (for 2 things, 1 for the tweets that will be deleted, 2nd for the actual identity of the person), in our Napkin we will assume that both databases are in one big blob:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866160-0952bdc0-9cff-4b0f-92c4-69b125751362.png" alt="Sublime's custom image"/>
</p>

4- Also, we need a massaging system:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866205-6bc7ef2e-4fe7-47a2-85ea-7ebb37ff4001.png" alt="Sublime's custom image"/>
</p>

5- We want the app to be highly available so we are going to need a load balancer in the frontend.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866251-97e79fc9-fc6c-4143-a5da-81b49dad41bd.png" alt="Sublime's custom image"/>
</p>

6- Now to complete the connection path, there is a dump question, will load balancer talk directly to the backend? (Answer: because like there is API like people could hit the API) (yes for backend there is a load balancer because if the API is going to be exposed -and that how frontend talking to the backend API-) (to save the cost we will use single load balancer for frontend and backend)
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866288-b9ab3e33-5513-48a8-a116-853a65d7d9ed.png" alt="Sublime's custom image"/>
</p>


7- Another question, is there a real time component? Is the messaging system real time? (Ans: Yes we will use the Dynamodb as a massages system with appsync as push real time update).
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866311-eeb4f9dd-c112-4b95-948d-350188f68a65.png" alt="Sublime's custom image"/>
</p>


8- If we looking to the twitter Napkin design we will see a Search service. (it will interact with backend because if we did every thing to the backend then if you had an API that you wanted to search youd have to be here)
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866334-00aaefef-4e17-4c76-8228-6edbb0867122.png" alt="Sublime's custom image"/>
</p>


9- Remember to only add in Napkin diagram the main focused business things, so if for example ddos is important add it, if any security service important add it, and remember this is not the final arch it is just a very high level, for our management the timeline is an important and distinguisher we should add it.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866364-77923738-5d47-4cdb-8e88-22412d2a7a0b.png" alt="Sublime's custom image"/>
</p>

Proof Link: https://lucid.app/documents/view/77b2e0c8-8e3b-4858-aec1-147a6f69e2cd

<br/>
<br/>

## Creating AWS Account
Already DONE "Easy"

<br/>
<br/>

## Creating Budget for AWS
Go to your Root Account &rarr; Search for AWS Budget Service &rarr; Click “create budget” &rarr; we can select between template, or customize (we will select zero spend budget) &rarr; select the template (zero spend) &rarr; Select the name of your budget (we select My Zero-Spend Budget) &rarr; Select the email we will send the alert to &rarr; finally click “create budget”
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866530-7b4370b9-a4b1-4bb9-b187-266efcd08ee4.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866598-500df430-5d57-4b79-8a1d-e46a81a1813c.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866604-0d62d451-8a99-4bb7-8795-b845280b4dfc.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866630-ccd49bf5-8f37-438e-977c-2159e7f83189.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219866648-3599fd6d-99ad-4616-8780-578c488e3125.png" alt="Sublime's custom image"/>
</p>

<br/>
<br/>

### Usage Budget
* We can also make a usage budget that will depends on hours…
Go to your Root Account &rarr; Search for AWS Budget Service &rarr; Click “create budget” &rarr; we can select between template, or customize (we will select this time customize) &rarr; Select the template of your budget (we select usage budget) &rarr; Select the email we will send the alert to &rarr; finally click “create budget”

### Creating Thresholds
* To make your own threshold &rarr; click on your budget &rarr; go to ‘Edit’ &rarr; go next to step 2 &rarr; Select the threshold value &rarr; Save.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867581-38cf1c38-5007-41a4-843f-769af68ac406.png" alt="Sublime's custom image"/>
</p>
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867623-e306ce6e-ccf9-4638-b492-6cea26842046.png" alt="Sublime's custom image"/>
</p>

### Activating MFA
Already done.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867744-b477fdca-e690-467b-bdf2-cb14774b8700.png" alt="Sublime's custom image"/>
</p>


### Delete Root User Access Keys
Already done.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867773-e11410ca-7620-4292-a933-324c020300e2.png" alt="Sublime's custom image"/>
</p>

### Creating a new user to use it for deployment
In the left hand select users &rarr; click ‘Add user’ &rarr; Select username &rarr; enable user access console &rarr; customize your setting for password &rarr; create an admin group with “Administrator Access” &rarr; click ‘create user’.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867897-005cb212-cce6-4af0-8faa-dd64f4adfdc0.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867915-2ac22bbb-7b22-49c2-8790-cd13a6e0a75b.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867928-36d537fe-de95-4e04-b2df-64064cead244.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867955-1210c957-de86-4f20-b468-0caa5f473867.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219867989-931748ab-c5a1-4bc7-8ab8-3f351fbcb0dc.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219868009-baa71394-4d64-4e11-92a1-1712afe6c111.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219868018-dd13c823-1585-4754-a9f5-b9684defc7be.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219868034-e318354b-4b9b-4fd7-aaca-0088c353b22c.png" alt="Sublime's custom image"/>
</p>

## Setup Gitpod
It is an online cloud development environment CDE, we will use it to write a code as we integrate cloud services into our web application, 
First of all login to Gitpod using your GitHub account (you will see all your repositories), Select the needed repository and start a new project.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219868182-6e4eaf7b-1d0b-4569-8687-48668089e117.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219868207-684aea02-6d65-49d0-8080-1d71cda75669.png" alt="Sublime's custom image"/>
</p>


## AWS CloudShell
AWS CloudShell is a browser-based shell experience for managing and administering your AWS resources in the cloud. It provides a secure, temporary environment with all the tools you need to manage your AWS resources.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219941845-ccbfb393-f26c-425c-bc22-48d513176c7e.png" alt="Sublime's custom image"/>
</p>

We can also a tool that turn on auto-prompt this can be done by entering the following command (aws - -cli-auto-prompt).

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219941883-e1c748d3-9175-40f0-8888-9598a2c12c90.png" alt="Sublime's custom image"/>
</p>

To check who is the user “aws sts get-caller-identity” (this because in real environment you will have a lot of account and users and you should check where are you and where the configuration done).

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219941920-dd9a0ac3-fede-40b8-9add-e56a562aba63.png" alt="Sublime's custom image"/>
</p>


### Install AWS CLI
We will install it on our gitpod environment. “Linux”
1-	Go to your Gitpod account and open a new Terminal, upper bar &rarr; Terminal &rarr; New Terminal.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942000-f557c95f-c11a-48bf-ad94-f99304ab6e49.png" alt="Sublime's custom image"/>
</p>

2-	Here the Code for installation of AWS CLI 
Reference: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Change the directory:
```
cd /workspace/
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942113-03ee4f73-d188-4293-8f14-9cf18c7e4f28.png" alt="Sublime's custom image"/>
</p>

Install the zip using curl:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942138-e83679d1-a9df-4f7b-8640-0323fd886360.png" alt="Sublime's custom image"/>
</p>

Check in directory if the file installed or not:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942193-3ea379f8-4850-47df-9e68-740a536fda78.png" alt="Sublime's custom image"/>
</p>

Now unzip (unzip is already installed in Gitpod), and after unzipping we will see new directory called aws:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942230-6ea183e7-8f07-4a3f-b93e-801b3f31b356.png" alt="Sublime's custom image"/>
</p>

Now to begin the installation run:
```
sudo ./aws/install
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942261-f9d4a27d-b427-40cd-9f77-7e4905479860.png" alt="Sublime's custom image"/>
</p>

###To learn more about the directory structure of linux visit “Freebsd.org”###

Now if we check the user of this AWS cli by command, we will get the following error:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942317-35e87f4b-04e4-4fa5-8be5-249b83642ebb.png" alt="Sublime's custom image"/>
</p>

We can add user using this command “aws configure”, but we will use alternative, by adding the environment variables.

To check the environment variables, we use: 
```
export AWS_ACCESS_KEY_ID=AKIAxxxxxxxxxxxxxxxxMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFxxxxxxxxxxxxCYEXAMPLEKEY
export AWS_DEFAULT_REGION=us-west-2
```
Reference: https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html


Add your environment variables using this way:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942496-73af172e-de2f-4c85-ab78-bee8a3c25f21.png" alt="Sublime's custom image"/>
</p>

Now check your environment variables:
```
env | grep AWS_
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942680-af03da8a-15d5-4b71-9623-2fdec1c7545a.png" alt="Sublime's custom image"/>
</p>

Now again check the user identity:
```
aws sts get-caller-identity
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942622-ac605cb1-210b-4451-b55b-5d0b579475b6.png" alt="Sublime's custom image"/>
</p>

### Make your Gitpod Enviroment Consistent
Now if we close this Gitpod environment we will lost everything we did, now what we will do is to make sure that every time we startup our environment it will install again all this automatically.

1-Go to .gitpod.yaml in your repository.

2-Add the below code:
```
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
vscode:
  extensions:
    - 42Crunch.vscode-openapi
```


To save your environment variables to make sure not lost when start up the Gitpod environment:
```
gp env  AWS_ACCESS_KEY_ID="AKIAxxxxxxxxxxxxxxZC"
gp env AWS_SECRET_ACCESS_KEY="08xxxxggfhss1zMYxxxxxxxxxxxxZu+QiAcwfSL"
gp env AWS_DEFAULT_REGION="us-east-1"

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942904-1bcfe0bd-387a-4efc-84e2-e91dd547cb76.png" alt="Sublime's custom image"/>
</p>

If we go to Gitpod dashboard &rarr; user setting &rarr; Variables: we can see that the variables are saved:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942932-e4907a39-f712-4893-9b5c-bee7e2e9a85f.png" alt="Sublime's custom image"/>
</p>

Now will move in our right-hand side to “Source Code”:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219942999-14661ba1-32b3-40bb-9a8d-53c9ed9af008.png" alt="Sublime's custom image"/>
</p>

Click to see the changes:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943069-cb83187d-fbcc-4d1a-b4ee-4360d8b71cd8.png" alt="Sublime's custom image"/>
</p>

To commit it, write your message and hit commit:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943112-e78b53a3-97e6-40cd-b57c-9533f3f13775.png" alt="Sublime's custom image"/>
</p>

**Problem !!**
When I tried to commit the code it is not committed and its gives me following error:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943521-8c794f30-d2b6-4481-8f41-4dd56fbb087c.png" alt="Sublime's custom image"/>
</p>

Also, when I tried to push the code manually it shows me:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943584-4de70813-bccf-47cb-bbb2-1cd40b0ddaf1.png" alt="Sublime's custom image"/>
</p>

I tried to edit the permissions of Gitpod by following steps:
(https://gitpod.io/user/integrations)
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943612-a1b91330-03fe-4015-995f-aa06ef700503.png" alt="Sublime's custom image"/>
</p>

Now it shows me a **new error** message:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943649-81bb0eb2-19ab-41fe-848a-9c88759bceaa.png" alt="Sublime's custom image"/>
</p>


**Problem solved by installing Gitpod Chrome extension…**

Now to check if its work or not, go to the dashboard of Gitpod: (will run the configuration automatically).
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943707-9a3710cf-c7dc-46df-b0a9-1301137545c8.png" alt="Sublime's custom image"/>
</p>


## Create a Budget using AWS CLI
First, we will create a folder in our repository to add the json code on it: right click &rarr; new folder &rarr; make the name “aws” &rarr; create another new folder inside aws folder &rarr; select the name “json”.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943728-803792d5-82fd-4196-92dd-544a712203ce.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219943753-291a3438-e030-453d-9487-45e061a8ac92.png" alt="Sublime's custom image"/>
</p>

Now create a new file called “budget.json” inside the aws/json and add the following code:
```
{
    "BudgetLimit": {
        "Amount": "100",
        "Unit": "USD"
    },
    "BudgetName": "Example Tag Budget",
    "BudgetType": "COST",
    "CostFilters": {
        "TagKeyValue": [
            "user:Key$value1",
            "user:Key$value2"
        ]
    },
    "CostTypes": {
        "IncludeCredit": true,
        "IncludeDiscount": true,
        "IncludeOtherSubscription": true,
        "IncludeRecurring": true,
        "IncludeRefund": true,
        "IncludeSubscription": true,
        "IncludeSupport": true,
        "IncludeTax": true,
        "IncludeUpfront": true,
        "UseBlended": false
    },
    "TimePeriod": {
        "Start": 1477958399,
        "End": 3706473600
    },
    "TimeUnit": "MONTHLY"
}

```

Also, create a new file called “budget-notifications-with-subscribers.json” inside the aws/json and add the following code:
```
[
    {
        "Notification": {
            "ComparisonOperator": "GREATER_THAN",
            "NotificationType": "ACTUAL",
            "Threshold": 80,
            "ThresholdType": "PERCENTAGE"
        },
        "Subscribers": [
            {
                "Address": "example@example.com",
                "SubscriptionType": "EMAIL"
            }
        ]
    }
]

```

To create the budget and notification we will use the following command:
```
aws budgets create-budget \
    --account-id $ACCOUNT_ID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/notifications-with-subscribers.json

```


Finally, create a new file called “alarm-config.json” inside the aws/json and add the following code:
```
    {
        "AlarmName": "DailyEstimatedCharges",
        "AlarmDescription": "This alarm would be triggered if the daily estimated charges exceeds 1$",
        "ActionsEnabled": true,
        "AlarmActions": [
            "arn:aws:sns:ca-central-1:***REMOVED***:billing-alarm"
        ],
        "EvaluationPeriods": 1,
        "DatapointsToAlarm": 1,
        "Threshold": 1,
        "ComparisonOperator": "GreaterThanOrEqualToThreshold",
        "TreatMissingData": "breaching",
        "Metrics": [{
            "Id": "m1",
            "MetricStat": {
                "Metric": {
                    "Namespace": "AWS/Billing",
                    "MetricName": "EstimatedCharges",
                    "Dimensions": [{
                        "Name": "Currency",
                        "Value": "USD"
                    }]
                },
                "Period": 86400,
                "Stat": "Maximum"
            },
            "ReturnData": false
        },
        {
            "Id": "e1",
            "Expression": "IF(RATE(m1)>0,RATE(m1)*86400,0)",
            "Label": "DailyEstimatedCharges",
            "ReturnData": true
        }]
      }

```

But before that we should create a 

1-SNS topic by running following command (aws sns create-topic –name billing-alarm): This will return for us TopicARN.
2-Create subscription supply the TopicARN and Email using the below command:
```
aws sns subscribe \
      --topic-arn=”TopicARN” \
      --protocol=email \
      --notification-endpoint your@email.com \

```
3-	Apply
```
aws cloudwatch put-metric-alarm –cli-input-json: file://aws/json/alarm-config.json”
```

## HOMEWORK

*1. Destroy your root account credentials, Set MFA, IAM role*
already done as shown above.

*2. Use EventBridge to hookup Health Dashboard to SNS and send notification when there is a service health issue*

*3. Review all the questions of each pillars in the Well Architected Tool*
in thsi part we will see the pillars of well architecture design, and within each of these pillars there are multiple of different questions to extract your requirements risk asumptions and constraints.
Quastion are variable and no yes/no questions, also it's depend if it for production or pre-production workload.

Pillar 1: Operational Excellence
Q) How do you mitigate deployment risks?

Pillar 2: Security
Q) How do you manage identities for people and machines?

Pillar 3: Reliability
Q) How do you monitor workload resources?

Pillar 4: Performance Efficiency
Q) How do you select the best performance architecture?

Pillar 5: Cost OPtimization
Q) How do you implement cloud financial management?

Pillar 6: Sustainability
@) How do your development and deployment processes support your sustainability goals?


*4. Create an architectural diagram (to the best of your ability) the CI/CD logical pipeline in Lucid Charts*













<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219944011-1fc891a5-b4a8-4c14-8caf-5a9b23868e62.png" alt="Sublime's custom image"/>
</p>

photo refrance:https://allcode.com/top-aws-services/
