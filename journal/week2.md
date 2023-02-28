# Week 2 — Distributed Tracing

## HoneyComb

1- Register in HoneyComb.

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/221829814-d9f17d24-c2df-469f-8a06-8ea30615b9cc.png)
" alt="Sublime's custom image"/>
</p>

There is an environment, such as test:

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/221829890-91c5e231-da14-463b-9d17-22f7a34c83eb.png)
" alt="Sublime's custom image"/>
</p>

We can create a new environment named “Bootcamp”:

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/221829954-82cc582b-c54b-44df-9da7-d103e7e67064.png)
" alt="Sublime's custom image"/>
</p>

The API key we use to determine which environment that data will land in, and we can view it and rotate it.

<p align="center">
  <img src="<img width="480" alt="image" src="https://user-images.githubusercontent.com/82225825/221830205-01438c13-15b2-46a0-9a59-162c4bd66367.png">
" alt="Sublime's custom image"/>
</p>

Now we are going to save the API in Gitpod environment:

```
Export HONEYCOMB_API_KEY=“API”
Gp env HONEYCOMB_API_KEY=“API”

```

<p align="center">
  <img src="<img width="467" alt="image" src="https://user-images.githubusercontent.com/82225825/221830427-76e1a374-3304-48b5-a1ae-6188b340665e.png">
" alt="Sublime's custom image"/>
</p>

Check the environment if it saved:

<p align="center">
  <img src="<img width="465" alt="image" src="https://user-images.githubusercontent.com/82225825/221830507-b10cdfe0-6c29-4e0e-8946-2b3c4bb71bad.png">
" alt="Sublime's custom image"/>
</p>

Now let’s set the service name:

```
Export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_SERVICE_NAME="Cruddur"

```
<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/221830625-cf269851-61af-4ac7-86e7-81dcd1928076.png)
" alt="Sublime's custom image"/>
</p>

What noticed in our docker compose file that is we sending the Honeycomb service name to the backend container as OTL service name, and if we were running multiple backend services in the docker compose that both of them will send traces to Honeycomb. So, it is preferred to set this environment variables hardcoded in docker compose file, because we don’t want it to be consistent between multiple services (don’t set it in your CDE).

So, we will go to docker compose file, and we will hardcode the environment variables for the backend:

```
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"

```
The all project needs to use the same Honeycomb API key so all the pieces of the app story from the different services can fit together, but each piece of the project should have it is own service name.

We also can add the tracing for the frontend app. (but not recommended).

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/221831254-5ea5f1ae-e667-4846-b6d1-b6259dab9c67.png)
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



## AWS X-RAY












## Rollbar
