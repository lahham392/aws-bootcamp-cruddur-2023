# Week 2 — Distributed Tracing

## HoneyComb

**1- Register in HoneyComb**

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221829814-d9f17d24-c2df-469f-8a06-8ea30615b9cc.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
There is an environment, such as test:
<br />
<br />
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221829890-91c5e231-da14-463b-9d17-22f7a34c83eb.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
We can create a new environment named “Bootcamp”:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221829954-82cc582b-c54b-44df-9da7-d103e7e67064.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
The API key we use to determine which environment that data will land in, and we can view it and rotate it.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221830205-01438c13-15b2-46a0-9a59-162c4bd66367.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now we are going to save the API in Gitpod environment:

```
Export HONEYCOMB_API_KEY=“API”
Gp env HONEYCOMB_API_KEY=“API”

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221830427-76e1a374-3304-48b5-a1ae-6188b340665e.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Check the environment if it saved:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221830507-b10cdfe0-6c29-4e0e-8946-2b3c4bb71bad.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

Now let’s set the service name:

```
Export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_SERVICE_NAME="Cruddur"

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221830625-cf269851-61af-4ac7-86e7-81dcd1928076.png" alt="Sublime's custom image" width="700" height="350"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/221831254-5ea5f1ae-e667-4846-b6d1-b6259dab9c67.png" alt="Sublime's custom image" width="700" height="350"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/221860242-7cd40665-a8ab-46cc-8bb4-e2bd4160e897.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860267-9f42bab0-dd66-4e8a-9c95-28babdbc6f27.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Go to /backend-flsk/requirements

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860321-987ca392-e8c9-4069-a420-a7c473d8ee4d.png" alt="Sublime's custom image" width="700" height="350"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/221860394-aed82cc3-5ee3-4f03-bef9-4ff17dca5afb.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now we are going to write:

```
pip install -r requirements.txt
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860466-bf79af72-41d7-4676-baf3-2c5971ef251d.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

**3- Initialize the Honeycomb**

First we will put this part in app.py

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221860793-5d08c4e6-adc6-4cb0-b054-6b3efcc08654.png" alt="Sublime's custom image" width="700" height="350"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/221860884-b9b1e33b-223b-4940-bbe3-cb4163145c00.png" alt="Sublime's custom image" width="700" height="350"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/221861021-b0422cbc-b3aa-41ca-9d48-db429d8866c8.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now we will run it.
But before that we will install npm to our frontend app.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861232-3e1df1fe-ea32-4b88-8d40-d9abdbfbacf3.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Run up the docker compose

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861365-8b7ea78f-44b2-4a7e-8c41-f9ab46068463.png" alt="Sublime's custom image" width="700" height="350"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/221861574-a545255a-7926-4a98-a32a-004d92b12b3f.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Check if the app is running or not:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861623-8758fd8a-4360-417a-8570-463183a81f4a.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Check if there is any data or stuff reported:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861734-8430ef5e-aa0c-4e51-8cbd-1947baf3ac20.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861757-1da86a9e-9ea0-4eee-b5a3-913fa1fcfb2c.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
What we can see below is a trace with only a single span, it has a root span only.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221861805-f34d6aa7-7ec1-498a-8afc-85f433a55a45.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

### Creating a Spans

If we are making an http requests inside for example our /api/activities/home/ to call another service or if we were calling to a database and we want to setup automatic instrumentation to tell us about that, so we need to add a bunch of Spans for each.
We can add a span be following instructions in (https://docs.honeycomb.io/getting-data-in/opentelemetry/python/).
In below we will hardcode the span with hardcoded mock information in /home/


Now go to /backend-flask/home_activities.py

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221862357-3cba09dc-dbb3-4387-931f-013b7f1a7719.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
From Honeycomb document page we will add the following:

```
from opentelemetry import trace ## this are going to use the open telemetry API
tracer = trace.get_tracer("tracer.name.here")

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221862507-07ccb542-aff1-4863-89d4-474f57d05b79.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now inside def run(), we will add the rest of code:

