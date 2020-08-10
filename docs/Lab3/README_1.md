# Lab 3: Scale and update apps natively, building multi-tier applications.

In this lab you'll learn how to deploy the same guestbook application we
deployed in the previous labs, however, instead of using the `kubectl`
command line helper functions we'll be deploying the application using
configuration files. The configuration file mechanism allows you to have more
fine-grained control over all of resources being created within the
Kubernetes cluster.

Before we work with the application we need to clone a github repo:

```shell
git clone https://github.com/IBM/guestbook.git
```

This repo contains multiple versions of the guestbook application
as well as the configuration files we'll use to deploy the pieces of the application.

Change directory by running the command 
```shell
cd guestbook/v1
```
You will find all the
configurations files for this exercise in this directory.

## 1. Scale apps natively

Kubernetes can deploy an individual pod to run an application but when you
need to scale it to handle a large number of requests a `Deployment` is the
resource you want to use.
A Deployment manages a collection of similar pods. When you ask for a specific number of replicas
the Kubernetes Deployment Controller will attempt to maintain that number of replicas at all times.

Every Kubernetes object we create should provide two nested object fields
that govern the object’s configuration: the object `spec` and the object
`status`. Object `spec` defines the desired state, and object `status`
contains Kubernetes system provided information about the actual state of the
resource. As described before, Kubernetes will attempt to reconcile
your desired state with the actual state of the system.

For Object that we create we need to provide the `apiVersion` you are using
to create the object, `kind` of the object we are creating and the `metadata`
about the object such as a `name`, set of `labels` and optionally `namespace`
that this object should belong.

Consider the following deployment configuration for guestbook application

**guestbook-deployment.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: guestbook-v1
  labels:
    app: guestbook
    version: "1.0"
spec:
  replicas: 3
  selector:
    matchLabels:
      app: guestbook
  template:
    metadata:
      labels:
        app: guestbook
        version: "1.0"
    spec:
      containers:
      - name: guestbook
        image: ibmcom/guestbook:v1
        ports:
        - name: http-server
          containerPort: 3000
```

The above configuration file create a deployment object named 'guestbook'
with a pod containing a single container running the image
`ibmcom/guestbook:v1`.  Also the configuration specifies replicas set to 3
and Kubernetes tries to make sure that at least three active pods are running at
all times.

- Create guestbook deployment

   To create a Deployment using this configuration file we use the
   following command:

   ```shell
   kubectl create -f guestbook-deployment.yaml
   ```

- List the pod with label app=guestbook

  We can then list the pods it created by listing all pods that
  have a label of "app" with a value of "guestbook". This matches
  the labels defined above in the yaml file in the
  `spec.template.metadata.labels` section.

   ```shell 
   kubectl get pods -l app=guestbook
   ```

When you change the number of replicas in the configuration, Kubernetes will
try to add, or remove, pods from the system to match your request. To can
make these modifications by using the following command:

   ```shell
   kubectl edit deployment guestbook-v1
   ```

This will retrieve the latest configuration for the Deployment from the
Kubernetes server and then load it into an editor for you. You'll notice
that there are a lot more fields in this version than the original yaml
file we used. This is because it contains all of the properties about the
Deployment that Kubernetes knows about, not just the ones we chose to
specify when we create it. Also notice that it now contains the `status`
section mentioned previously.

To exit the `vi` editor, type `:q!`, of if you made changes that you want to see reflected, save them using `:wq`.

You can also edit the deployment file we used to create the Deployment
to make changes. You should use the following command to make the change
effective when you edit the deployment locally.

   ```shell
   kubectl apply -f guestbook-deployment.yaml
   ```

This will ask Kubernetes to "diff" our yaml file with the current state
of the Deployment and apply just those changes.

We can now define a Service object to expose the deployment to external
clients.

**guestbook-service.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: guestbook
  labels:
    app: guestbook
spec:
  ports:
  - port: 3000
    targetPort: http-server
  selector:
    app: guestbook
  type: LoadBalancer
```

The above configuration creates a Service resource named guestbook. A Service
can be used to create a network path for incoming traffic to your running
application.  In this case, we are setting up a route from port 3000 on the
cluster to the "http-server" port on our app, which is port 3000 per the
Deployment container spec.

- Let us now create the guestbook service using the same type of command
  we used when we created the Deployment:

  ```shell
  kubectl create -f guestbook-service.yaml
  ```

- Test guestbook app using a browser of your choice using the url
  `<your-cluster-ip>:<node-port>`

  Remember, to get the `nodeport` and `public-ip` use the following commands, replacing `$CLUSTER_NAME` with the name of your cluster if the environment variable is not already set.

  ```shell
  kubectl describe service guestbook
  ```
  and

  ```shell 
  ibmcloud ks workers --cluster $CLUSTER_NAME
  ```
