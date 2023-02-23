# Week 1 — App Containerization
The first question we should ask ourselves, why we need to use the containers if we have already the code and we can run immediately?

Answer is: 
* The size of docker image is much smaller because they just have to implement one layer.
* You can run and start docker containers much faster than the VM.
* You can run a VM image of any OS on any other OS host, you can not do that in docker, because your docker image may not compatible with Kernel in the host “for windows below 10”. You can solve this issue using Docker Toolbox.


Where do containers live?

* Containers live in a container repository “special type of storage for containers”.
* Many companies have their own private repositories where they host or store the containers.
There is also a public repositories for Docker containers where you can browse and find any application container that you want “Docker Hub”.


### Installing Docker Extention in Gitpod
Install Docker extension on your Gitpod environment: mouse right click &rarr; go to “view” &rarr; go to extensions &rarr; write in search “Docker” &rarr; click “install”
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220284580-3018f5e1-554d-469a-96d8-b8cc01fb2960.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220284654-09192bb6-af6c-430b-ba62-52e60c503ad6.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220284786-13fcf631-3b4c-4470-82fe-7a20b893f557.png" alt="Sublime's custom image"/>
</p>

## Now let’s start containerizing our backend:

### Building Docker File:

1- In your Gitpod go to your backend folder and create a new file call it “Dockerfile”, in this file we will put our docker configuration.
2- Copy the below code:
```
FROM python:3.10-slim-buster 

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]

```
#### Code explanation:
```
FROM python:3.10-slim-buster (it will pull the python container image from dockerhub)

# Inside Container, make a new folder
WORKDIR /backend-flask (it will switch us to the needed work directory, this directory will specify where the container run in the guest os not host os)

# Outside container -> inside container
COPY requirements.txt requirements.txt (to copy file from outside the container to inside container), (this file contain the libraries want to install to run the app)

# Inside the container
RUN pip3 install -r requirements.txt (it will install the python libraries used for the app)

# Outside container -> inside container
COPY . . (. Or called ‘period’ it means everything in current directory) (first . means everything in /backend-flask -outside container-) (second . represent /backend-flask -inside container-)

# Inside the container
ENV FLASK_ENV=development (environment variable )
EXPOSE ${PORT}

# it represent the following command “python3 -m flask run --host=0.0.0.0 --port=4567”
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220285803-c9ddd690-eb62-4be8-ae5b-6d6e41d27f16.png" alt="Sublime's custom image"/>
</p>

**Now, lets try this configuration manually in our environment:**

1-	Go to /backend-flsk

2-	Run “pip3 install -r requirements.txt”

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220286894-377a04fb-1ccd-4934-9b6f-2a265d087d1e.png" alt="Sublime's custom image"/>
</p>

3-	Run cmd command:
```
python3 -m flask run --host=0.0.0.0 --port=4567
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220287047-34bbdb8d-2159-4cef-956d-067220a08c53.png" alt="Sublime's custom image"/>
</p>

4- Check ports tap in Gitpod we will see:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220287216-4f15f7de-2a3d-4409-9b0b-68ba384bfc52.png" alt="Sublime's custom image"/>
</p>

5- Make it public click on this icon:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220287357-e94d0a3b-bc4c-4644-9999-7c1731f3263c.png" alt="Sublime's custom image"/>
</p>

6- Click in the link address we will see the following:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220287591-61c0f65b-274f-4ed1-93ed-b63311101f97.png" alt="Sublime's custom image"/>
</p>

7- Check the terminal:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220287713-033df10c-4585-4cfe-955e-aa2177e3e29a.png" alt="Sublime's custom image"/>
</p>

NOTE: Actually, the server is running well, it need some more configuration we will do it later… (app need 2 environment variables, frontend URL and backend URL).


**Question, we usually run the docker image using run command, why here we used python3 to run the image?**
Ans. Run is a command that were actually running to create a layer in the image, but cmd it is a command that that container is going to run when it starts up.
[CMD] is only one in the docker file not multiple such as RUN, so this marks for docker file that this is the command you want to execute as an entry point. 


### Build Docker Image from Docker File

1-	Make sure that you are in (/aws-bootcamp-cruddur-2023) directory.

