# IBM Cloud Kubernetes Service Lab

<img src="https://kubernetes.io/images/favicon.png" width="200">

# An introduction to containers

Hey, are you looking for a containers 101 course? Check out our [Docker Essentials](https://developer.ibm.com/courses/all/docker-essentials-extend-your-apps-with-containers/).

Containers allow you to run securely isolated applications with quotas on system resources. Containers started out as an individual feature delivered with the linux kernel. Docker launched with making containers easy to use and developers quickly latched onto that idea. Containers have also sparked an interest in microservice architecture, a design pattern for developing applications in which complex applications are down into smaller, composable pieces which work together.

Watch this [video](https://www.youtube.com/watch?v=wlBhtc31I8c) to learn about production uses of containers.

# Objectives

This lab is an introduction to using Docker containers on Kubernetes in the IBM Cloud Kubernetes Service. By the end of the course, you'll achieve these objectives:
* Understand core concepts of Kubernetes
* Build a Docker image and deploy an application on Kubernetes in the IBM Cloud Kubernetes Service 
* Control application deployments, while minimizing your time with infrastructure management
* Add AI services to extend your app 
* Secure and monitor your cluster and app

# Prerequisites 
* A Pay-As-You-Go or Subscription [IBM Cloud account](https://console.bluemix.net/registration/)

# Virtual machines

Prior to containers, most infrastructure ran not on bare metal, but atop hypervisors managing multiple virtualized operating systems (OSes). This arrangement allowed isolation of applications from one another on a higher level than that provided by the OS. These virtualized operating systems see what looks like their own exclusive hardware. However, this also means that each of these virtual operating systems are replicating an entire OS, taking up disk space.

# Containers

Containers provide isolation similar to VMs, except provided by the OS and at the process level. Each container is a process or group of processes run in isolation. Typical containers explicitly run only a single process, as they have no need for the standard system services. What they usually need to do can be provided by system calls to the base OS kernel.

The isolation on linux is provided by a feature called 'namespaces'. Each different kind of isolation (IE user, cgroups) is provided by a different namespace.

This is a list of some of the namespaces that are commonly used and visible to the user:

* PID - process IDs
* USER - user and group IDs
* UTS - hostname and domain name
* NS - mount points
* NET - network devices, stacks, and ports
* CGROUPS - control limits and monitoring of resources

# VM vs container

Traditional applications are run on native hardware. A single application does not typically use the full resources of a single machine. We try to run multiple applications on a single machine to avoid wasting resources. We could run multiple copies of the same application, but to provide isolation we use VMs to run multiple application instances (VMs) on the same hardware. These VMs have full operating system stacks which make them relatively large and inefficient due to duplication both at runtime and on disk.

![Containers versus VMs](images/VMvsContainer.png)

Containers allow you to share the host OS. This reduces duplication while still providing the isolation. Containers also allow you to drop unneeded files such as system libraries and binaries to save space and reduce your attack surface. If SSHD or LIBC are not installed, they cannot be exploited.

# Get set up

Before we dive into Kubernetes, you need to provision a cluster for your containerized app. Then you won't have to wait for it to be ready for the subsequent labs. 

1. You must install the CLIs per https://console.ng.bluemix.net/docs/containers/cs_cli_install.html. If you do not yet have these CLIs and the Kubernetes CLI, do [lab 0](Lab0) before starting the course.
2. If you haven't already, provision a cluster. This can take a few minutes, so let it start first: `ibmcloud cs cluster-create --name <name-of-cluster>`
3. After creation, before using the cluster, make sure it has completed provisioning and is ready for use. Run `ibmcloud cs clusters` and make sure that your cluster is in state "deployed".  
4. Then use `ibmcloud cs workers <name-of-cluster>` and make sure that all worker nodes are in state "normal" with Status "Ready".