```
with tracer.start_as_current_span("http-handler"):
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221862720-2bd85534-9606-4f2a-bbb2-ea916a457642.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221862905-0a779fb1-b997-4de2-87d9-b2e4bf2ed62b.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now after updating let's run our docker compose up, and try access the front-end and back-end applications.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863094-4ffd79bb-bdde-4b57-a2e3-01799befed15.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863113-c5afae49-72f6-44ea-a8bb-b0103027b122.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

Let’s check our Honeycomb environment:
We can see now it has 2 spans.
<br />
<br />
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863174-85434173-5bca-4a3f-86bb-de70463f9f4b.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now we can see after we received that request, we did other things we mocked the data.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863291-4d74b99e-98ac-40a9-9cc7-e349f61f5b6b.png" alt="Sublime's custom image" width="700" height="350"/>
</p>


### Adding Attributes to Spans

Also here we can see the Flask instrumentation: all these fields are there automatically “magic of the auto instrumentation”, also some of these fields are global to the all process. But we can add our own one “very useful.” 

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863425-c19f5273-2b17-4bcf-91c4-9e1c67c55496.png" alt="Sublime's custom image" width="700" height="350"/>
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
  <img src="https://user-images.githubusercontent.com/82225825/221863531-73391e9d-0735-4b81-b52a-2ef38bcbda5e.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
To get the result:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863676-527c8b07-8432-4cd1-87af-90f5afda5b9f.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now let’s check our front-end and back-end again:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863726-0752138f-cce7-4e3d-b65f-002ccf2116ba.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863757-51650490-4965-456a-a9c3-0c0a1d50e73b.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Back to Honeycomb, and let’s make a new query:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863934-997b8acd-fd85-49dc-92a6-653c7bfe5320.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

In Visualize add Count.

In Where it shows the “app.now” attribute!!

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221863993-6e3408d7-7a07-4f8b-b097-35b20eaa2af8.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now in group ID add ttrace.trace_id (we will find all our traces )

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864067-3a43a31f-5098-4afb-8834-763d1eff94b0.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now Run the Query, and down below we can see the results:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864117-8bdd6ad1-d039-4ec3-b9f9-4774535de9e6.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
In last 10 minutes:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864144-a0350550-ba7e-40e3-b39e-d78d7974be80.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
If we click in one of the shown traces it will gives as more details about it:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864188-5425c586-1e8f-42be-80e3-db338ec0faf6.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now if we check the app.now attribute that what we add to our span we got as below:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864472-48f89363-01d0-429a-a787-2936a38f6bf2.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
<br />
<br />
Now let’s check the latency of this queries, by creating a new query, in Visualize add Heatmap (duration ms) also add P90 which means what is the numbers such that 90 of requests are faster that this, and run query:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864531-a5b9c086-05e5-4715-8bb2-5f6ee4dfad84.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221864610-e933fc79-973a-42ca-8584-ff6eea34d800.png" alt="Sublime's custom image" width="700" height="350"/>
</p>


## Instrument X-RAY [HARD]

First, we will make npm installation of front-end automated: go to gitpod.yml and add the below code:
```
-name: react-js
   Command:
     Cd frontend-react-js
       Npm i

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221867224-4c1ef0d3-8cfd-4508-b3b8-e25e61e547e5.png" alt="Sublime's custom image" width="700" height="350"/>
</p>


**Back to X-RAY**

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221868962-1aebd6b4-a560-40ec-9fb6-1a9ed65590ed.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

In order to make it work we have to have a Daemon which is basically another container another app that runs along our application, which you send data to it and then collect it baches and sends it over the X-Ray API to visualize your data in X-Ray UI.

1- Install AWS SDK for Python (Boto3)
Add to the requirements.txt

```
aws-xray-sdk
```
			   
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221869159-b3a06398-73fc-4689-8f78-5ecc11a75038.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

Install python dependencies
			      
```
pip install -r requirements.txt
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221869349-9c9edf45-5825-4aa0-8a3d-6293334d8ec4.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221869364-2dab7018-bb91-454e-a177-557fb24069bd.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

2- Add the following to app.py
										
```
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.ext.flask.middleware import XRayMiddleware
xray_url = os.getenv("AWS_XRAY_URL")
xray_recorder.configure(service='Cruddur', dynamic_naming=xray_url)
XRayMiddleware(app, xray_recorder)
																	       
```
																	       
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221869594-5608a53e-d1f8-446a-83f1-d9ed6bd231e8.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

3- Create x-ray json code. In aws/json/xray.json
	       
```
{
  "SamplingRule": {
      "RuleName": "Cruddur",
      "ResourceARN": "*",
      "Priority": 9000,
      "FixedRate": 0.1,
      "ReservoirSize": 5,
      "ServiceName": "Cruddur",
      "ServiceType": "*",
      "Host": "*",
      "HTTPMethod": "*",
      "URLPath": "*",
      "Version": 1
  }
}
	       
