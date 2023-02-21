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

3-	Run cmd command “python3 -m flask run --host=0.0.0.0 --port=4567

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