2-	Paste the following command (-t represent the name of the container…)
```
docker build -t backend-flask ./backend-flask
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220290145-f2695b3f-5dad-4f44-a171-ed4d8c5a1f0f.png" alt="Sublime's custom image"/>
</p>

And as we can see it successfully built:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220290263-65e94eec-16b2-49e6-ad57-fb00363520e5.png" alt="Sublime's custom image"/>
</p>

Now if we checked the Docker extension: we can see the builded image.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220290528-890b3493-c4d0-4fcf-9d11-996ef06a7bf6.png" alt="Sublime's custom image"/>
</p>

Also if I write this command, you can see the running images:
```
docker images
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220290719-620fda90-db6a-402a-913e-00ec750fb4e9.png" alt="Sublime's custom image"/>
</p>

### Run Docker Container

1-	Run the following command
```
docker run --rm -p 4567:4567 -it backend-flask
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220291086-d69a22cd-0ab5-46ff-9341-34aa561372a7.png" alt="Sublime's custom image"/>
</p>

Notice the green dot in Ports tap:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220291229-e7511646-152e-4ae3-ad24-e349dc1be108.png" alt="Sublime's custom image"/>
</p>

Check the server if running by visiting the:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220291779-4b3ca901-3fbf-4bb5-902f-c57121c5bae2.png" alt="Sublime's custom image"/>
</p>


#### To make it run temporarily, make the following instructions:

1- Run the following command, to set the enviroment variables:
```
FRONTEND_URL="*" BACKEND_URL="*" docker run --rm -p 4567:4567 -it backend-flask
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220293158-f8757568-fed5-432c-aa7c-62a44d43aef3.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220293264-315e7d24-bc38-470b-aa97-223f0a6e74eb.png" alt="Sublime's custom image"/>
</p>

Server not working

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220293420-a4a0f838-b218-482b-bd82-5efacfc58ea8.png" alt="Sublime's custom image"/>
</p>

2- To check the logs of the container: Docker extension &rarr; right click &rarr; view logs.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220294025-23086879-519a-4a5d-a944-d8ed31c31687.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220294145-58e43dca-78c0-4ea4-9dc3-bcec4893c6e8.png" alt="Sublime's custom image"/>
</p>

3- Another way, check the environment variables of the container, go to Docker extension &rarr; right click &rarr; Attach shell. (now we will have an access to the container cmd)

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220294484-4e7501e6-ae93-435d-a430-8afe7be779e4.png" alt="Sublime's custom image"/>
</p>

We can notice it the below photo, that the environment variables not set, so because that we got 404 error.
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220294576-9a6fbd50-9453-4d5e-ab4f-2a40c501280f.png" alt="Sublime's custom image"/>
</p>

**To fix it do follow**

1-	Run:
```
set FRONTEND_URL="*"
```
2-	Run:
```
set BACKEND_URL="*"
```
3-	Run:
```
docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask
```
And yes it runs:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220295332-53b6d8b6-1187-4588-8ec2-d83530bae4e7.png" alt="Sublime's custom image"/>
</p>

WORKED!!

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220295423-638005f4-9ffa-4a84-9ea5-1bb4ca4acc5a.png" alt="Sublime's custom image"/>
</p>



**To Run Container in Background**
```
docker container run --rm -p 4567:4567 -d backend-flask
```

## Now let’s start containerizing our backend:

### Building Docker File:

1-	Move to frontend directory “cd /frontend-react-js”

2-	Install NPM using
```
npm i
```

3-	Make a new file in your frontend-react-js and name it ‘Dockerfile’

4-	Copy the code content inside Docker file ‘check below’
```
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]

```

5-	Back to main directory “cd ..”

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220513516-409c0779-f726-472d-9b1d-bbfebbd3e6c2.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220513574-12b12414-f3b1-4ba5-bd65-f033ffb8b5a5.png" alt="Sublime's custom image"/>
</p>

### Building Docker Image from Docker File:

Using the below command:
```
docker build -t frontend-react-js ./frontend-react-js
```

### Run Docker Image:

Using the below command:
```
docker run -p 3000:3000 -d frontend-react-js
```

## Creating Docker Compose YAML