```
	       
4- Run the below command in your terminal.
	       
```
aws xray create-group \
   --group-name "Cruddur" \
   --filter-expression "service(\"backend-flask\")"
	       
```	
	       
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221869852-10b9798b-7a51-4531-8272-8c0671924771.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

If we checked the AWS X-Ray 
I skipped this page by pressing cancel :D

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221869965-496805d9-148a-43ea-a196-7ac9706bfdd0.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221869995-b4466540-f0ad-4bdf-96bc-553db01e87d5.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

5- Add sampling. 
			      
Sampling used determines how much information you want to see “show me a percentage of that requests” less sampling used to save money
			      
Run the following command:

```			      
aws xray create-sampling-rule --cli-input-json file://aws/json/xray.json			      
```
			      
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221870202-d2816878-0d71-4f34-9ef6-99ca1bb06d6f.png" alt="Sublime's custom image" width="700" height="350"/>
</p>
															
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221870474-8a0a820c-ad63-4c9c-a8b3-bc4be1b36d91.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

### X-Ray Daemon Setup			      

This command implemented if we want to install it onto our environment.	
																	       
```																	       
wget https://s3.us-east-2.amazonaws.com/aws-xray-assets.us-east-2/xray-daemon/aws-xray-daemon-3.x.deb
 
sudo dpkg -i **.deb

```																	       

but in this case we will run it as a Container, because when we run ECS EC2 we may have to run X-Ray daemon in the cluster in order to get reporting, the below code will be inside our docker compose file.																
																	       
```																	       
  xray-daemon:
    image: "amazon/aws-xray-daemon"
    environment:
      AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
      AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
      AWS_REGION: "us-east-1"
    command:
      - "xray -o -b xray-daemon:2000"
    ports:
      - 2000:2000/udp
																	       
```																	       

Now go to your Docker Compose file and add it:				     
				     
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221871141-13dc9cf5-9f11-4713-9b9b-6af5b8ed710f.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

**Let’s explain the code:**
	       
**image: "amazon/aws-xray-daemon" :** it is the image of this container, it is available in DockerHub and it is updated from AWS.

**AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}":** it is environment variable which is available and saved in our CDE.

**AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}":** it is environment variable which is available and saved in our CDE.
	       
**AWS_REGION: "us-east-1":** your environment region.
	       
**- "xray -o -b xray-daemon:2000" :** -b to bind and the reason of bind because we want to ignore user data metadata, because x-ray by default thinks it is running in VM, so -o will tell the daemon that you are not running in a VM so don’t reach out AWS metadata

**ports:
      - 2000:2000/udp:** the port that will run on.
	       
	       
	       
Now, we need to add these 2 environments variables in our backend, in docker compose file:
	       
```	       
AWS_XRAY_URL: "*4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}*"
AWS_XRAY_DAEMON_ADDRESS: "xray-daemon:2000"
	       
