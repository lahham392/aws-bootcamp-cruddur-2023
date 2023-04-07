# Week 5 — DynamoDB and Serverless Caching

Code explanation:

FROM python:3.10-slim-buster (it will pull the python container image
from dockerhub)

\# Inside Container, make a new folder

WORKDIR /backend-flask (it will switch us to the needed work directory,
this directory will specify where the container run in the guest os not
host os)

\# Outside container -\> inside container

COPY requirements.txt requirements.txt (to copy file from outside the
container to inside container), (this file contain the libraries want to
install to run the app)

\# Inside the container

RUN pip3 install -r requirements.txt (it will install the python
libraries used for the app)

\# Outside container -\> inside container

COPY . . (. Or called 'period' it means everything in current directory)
(first . means everything in /backend-flask -outside container-) (second
. represent /backend-flask -inside container-)

\# Inside the container

ENV FLASK_ENV=development (environment variable )

EXPOSE \${PORT}