* Docker Compose is a structured way to contain very normal docker comments.
* with docker compose file we take the whole comments with it is configurations and map it into a file “as a structured comments”.
* A tool makes running multiple docker containers with all these configurations in much easier way, That is **Docker Compose…**

1-	Make a new file in your root directory and name it ‘docker-compose.yaml’

2-	Copy the file content:
```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur

```
3-	Run following command “docker compose up” or by right click on the compose file and clicking compose up.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220514906-58596d97-be67-466a-9248-7bf85c0cbfe1.png" alt="Sublime's custom image"/>
</p>

It is strating:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220514987-a26bba25-1d64-4791-b1b5-c72dfde0d0fd.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220515032-61db97cc-e6c6-4bbc-a213-3ada07985715.png" alt="Sublime's custom image"/>
</p>

Open the link Adress:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220515114-532dff6b-9f97-4c0c-8645-7b241f13daf8.png" alt="Sublime's custom image"/>
</p>

## Create the notification feature (Backend and Front)

### OpenAPI file
Let’s check our API file and how it looks:

Go to backend directory &rarr; click on ‘openapi-03.yml’ &rarr; for more information about the API go to API extension.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220515437-b79cebaa-bd00-4999-a000-afbe5042dadf.png" alt="Sublime's custom image"/>
</p>

The reason for using open API is because if we want to use API Gateway, we can import this and it will automatically created, also used to test the API.
The OpenAPI.yml file contains information about the API such as its endpoints, operations, parameters, and security definitions. It can also be used to generate documentation and client libraries for interacting with the API.



### How to run your application again
Just by entering the Gitpod, you can start front-end and back-end containers, also you should start up the docker compose.
If front-end container is not running, make sure to install npm, go to frontend directory and run “npm i”.


### Fix Notification Section

If we are trying to access the notification tap we will get the following page:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220515740-85bd84e6-c0b9-42ff-8b87-9cf3f34f5055.png" alt="Sublime's custom image"/>
</p>

### How will we configure the Notification?

#### Backend Notification

**1-	Update the OpenAPI to add notification:**
We want to add an endpoint to our application, go to openapl-03.yml file and let’s notice the following:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220515936-16853738-2ec3-46ae-ad08-2ba5e67474ab.png" alt="Sublime's custom image"/>
</p>

If we check the current openAPI path, we will notice that there are multiple paths, for example the home, search, and message groups.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220516028-6a6fad53-2e03-4e65-8aa2-5e90dc625529.png" alt="Sublime's custom image"/>
</p>


**2- Now we will add a new path for notifications.**
2.1-  In your right hand there are a block called ‘Paths’ &rarr; click on the 3 dots &rarr; ‘add new path’

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220516188-62776c98-0bfa-4103-be77-af80b65ec8fa.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220516240-4c01c7ca-d8f6-4c69-9764-0bfd75919abe.png" alt="Sublime's custom image"/>
</p>

2.2-  Add a new API code:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220516377-274398ee-5ae9-40a7-ade1-40bcc2ab2f70.png" alt="Sublime's custom image"/>
</p>

#### Code explanation:

**/api/activities/notifications:** when we make the API usually good to scope them with /api our application will be served on API, it will be like (api.crudder.com)

**description:** 'Return a feed of activity for all of those that i follow': it is just a description of our API, this code will be similar for the home page with some little changes.

**tags:** - activities : this will allow you to group, ‘what kind of stuff you are going to pass’

**responses: '200':** indicates that the request has been passed.

**application/json:** we want the application to return application Json (that’s common for the applications to just communicate in Json to be explicit).

**schema:** also we are going to specifiy a schema, it is just a way of refactoring , just saying reference down below in the code.

2.3  Commit your changes and push them to the repository

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220516819-36f64455-1d66-4700-bedc-888c668da78a.png" alt="Sublime's custom image"/>
</p>



**3- Defining a New Endpoint in Backend**

3.1 Back to your backend application “app.py” (the entry point of this application is App.py)

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220517265-80b3974c-5976-4ab9-9a1a-fe1d92128ee5.png" alt="Sublime's custom image"/>
</p>

We can see from above photo that we importing 9 different services, the reason of setting them up is for a concept called Rails Service Object, the idea of this concept is to break your code into concrete services, the advantage of this is that when you want to break these off into microservices as in below photo:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220517328-74a7db3e-1faf-4a72-a698-516985f13571.png" alt="Sublime's custom image"/>
</p>