```	       
	       
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221871724-74040a48-3bce-423b-a7c1-8367a6a649b4.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

Let’s check if it is works or not “Compose Up”
																	       
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221871997-b5934e00-f875-42ad-a30e-997b5948b8f0.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

But back-end not working:
	       
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221872046-7abe36f4-ae79-4960-ac7d-aeb5cb5be980.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

And if we check the logs, we will find the following error ‘Name app not defined’
																	       
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221872140-7fdc1390-4dcd-4c7e-9882-ffa3cf0144a8.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

We will back to app.py:
																
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221872343-f2d797f6-1152-4877-b9b7-6eaa704658f4.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

And now everything is running except the xray-daemon!!! But no worries.
			      
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221872379-5f4d1e08-1f3f-4f1c-b0ef-cc64cac690d4.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

By checking the xray container logs, we will got successfully sent message:
	       
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221872465-68971819-b7fe-4f52-ad20-be24216e77a2.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

Go to AWS console and check the Traces:
			      
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221872502-4a5e0e0d-521a-4bdd-9b17-292b336cb5fb.png" alt="Sublime's custom image" width="700" height="350"/>
</p>

And also if we click on trace, we can see the trace map:
																
<p align="center">
  <img src="!https://user-images.githubusercontent.com/82225825/221872624-908386ef-982d-4cbb-a2fc-c60b7e088aab.png" alt="Sublime's custom image" width="700" height="350"/>
</p>


### Now as what we did in Honeycomb, Start a custom segment:
			      
Got to home-activities.py
			      
Just to save a spent we did:			      
			      
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221872771-64fdd862-b46f-4d51-b6d9-861bfe95091d.png" alt="Sublime's custom image" width="700" height="350"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/221872797-1a94cc19-2a1e-46b7-ac14-6ff3588a5c3a.png" alt="Sublime's custom image" width="700" height="350"/>
</p>



## Cloudwatch Logs

1- Install Watchtower

What is a watchtower? Watchtower is a log handler for Amazon Web Services CloudWatch Logs. CloudWatch Logs is a log management service built into AWS. It is conceptually similar to services like Splunk, Datadog, and Loggly, but is lightweight, cheaper, and tightly integrated with the rest of AWS.

Open your Gitpod environment and go to requirements.txt and add the below:

watchtower

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222350090-24f31c42-9f04-4003-a222-535e771dd021.png" alt="Sublime's custom image"/>
</p>

Now go to backend-flask directory and run:

```
pip install -r requirements.txt
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222350154-ee785a6e-6a97-44e6-abd7-1dfa754a4fd5.png" alt="Sublime's custom image"/>
</p>

2- In App.py

Add the below import stuff:

```
import watchtower
import logging
from time import strftime

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222350256-d2dd0786-3ade-4565-b65b-8593cea75d33.png" alt="Sublime's custom image"/>
</p>

Add the below code configuration:

```
# Configuring Logger to Use CloudWatch
LOGGER = logging.getLogger(__name__)
LOGGER.setLevel(logging.DEBUG)
console_handler = logging.StreamHandler()
cw_handler = watchtower.CloudWatchLogHandler(log_group='cruddur')
LOGGER.addHandler(console_handler)
LOGGER.addHandler(cw_handler)
LOGGER.info("some message")

```
<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222350367-a0809ac3-df8d-4146-8ab6-e6364ec1aa37.png" alt="Sublime's custom image"/>
</p>

What the code will do is:

This will setup a log group with cloudwatch called Crudder, with handler.

Log error after every single request.

Add the below code:

```
@app.after_request
def after_request(response):
    timestamp = strftime('[%Y-%b-%d %H:%M]')
    LOGGER.error('%s %s %s %s %s %s', timestamp, request.remote_addr, request.method, request.scheme, request.full_path, response.status)
    return response

```

also, in Activities Home area add the following:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222350555-bc5b577a-cac9-4215-98fa-99260a4141bd.png" alt="Sublime's custom image"/>
</p>

3- To make some customer logging, Go to home-activites and add following:

```
def run(Logger):
    logger.info("HomeActivites")
    
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222350679-dce46e19-143a-4d40-a555-05bae482d057.png" alt="Sublime's custom image"/>
</p>

4- Add the environment variables in Docker compose:

```
AWS_DEFAULT_REGION: "${AWS_DEFAULT_REGION}"
AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222350892-8a2d075f-f7d8-40f2-883b-f96f6274f35e.png" alt="Sublime's custom image"/>
</p>

Test by starting docker compose up:

All is running.

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351009-f4826ac5-9421-48a2-a51f-18da2f309c32.png" alt="Sublime's custom image"/>
</p>

It shows me the following error.

<p align="center">
  <img src="!https://user-images.githubusercontent.com/82225825/222351110-f8d62d4c-4948-4eb2-b681-db48dcbd2a71.png" alt="Sublime's custom image"/>
</p>


After changing it from Logger to logger

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351141-7de92c96-d4c2-41ca-a416-3553d9129059.png" alt="Sublime's custom image"/>
</p>

It shows:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351246-8e85e437-5d51-47d1-a4dc-c3cfd33c8bf5.png" alt="Sublime's custom image"/>
</p>

After change Logger to logger:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351281-e9b69a20-1578-40a2-8c36-c04a50a24ac0.png" alt="Sublime's custom image"/>
</p>

It worked!!

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351403-2249a831-ac08-4b30-9b24-2d846b3a4066.png" alt="Sublime's custom image"/>
</p>

**Check Cloudwatch:**

We can see our log group:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351521-c068b1fb-13bd-4a93-9290-3af26e3b225b.png" alt="Sublime's custom image"/>
</p>

Some logs streams:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351599-4f6153e3-3769-40d2-862d-7ac674c3fb22.png" alt="Sublime's custom image"/>
</p>

And here our Homeactivites log:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351642-99e5c54b-b975-4b32-b1f2-662d506173fe.png" alt="Sublime's custom image"/>
</p>

Just to save a spent we did:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351750-2fe9ed88-8d89-49e6-9bcb-71499331d9bb.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351781-5cd28212-fba4-4386-9946-8e46b5796732.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351803-0daca7f9-829b-4587-bf4c-c2a6d0a5e437.png" alt="Sublime's custom image"/>
</p>



## Rollbar

1- Go to requirements.txt and add the following:

```
blinker
rollbar

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222351982-067a6295-59de-4d3e-b62d-3f89afcd74a5.png" alt="Sublime's custom image"/>
</p>

