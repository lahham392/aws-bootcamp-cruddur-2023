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
### Code explanation:
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
  <img src="" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="" alt="Sublime's custom image"/>
</p>