Actually, we can replace these parts of the code with API gateway and just go to these pythons as a containers or even put them in lambda.


#### App.py Code explanation:


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220517375-e3bf6afc-122d-4873-897e-8b3b3497daf5.png" alt="Sublime's custom image"/>
</p>
**app = Flask(__name__):** In this part we load the application.

**frontend =os.getenv('FRONTEND_URL'), backend=os.getenv('BACKEND_URL'):** it is a way of getting environment variables into the application in front-end and back-end.

**origins = [frontend, backend]:** for setting up the origins.


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220517813-588f0408-6a0c-46e4-9ea2-072d0a67e05e.png" alt="Sublime's custom image"/>
</p>

The above is to set up the CORS (will be explained later in the course).


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220517891-23b8698b-b528-47fe-9448-eace4aa7510c.png" alt="Sublime's custom image"/>
</p>

This is how to define routes for a flask app.


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220517964-3bbf324d-c2fe-4639-a8f8-392b3775ad26.png" alt="Sublime's custom image"/>
</p>

Another route for the flask app, notice that for POST request we need cross_origin…

3.2 We are looking for API home activities, to copy it and make it for the notifications.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220518179-e381846e-06a8-4db2-9e3c-25d52ed579e0.png" alt="Sublime's custom image"/>
</p>

3.3-	Paste the code below this one and perform the below changes:

  a.	Replace /home &rarr; /notifications.
  
  b.	Change data from HomeActivities to &rarr; NotificationsActivities
  
  c.	Create a new file in /backend-flask/services and name it ‘notifications_activities.py’
  
  d.	Change data_home to &rarr; data_notifications().


3.4-	Now lets back in the top of app.py and add the new service to be imported:

as shown below:
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220518679-ba444415-b125-4f8f-8e5c-c9a4a3120b84.png" alt="Sublime's custom image"/>
</p>

3.5-	Go to “notifications_activities.py” and copy what inside “home_activites.py” because they are similar with a little of changes.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220518729-8f21d619-7925-49ac-8a7c-4705fa8699c3.png" alt="Sublime's custom image"/>
</p>

#### Let’s explain the code inside:

**from datetime import datetime, timedelta, timezone:** this import date times and timedelta, time zones because we to mock out the data .

**'created_at': (now - timedelta(days=2)).isoformat(), 'expires_at': (now + timedelta(days=5)).isoformat(), :** it is a way to mock some data, it mean this created before 2 days and going to expire after 5 days.

The rest of information are just mocked to make it as realistic

3.6-	Change class HomeActivities: to &rarr; NotificationsActivities:.


3.7-	Copy the rest of code from home_activities.py to notifications_activities.py and do the following:

  a.	Change the content of page (handel: coco, message: Im a white unicorn).
  
  b.	Delete the rest examples 1 is enough for now.
  
3.8-	Note when you make major changes like add new endpoint you have to restart the app.


3.9-	Now let’s test our new backend service (/api/activities/notifications) by adding it to the path of backend URL.

**It’s Working!!!**

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220519147-cc442f4f-27d6-4d2f-8342-c9acf590ca81.png" alt="Sublime's custom image"/>
</p>

3.10  Commit the changes


#### Backend Notification

1-	Go to ‘/frontend-react-js/’ &rarr; app.js

2-	Add a new import for our notifications 

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220519385-0b047d91-b2e5-4b9d-ad58-00d53d66cc33.png" alt="Sublime's custom image"/>
</p>

3-	Delete (impot process from ‘process’)

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220519416-155e222a-ed26-4660-a48d-bd2f1de20533.png" alt="Sublime's custom image"/>
</p>

4-	Now we are going to add a new path for notifications:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220519519-7bf58d22-b6ea-43aa-a065-8928fcf3770d.png" alt="Sublime's custom image"/>
</p>

5-	Now we need to create file “./pages/NotificationsFeedPage.js'”

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220519549-32787186-f817-46d2-90de-35d08a05e0a2.png" alt="Sublime's custom image"/>
</p>

6-	Create another file for css ““./pages/NotificationsFeedPage.css'

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220519607-848edca7-25f2-4f3c-9e83-5018814ec966.png" alt="Sublime's custom image"/>
</p>