Go to backend-flask directory and run it:

```
pip install -r requirements.txt
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352066-709cd2a0-dfa3-4713-a335-a4b53dd51b59.png" alt="Sublime's custom image"/>
</p>

2- We need to set our access token.

```
export ROLLBAR_ACCESS_TOKEN="135ec44feaxxxxxxxxxx9268aa5d7a00"
gp env ROLLBAR_ACCESS_TOKEN="135ec44feaxxxxxxxxxx9268aa5d7a00"

```
we can get the access token from Rollbar website:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352186-3e69d3e0-b776-4642-b05d-7ef977e773f0.png" alt="Sublime's custom image"/>
</p>

Check if it saved:

```
env | grep ROLL
```

3- Go to app.py and add import:

```
import rollbar
import rollbar.contrib.flask
from flask import got_request_exception

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352361-7e94b94e-7da1-4b85-90b3-dfa14fd98fe4.png" alt="Sublime's custom image"/>
</p>

4- Add the initialization code

```
rollbar_access_token = os.getenv('ROLLBAR_ACCESS_TOKEN')
@app.before_first_request
def init_rollbar():
    """init rollbar module"""
    rollbar.init(
        # access token
        rollbar_access_token,
        # environment name
        'production',
        # server root directory, makes tracebacks prettier
        root=os.path.dirname(os.path.realpath(__file__)),
        # flask already sets up logging
        allow_logging_basic_config=False)

    # send exceptions from `app` to rollbar, using flask's signal system.
    got_request_exception.connect(rollbar.contrib.flask.report_exception, app)

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352490-65b10d8e-811c-4435-9248-0501fd5b8b3c.png" alt="Sublime's custom image"/>
</p>

5- Add endpoint in app.py for testing:

```
@app.route('/rollbar/test')
def rollbar_test():
    rollbar.report_message('Hello World!', 'warning')
    return "Hello World!"

```

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352589-b888f156-b991-43a8-ac3a-4f37f92253cc.png" alt="Sublime's custom image"/>
</p>

Run docker compose up and check the backend if it is working:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352635-ba923161-791a-4885-b458-cb3d6ed7166a.png" alt="Sublime's custom image"/>
</p>

Run Rollbar test:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352674-19ade87c-60e2-4704-91e2-88078724cef5.png" alt="Sublime's custom image"/>
</p>

But data not logged, go and check the Backend logs:

Nothing in that, open the shell and check the access token environment variables available or not:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352879-f1a099af-3543-422f-8246-df32f4a2842c.png" alt="Sublime's custom image"/>
</p>

**Not shown!!!**

Commit it and restart the environment:

The problem that we didn’t add the environment variable in our docker compose:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222352993-e7562bd6-0552-47f1-b93f-6a208853587c.png" alt="Sublime's custom image"/>
</p>

Now it is working:

<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222353020-6fa9e13a-0677-4fc6-a693-ebd5f9e2b6a1.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222353049-ac1e8652-6179-4c15-8dad-9d1b785e5f6c.png" alt="Sublime's custom image"/>
</p>


<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/222353068-c6e70ceb-4109-4e36-b448-2f2da26b6948.png" alt="Sublime's custom image"/>
</p>



<p align="center">
  <img src="https://user-images.githubusercontent.com/82225825/219944011-1fc891a5-b4a8-4c14-8caf-5a9b23868e62.png" alt="Sublime's custom image"/>
</p>

photo refrance:https://allcode.com/top-aws-services/
