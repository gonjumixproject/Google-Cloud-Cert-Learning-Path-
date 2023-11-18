# Google-Cloud-Cert-Learning-Path-
# Chapter 2: Designing for Business Requirements
# Chapter 3: Designing for Technical Requirements
<details open>
<summary>  # Section 2: Managing, Designing, and Planning a Cloud Solution Architecture </summary>

## Working with Google Compute Engine
## Managing Kubernetes Clusters with Google Kubernetes Engine

In the previous chapter, we took a deep dive into Google Compute Engine, which provides Infrastructure as a Service. In this chapter, we will look at a Container as a Service (CaaS) offering. Google Kubernetes Engine (GKE) allows us to create managed Kubernetes clusters on demand. Before we start talking about GKE, we need to understand a few concepts, such as microservices, containers, and Kubernetes (K8s) itself.

If you have never heard of these before, don't worry—we will take you through all the basic concepts. We will go through the following topics in this chapter:

 * An introduction to microservices
 * Containers
 * Docker
 * Kubernetes
 * Google Kubernetes Engine

### An introduction to microservices

Let's start by going over the concept of microservices. In the legacy world, applications were delivered in a monolithic architecture. This meant that multiple services were hosted together on a single node. In the microservice architecture, the application is divided into a number of microservices, each hosted on a separate node, like so:

<img width="463" alt="Kb1" src="https://github.com/gonjumixproject/Google-Cloud-Cert-Learning-Path-/assets/43034144/69512a18-9b3f-4ec2-b6e7-79448caa0c2a">


### Containers

To understand containers, let's compare them with virtual machines. While virtual machines virtualize hardware, containers virtualize the operating system. They abstract the application, along with all its dependencies, into one unit. Multiple containers can be hosted on one operating system running as an isolated process:

<img width="461" alt="kb2" src="https://github.com/gonjumixproject/Google-Cloud-Cert-Learning-Path-/assets/43034144/8ce24425-88ac-4fb8-900b-3a8b834ab047">


Containers bring the following advantages:

Isolation: Applications can use their own libraries without conflicting with libraries from other applications.
Resource limitation: Applications can be limited to the resource's usage.
Portability: Applications are self-contained with all dependencies and are not tied to an OS or a cloud provider.
Lightweight: The footprint of the application is much smaller as the containers share a kernel.


### Docker

There are multiple container formats available on the market. GKE supports the most popular one, which is Docker. Docker is an open platform and allows you to develop and run containerized applications. It can run on multiple Linux images offered in GCP as they have the same kernel. Docker images are created using a definition called a Dockerfile. Google Cloud Platform offers a service called Google Container Registry, which allows you to host your Docker images securely, as well as to access them from your Kubernetes cluster.

### Kubernetes

Kubernetes, also known as K8s, is an open source container orchestrator that was initially developed by Google and donated to the Cloud Native Computing Foundation. It allows you to deploy, scale, and manage containerized applications. As an open source platform, it can run on multiple platforms both on-premise as well as in the public cloud. It is suitable for both stateless as well as stateful applications.

#### Kubernetes architecture

In the following diagram, we can see the basic architecture of a Kubernetes cluster. The cluster consists of multiple nodes. From a high level, the master nodes are responsible for the management of the cluster, while the worker nodes host the workloads. The worker nodes host so-called Pods, which are the most atomic units of Kubernetes. These Pods can contain one or more containers. Access to the containers in the Pods is provided using services. In the following diagram, we can see a Kubernetes cluster and the services that are hosted on each type of node:

<img width="438" alt="kb3" src="https://github.com/gonjumixproject/Google-Cloud-Cert-Learning-Path-/assets/43034144/7a502746-c33f-497f-a0b2-a3bb9c0df6f4">


Now, let's have a closer look at master and worker nodes.

#### The master node
The master node takes care of maintaining the desired state of the cluster. It monitors the Kubernetes object definitions (YAML files) and makes sure that they are scheduled on the worker nodes.

It is essentially a control plane for the cluster. It works as follows:

<img width="604" alt="kb4" src="https://github.com/gonjumixproject/Google-Cloud-Cert-Learning-Path-/assets/43034144/4d81ad23-a59e-4d55-8663-b00dd8560a01">


The master node runs multiple processes:

**API server:** Exposes the Kubernetes API. It is the frontend of the control plane.
**Controller manager:** Multiple controllers are responsible for the overall health of the cluster.
**etcd:** A database that hosts the cluster state information.
**Scheduler:** Responsible for placing the Pods across the nodes to balance resource consumption.

A cluster can run perfectly with just one master, but multiple nodes should be run for high availability and redundancy purposes. Without the master, the control plane is essentially down. All the cluster management operations you perform go through the master API. So, for production workloads, it is recommended that you have multiple master configurations.

#### Worker nodes
A worker node can be a virtual machine or even a physical server. In the case of GKE, it is a virtual machine instance. Worker nodes are responsible for running containerized applications. Worker nodes are managed by the master node. They run the following services:

**Kubelet:** This reads the Pod specification and makes sure the right containers run in the Pods. It interacts directly with the master node.
**Kube-proxy:** This is a network proxy running on each node. It enables the usage of services (we will learn about services shortly).
**Container runtime:** This is responsible for running containers. Kubernetes supports multiple runtimes but in the case of GKE, Docker is used.

### Kubernetes objects

Kubernetes objects are records of intent that are defined in YAML format. They are declarative in nature. Once created, Kubernetes will take care of keeping them in the state declared in the definition file. Some examples of the most important objects are as follows:

**Pods**
**ReplicaSets**
**Replication controllers**
**Deployments**
**Namespaces**

It would take hundreds of pages to describe all the available Kubernetes objects, so for the purpose of the exam, we will concentrate on the preceding ones. Let's take a look at an example of a deployment object file. This deployment would basically deploy two Pods with containers using the ```nginx``` image:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

No matter what kind of object we define, we need to have the following data in it:

**apiVersion:** The version of the Kubernetes API we want to use
**kind:** The kind of object to be created
**metadata:** Data that helps uniquely identify the object, such as its name
**spec:** The specification of the object, which is dependent on its type

To create or update an existing object, we can use the following command:

```kubectl apply -f definition.yaml```

Here, definition.yaml is the file with an object definition. To run our first deployment, we would save the preceding definition in the definition.yaml file and run the kubectl command.

There are multiple commands you can use to manage Kubernetes. kubectl is the most used as it allows us to create, delete, and update Kubernetes objects. Take a look at the following link to see what operations can be performed using ```kubectl: https://kubernetes.io/docs/reference/kubectl/overview/```. Note that kubectl is installed in the GCP Cloud Shell console by default, but if you want to use it from any other machine, it needs to be installed.

Now, let's have a look at each object, starting with Pods.

#### Pods
A Pod is the atomic unit of deployment in Kubernetes. A Pod contains one or more containers and storage resources. Usually, there would be a single container within a Pod. Additional containers can be added to the Pod when we need small helper services. Each Pod has a unique IP address that is shared with the containers inside it. Pods are ephemeral by nature and are recreated when they need to be rescheduled. If they use no persistent volumes, the volume content vanishes when a Pod is recreated. To create a Pod, we use kind of Pod and define what image we want to use, like so:

```
apiVersion: v1
kind: Pod
metadata:
 name: my-pod
 labels:
 app: myapp
spec:
 containers:
 - name: my-container
 image: nginx:1.7.9
```

Here, we can see a definition of a Pod with a container that's been deployed from an nginx image.

#### ReplicaSets
A ReplicaSet object is used to manage the number of Pods that are running at a given time. A ReplicaSet monitors how many Pods are running and deploys new ones to reach the desired number of replicas. To define a ReplicaSet, use kind of ReplicaSet. The number of Pods to run is defined under the replicas parameter:

```

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```

ReplicaSets are successors of replication controller objects.

#### Deployments
Deployments are used to deploy, update, and control Pods. These deployments create ReplicaSets without the need to define them separately. By stating how many replicas are needed, the appropriate ReplicaSet object will be created for you. By changing the image in the container, we can update the application to a new version. Deployment objects support both Canary and Blue/Green deployment methods.

// In Canary deployment, we deploy a new version of the application to a subset of users. Once we are sure that the new version works properly, the application is updated for all the users. In Blue/Green deployment, we use two environments, with only one active at a time. After updating the inactive one to a new version and testing it, we switch the traffic and make it the active one. //

To create a deployment object, use kind of Deployment, like so:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-demo
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

In the preceding example, we created a deployment with three replicas from the nginx:1.7.9 image. The deployment object will make sure that three replicas will be running at all times.

#### Namespaces
Namespaces are essentially virtual clusters within a Kubernetes cluster. In big environments, there can be multiple teams developing an application. By creating namespaces, users are allowed to reuse the names of resources. The names need to be unique within the namespaces but not across the cluster. By default, a Kubernetes cluster comes with three predefined namespaces:

**default:** A default namespace for objects with no other namespace
**kube-system:** Used for resources that are created by Kubernetes
**kube-public:** Reserved for future use:

IMAGE KB5



### Google Kubernetes Engine 
</details>

## Exploring Google App Engine as a Compute Option
## Running Serverless Functions with Google Cloud Functions
## Network Options in GCP
## Exploring Storage Options in GCP - Part 1
## Exploring Storage Options in GCP - Part 2
## Analyzing Big Data Options
## Putting Machine Learning to Work
# Section 3 : Designing for Security and Compliance
# Section 4 : Managing Implementation
## Google Cloud Management Options
# Section 5 : Ensuring Solution and Operations Reliability
## Monitoring your Infrastructure
# Section 6 : Exam Focus
## Case Studies 
## Test Your Knowledge
## Assessments


