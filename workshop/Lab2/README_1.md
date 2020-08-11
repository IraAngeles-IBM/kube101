# Lab 2: Scale and Update Deployments

In this lab, you'll learn how to update the number of instances
a deployment has and how to safely roll out an update of your application
on Kubernetes.

For this lab, you need a running deployment of the `guestbook` application
from the previous lab. If you need to create it, go to Lab 1, step 1.

<!-- ```shell
kubectl create deployment guestbook --image=ibmcom/guestbook:v1
``` -->

## 1. Scale apps with replicas

A *replica* is a copy of a pod that contains a running service. By having
multiple replicas of a pod, you can ensure your deployment has the available
resources to handle increasing load on your application.

1. `kubectl` provides a `scale` subcommand to change the size of an
   existing deployment. Let's increase our capacity from a single running instance of
   `guestbook` up to 10 instances:

   ```shell
   kubectl scale --replicas=10 deployment guestbook
   ```

   Kubernetes will now try to make reality match the desired state of
   10 replicas by starting 9 new pods with the same configuration as
   the first.

1. To see your changes being rolled out, you can run:
   ```shell
   kubectl rollout status deployment guestbook
   ```

   The rollout might occur so quickly that the following messages might
   _not_ display:

   ```shell
   $ kubectl rollout status deployment guestbook
   Waiting for rollout to finish: 1 of 10 updated replicas are available...
   Waiting for rollout to finish: 2 of 10 updated replicas are available...
   Waiting for rollout to finish: 3 of 10 updated replicas are available...
   Waiting for rollout to finish: 4 of 10 updated replicas are available...
   Waiting for rollout to finish: 5 of 10 updated replicas are available...
   Waiting for rollout to finish: 6 of 10 updated replicas are available...
   Waiting for rollout to finish: 7 of 10 updated replicas are available...
   Waiting for rollout to finish: 8 of 10 updated replicas are available...
   Waiting for rollout to finish: 9 of 10 updated replicas are available...
   deployment "guestbook" successfully rolled out
   ```

1. Once the rollout has finished, ensure your pods are running by using:
   ```shell
   kubectl get pods
   ```

   You should see output listing 10 replicas of your deployment:

   ```shell
   $ kubectl get pods
   NAME                        READY     STATUS    RESTARTS   AGE
   guestbook-562211614-1tqm7   1/1       Running   0          1d
   guestbook-562211614-1zqn4   1/1       Running   0          2m
   guestbook-562211614-5htdz   1/1       Running   0          2m
   guestbook-562211614-6h04h   1/1       Running   0          2m
   guestbook-562211614-ds9hb   1/1       Running   0          2m
   guestbook-562211614-nb5qp   1/1       Running   0          2m
   guestbook-562211614-vtfp2   1/1       Running   0          2m
   guestbook-562211614-vz5qw   1/1       Running   0          2m
   guestbook-562211614-zksw3   1/1       Running   0          2m
   guestbook-562211614-zsp0j   1/1       Running   0          2m
   ```

**Tip:** Another way to improve availability is to
[add clusters and regions](https://cloud.ibm.com/docs/containers?topic=containers-ha_clusters#ha_clusters)
to your deployment, as shown in the following diagram:

![HA with more clusters and regions](../images/cs_cluster_ha_roadmap_multizone_public.png)

