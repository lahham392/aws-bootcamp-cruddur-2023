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



