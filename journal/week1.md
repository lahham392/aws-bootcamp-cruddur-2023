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

