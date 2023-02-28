# Week 2 — Distributed Tracing

## HoneyComb

**1- Register in HoneyComb**

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221829814-d9f17d24-c2df-469f-8a06-8ea30615b9cc.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
There is an environment, such as test:
<br />
<br />
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221829890-91c5e231-da14-463b-9d17-22f7a34c83eb.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
We can create a new environment named “Bootcamp”:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221829954-82cc582b-c54b-44df-9da7-d103e7e67064.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
The API key we use to determine which environment that data will land in, and we can view it and rotate it.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221830205-01438c13-15b2-46a0-9a59-162c4bd66367.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now we are going to save the API in Gitpod environment:

```
Export HONEYCOMB_API_KEY=“API”
Gp env HONEYCOMB_API_KEY=“API”

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221830427-76e1a374-3304-48b5-a1ae-6188b340665e.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Check the environment if it saved:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221830507-b10cdfe0-6c29-4e0e-8946-2b3c4bb71bad.png" alt="Sublime's custom image"/>
</p>

Now let’s set the service name:

```
Export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_SERVICE_NAME="Cruddur"

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221830625-cf269851-61af-4ac7-86e7-81dcd1928076.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
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
  <img src="https://user-images.githubusercontent.com/82225825/221831254-5ea5f1ae-e667-4846-b6d1-b6259dab9c67.png" alt="Sublime's custom image"/>
</p>

What we doing here is we configuring OTL (open telemetry) to send to honeycomb, what is good for this setup is that the libraries that you installing in your app are open source, and they are from the Open Telemetry project and the OTL is part of CNCF (cloud native compute foundation), so it is really well governed, full of committees, and really solid open source.

Honeycomb is not your cloud environment, the cloud environment sending standardized messages out to Honeycomb and Honeycomp will stores them in database and gives you a UI to look at them, but you could also change this configuration to send to various other open telemetry backends. 


**2- Now let’s install Honeycomb packages to our backend, move to /backend-flask and run below commands:**

```
pip install opentelemetry-api \
    opentelemetry-sdk \
    opentelemetry-exporter-otlp-proto-http \
    opentelemetry-instrumentation-flask \
    opentelemetry-instrumentation-requests

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860242-7cd40665-a8ab-46cc-8bb4-e2bd4160e897.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860267-9f42bab0-dd66-4e8a-9c95-28babdbc6f27.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Go to /backend-flsk/requirements

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860321-987ca392-e8c9-4069-a420-a7c473d8ee4d.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Add the OLT code:

```
    opentelemetry-api
    opentelemetry-sdk
    opentelemetry-exporter-otlp-proto-http
    opentelemetry-instrumentation-flask
    opentelemetry-instrumentation-requests

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860394-aed82cc3-5ee3-4f03-bef9-4ff17dca5afb.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now we are going to write:

```
pip install -r requirements.txt
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860466-bf79af72-41d7-4676-baf3-2c5971ef251d.png" alt="Sublime's custom image"/>
</p>

**3- Initialize the Honeycomb**

First we will put this part in app.py

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860793-5d08c4e6-adc6-4cb0-b054-6b3efcc08654.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now for initialize tracing and an exporter that can send data to Honeycomb, add following:

```
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860884-b9b1e33b-223b-4940-bbe3-cb4163145c00.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Add the below code for automatic instrumentation with Flask:

```
# Initialize automatic instrumentation with Flask
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861021-b0422cbc-b3aa-41ca-9d48-db429d8866c8.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now we will run it.
But before that we will install npm to our frontend app.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861232-3e1df1fe-ea32-4b88-8d40-d9abdbfbacf3.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Run up the docker compose

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861365-8b7ea78f-44b2-4a7e-8c41-f9ab46068463.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
If you want to make the port publicly by default:
Go to Gitpod.yaml and add the below code:

```
ports:
	  - name: frontend
	    port: 3000
	    onOpen: open-browser
	    visibility: public
	  - name: backend
	    port: 4567
	    visibility: public
	  - name: xray-daemon
	    port: 2000
	    visibility: public

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861574-a545255a-7926-4a98-a32a-004d92b12b3f.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Check if the app is running or not:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861623-8758fd8a-4360-417a-8570-463183a81f4a.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Check if there is any data or stuff reported:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861734-8430ef5e-aa0c-4e51-8cbd-1947baf3ac20.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861757-1da86a9e-9ea0-4eee-b5a3-913fa1fcfb2c.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
What we can see below is a trace with only a single span, it has a root span only.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861805-f34d6aa7-7ec1-498a-8afc-85f433a55a45.png" alt="Sublime's custom image"/>
</p>