7-	The content in .js is very similar to HomeFeedPage, so we are going to copy from them the content and make our changes:
  a.	Change ‘import './HomeFeedPage.css';’ to &rarr; “import './NotificationsFeedPage.css';”
  
  b.	Change “const backend_url = `${process.env.REACT_APP_BACKEND_URL}/api/activities/home`” &rarr; to “const backend_url =        `${process.env.REACT_APP_BACKEND_URL}/api/activities/notifications`”.
  
  c.	Change “export default function HomeFeedPage() {” to &rarr; “export default function NotificationsFeedPage() {”.
  
  d.	Change “    <DesktopNavigation user={user} active={'home'} setPopped={setPopped} />” to &rarr; “<DesktopNavigation user={user} active={'notifications'} setPopped={setPopped} />”
  
  e.	Also change  “ <ActivityFeed title="Home"” to &rarr; title=”Notifications”
                                   
                                   
8-	The content in .css is also same we are going to copy them.
                                   
9-	Now check you frontend if it is work or not:

**Yes!, notie that the notification page is loaded, also the content what we added is shown:**
                                   
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220519995-836e3a66-5379-43c1-b444-a89c2d0fc8a1.png" alt="Sublime's custom image"/>
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


# HomeWork

## Run the dockerfile CMD as an external script

we did this when we run our docker image using cmd command "python3 -m flask run --host=0.0.0.0 --port=4567"

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220287047-34bbdb8d-2159-4cef-956d-067220a08c53.png" alt="Sublime's custom image"/>
</p>

## Push and tag a image to DockerHub (they have a free tier)

1- Sign in to Docker Hub account
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220521700-53a9d669-e31a-4c3b-8237-4fb46d5dc750.png" alt="Sublime's custom image"/>
</p>

2- Create a repository 

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220522328-821040f5-82db-4c47-9b1f-4d4cee5438e9.png" alt="Sublime's custom image"/>
</p>

3- Check your images

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220522921-ee21f818-b71a-4e6e-8a90-8256378e26cf.png" alt="Sublime's custom image"/>
</p>

4- Login to Docker Hub from your terminal

```
docker login
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220523256-be5f4f37-0f94-45f5-bd72-f8573de214dd.png" alt="Sublime's custom image"/>
</p>

5- Now tag the locally created image to the docker hub:

```
docker tag local-image:tagname new-repo:tagname
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220524149-85e1abfb-c428-46ff-a927-b798c3694ab5.png" alt="Sublime's custom image"/>
</p>

6- Push the image to the Docker hub using the push command:
```
docker push new-repo:tagname
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220526476-b20b408b-ec41-4020-9c28-5fd7c7e99a4d.png" alt="Sublime's custom image"/>
</p>

DONE!! Image published
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220526658-4f8780f8-5e5d-4fb5-989c-4f4461b8cdba.png" alt="Sublime's custom image"/>
</p>


## Use multi-stage building for a Dockerfile build

With multi-stage builds, you use multiple FROM statements in your Dockerfile. Each FROM instruction can use a different base, and each of them begins a new stage of the build. the main benefit of making multi stages is to decrease the docker image size.


### Docker Image Idea

Lets say that we need to build an docker image that will do a process, and to done with this process it will need a Ubunto OS and another 2 tools.
these 2 tools will be used in in the first stage to merge 2 files, and after that in the final stage we will not use these tools any more. in this case we can make a multi-stage docker image to reduce the app size.

### Normal Image

if we are going to build the image in a single stage as shown in below code:

```
FROM ubuntu:20.04
# BUNDLE LAYERS
RUN apt-get update -y && apt install -y --no-install-recommends \
  curl \
  osmium-tool \
 && rm -rf /var/lib/apt/lists/*
RUN mkdir /osmfiles \
 && mkdir /merged \
 && curl https://download.geofabrik.de/europe/monaco-latest.osm.pbf -o /osmfiles/monaco.osm.pbf \
 && curl https://download.geofabrik.de/europe/andorra-latest.osm.pbf -o /osmfiles/andorra.osm.pbf \
 && osmium merge /osmfiles/monaco.osm.pbf /osmfiles/andorra.osm.pbf -o /merged/merged.osm.pbf
```
in summary: This dockerfile does exactly what we need: install curl and osmium, download the files and merge them. We end up with the merged file that resides in /merged.

**to build the image**
```
docker build -t myimage:1.0 .
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220609634-a8c7f148-8b3f-4828-bf40-81c576d96ec9.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220609783-c3355fa7-65b3-47d8-bdfa-00dea9917ef8.png" alt="Sublime's custom image"/>
</p>


The total size of image is:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220609965-1bd0817e-ef69-477b-bbac-abeeed89b00d.png" alt="Sublime's custom image"/>
</p>

### Multi-Stage Image

In this part we’ll focus on leaving behind the tools that we used to build the merged file
check the code below:

```
FROM ubuntu:20.04 AS final
FROM ubuntu:20.04 as build
# BUNDLE LAYERS
RUN apt-get update -y && apt install -y --no-install-recommends \
  curl \
  osmium-tool \
 && rm -rf /var/lib/apt/lists/*
RUN mkdir /osmfiles \
 && mkdir /merged \
 && curl http://download.geofabrik.de/europe/monaco-latest.osm.pbf -o /osmfiles/monaco.osm.pbf \
 && curl http://download.geofabrik.de/europe/andorra-latest.osm.pbf -o /osmfiles/andorra.osm.pbf \
 && osmium merge /osmfiles/monaco.osm.pbf /osmfiles/andorra.osm.pbf -o /merged/merged.osm.pbf
FROM final
RUN mkdir /merged
COPY --from=build /merged /merged
```
As you see we use two stages: one called build and one called final. Install curl and osmium in our build-stage. Use them to create the merged file and finally just copy the merged folder to the final stage.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220610329-b13cdde2-48a2-4fee-9700-001272f0e12c.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220610652-00156c2c-c625-44ef-b899-e411f4aaa3a2.png" alt="Sublime's custom image"/>
</p>


The total size of image is:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220610806-052a37e7-9d2b-4424-9b55-0ce904d56c48.png" alt="Sublime's custom image"/>
</p>


Refrance: https://towardsdatascience.com/using-multi-stage-builds-to-make-your-docker-image-almost-10x-smaller-239068cb6fb0

## Implement a healthcheck in the V3 Docker compose file

"depends_on" is no longer supported in compose version 3. So, unless your docker-compose version is <3 the healthcheck will not help you much. The healthcheck sets the status of the container (starting, healthy or unhealthy) but docker-compose does not wait until backend container is healthy before starting the app-test. There is a detailed explanation about how depends_on works in Control startup and shutdown order in Compose

As a side note, the healthcheck in your compose file sets the status of the app-test container and not backend.

Therefore to control when the api-test can start, you have to wrap the service command of the container. For your particular case the following will do the job:
```
bash -c 'while [[ "$(curl --connect-timeout 2 -s -o /dev/null -w ''%{http_code}'' https://backend:3015/readiness)" != "200" ]]; do echo ..; sleep 5; done; echo backend is up; <service_command>'
```

It tries to connect to backend every 5 seconds (the connection timeout is 2s). When the received HTTP status code is 200 OK the loop ends and it executes the <service_command>

The relevant docker-compose part:
```
api-test:
    restart: always
    command: bash -c 'while [[ "$$(curl --connect-timeout 2 -s -o /dev/null -w ''%{http_code}'' uds-mock-server:4000/readiness)" != "200" ]]; do echo ..; sleep 5; done; echo backend is up;npm start'
    depends_on:
      - backend
      ...
```



## Research best practices of Dockerfiles and attempt to implement it in your Dockerfile

1. Use Multi-Stage Builds: Multi-stage builds are a great way to optimize Dockerfiles, as they allow you to create multiple images from the same Dockerfile. This allows you to separate the build environment from the runtime environment, resulting in smaller images and improved security.

2. Use .dockerignore File: A .dockerignore file allows you to specify files and directories that should not be included in the image. This helps reduce image size and improve build times by excluding unnecessary files from being included in the image.

3. Keep Containers Secure: It is important to keep your containers secure by using minimal base images, running as non-root users, and using security scanning tools such as Clair or Anchore to scan for vulnerabilities in your images.

4. Leverage Caching: Caching can be used to improve build times by reusing layers that have already been built instead of rebuilding them each time a change is made. This can be done by using the --cache-from flag when building an image or by using a caching service such as Google Container Registry or Amazon ECR Image Scanning Service.

5. Use Labels: Labels are a great way to add metadata to your images and can be used for versioning, tracking, and other purposes. They can also be used for automated deployment processes such as Kubernetes or AWS ECS tasks.



## Learn how to install Docker on your localmachine and get the same containers running outside of Gitpod / Codespaces


To install it in our machine, check the following link: https://docs.docker.com/desktop/install/windows-install/#:~:text=Double%2Dclick%20Docker%20Desktop%20Installer,bottom%20of%20your%20web%20browser.

Photo from my machine
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220736507-b5427e9a-e7af-4824-bce6-09e58fd1a1c9.png" alt="Sublime's custom image"/>
</p>

we can run our containers images and compose files in our machine by pull the needed file from our Github repository...
All instructions will almost same.


## Launch an EC2 instance that has docker installed, and pull a container to demonstrate you can run your own docker processes.

Open AWS Console:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220826668-4b0c1ee9-ff75-4913-b940-826317b5a816.png" alt="Sublime's custom image"/>
</p>

Open EC2 Service:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220826778-3ea28a03-eb87-4b2a-b314-69ea7c2eff24.png" alt="Sublime's custom image"/>
</p>

Lunche new EC2 Instance:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220826940-a399c930-3fd2-4564-a596-c9bc17ad225b.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220827063-5636f65c-8398-4bd2-90e0-cf493579f7a9.png" alt="Sublime's custom image"/>
</p>

Connect to Instance:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220827265-eb3e77bc-c29d-401c-9a93-cac5b3ab0c92.png" alt="Sublime's custom image"/>
</p>

Now lets start downloading docker:

Update the yum package index and install packages to allow apt to use a repository over HTTPS:

```
sudo yum update
```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220827554-31628a21-d5dc-404e-850c-e81d9c529efb.png" alt="Sublime's custom image"/>
</p>

Search for Docker package:

```
sudo yum search docker
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220827769-41e6e28c-9515-4a86-a4f5-79ea68e658f8.png" alt="Sublime's custom image"/>
</p>


Install docker:

```
sudo yum install docker
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220827901-9efc8572-7f2b-46df-8f9c-76356d90c8b1.png" alt="Sublime's custom image"/>
</p>

Check docker version information:

```
sudo yum info docker
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220828365-e53171ae-2b46-4199-9df0-7276bbc6f51b.png" alt="Sublime's custom image"/>
</p>

**When we try to run any docker command it is giving me an error (Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?)
**
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/220828570-1eabba0e-2d31-450b-9197-0a8b19b13e7d.png)
" alt="Sublime's custom image"/>
</p>

it is worked after running the service using below command:

```
sudo systemctl start docker
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220828892-19dc9e7e-52ea-4a8c-b16d-7f4326c7c09b.png" alt="Sublime's custom image"/>
</p>

Install git to be able to pull the docker file:

```
sudo yum install git
```


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220829342-68f3ada2-b299-4a14-95c1-171fa54bca91.png" alt="Sublime's custom image"/>
</p>


Pull the image using:

```
git clone <repo url>
```

<p align="center">
  <img src="<img width="805" alt="image" src="https://user-images.githubusercontent.com/82225825/220836561-4a696aaf-e380-4a5f-96a1-2ac5fd3e7bc7.png">
" alt="Sublime's custom image"/>
</p>

Build the image on ec2, navigate to the cloned repo and write below:

```
sudo docker build -t myec2test .
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220837456-174797ef-adf7-462c-9822-6d9c852e95b9.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220837556-24b52e9e-0e87-419a-aca7-b7528b3553ed.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220837875-497c6b9c-ec7b-4aff-b061-0cd3c9e44093.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220838004-bd709225-e2ec-45ff-b2b7-53090fb26ccd.png" alt="Sublime's custom image"/>
</p>

Now run the image:
 
```
sudo docker run Image_ID
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/220839363-e334860e-bcc7-431a-87e2-baae763c0be3.png" alt="Sublime's custom image"/>
</p>