### Creating a Spans

If we are making an http requests inside for example our /api/activities/home/ to call another service or if we were calling to a database and we want to setup automatic instrumentation to tell us about that, so we need to add a bunch of Spans for each.
We can add a span be following instructions in (https://docs.honeycomb.io/getting-data-in/opentelemetry/python/).
In below we will hardcode the span with hardcoded mock information in /home/


Now go to /backend-flask/home_activities.py

<p align="center">
  <img src="![image](https://user-images.githubusercontent.com/82225825/221862357-3cba09dc-dbb3-4387-931f-013b7f1a7719.png)
" alt="Sublime's custom image"/>
</p>
<br />
<br />
From Honeycomb document page we will add the following:

```
from opentelemetry import trace ## this are going to use the open telemetry API
tracer = trace.get_tracer("tracer.name.here")

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221862507-07ccb542-aff1-4863-89d4-474f57d05b79.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now inside def run(), we will add the rest of code:

```
with tracer.start_as_current_span("http-handler"):
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221862720-2bd85534-9606-4f2a-bbb2-ea916a457642.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221862905-0a779fb1-b997-4de2-87d9-b2e4bf2ed62b.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now after updating let's run our docker compose up, and try access the front-end and back-end applications.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863094-4ffd79bb-bdde-4b57-a2e3-01799befed15.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863113-c5afae49-72f6-44ea-a8bb-b0103027b122.png" alt="Sublime's custom image"/>
</p>

Let’s check our Honeycomb environment:
We can see now it has 2 spans.
<br />
<br />
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863174-85434173-5bca-4a3f-86bb-de70463f9f4b.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now we can see after we received that request, we did other things we mocked the data.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863291-4d74b99e-98ac-40a9-9cc7-e349f61f5b6b.png" alt="Sublime's custom image"/>
</p>


### Adding Attributes to Spans

Also here we can see the Flask instrumentation: all these fields are there automatically “magic of the auto instrumentation”, also some of these fields are global to the all process. But we can add our own one “very useful.” 

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863425-c19f5273-2b17-4bcf-91c4-9e1c67c55496.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now go back again to home-activites.py.

In def run() we are going to add the following:

```
span = trace.get_current_span()
span.set_attribute("user.id", user.id())

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863531-73391e9d-0735-4b81-b52a-2ef38bcbda5e.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
To get the result:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863676-527c8b07-8432-4cd1-87af-90f5afda5b9f.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now let’s check our front-end and back-end again:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863726-0752138f-cce7-4e3d-b65f-002ccf2116ba.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863757-51650490-4965-456a-a9c3-0c0a1d50e73b.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Back to Honeycomb, and let’s make a new query:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863934-997b8acd-fd85-49dc-92a6-653c7bfe5320.png" alt="Sublime's custom image"/>
</p>

In Visualize add Count.

In Where it shows the “app.now” attribute!!

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863993-6e3408d7-7a07-4f8b-b097-35b20eaa2af8.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now in group ID add ttrace.trace_id (we will find all our traces )

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864067-3a43a31f-5098-4afb-8834-763d1eff94b0.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now Run the Query, and down below we can see the results:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864117-8bdd6ad1-d039-4ec3-b9f9-4774535de9e6.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
In last 10 minutes:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864144-a0350550-ba7e-40e3-b39e-d78d7974be80.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
If we click in one of the shown traces it will gives as more details about it:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864188-5425c586-1e8f-42be-80e3-db338ec0faf6.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now if we check the app.now attribute that what we add to our span we got as below:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864472-48f89363-01d0-429a-a787-2936a38f6bf2.png" alt="Sublime's custom image"/>
</p>
<br />
<br />
Now let’s check the latency of this queries, by creating a new query, in Visualize add Heatmap (duration ms) also add P90 which means what is the numbers such that 90 of requests are faster that this, and run query:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864531-a5b9c086-05e5-4715-8bb2-5f6ee4dfad84.png" alt="Sublime's custom image"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864610-e933fc79-973a-42ca-8584-ff6eea34d800.png" alt="Sublime's custom image"/>
</p>





## AWS X-RAY












## Rollbar
