# Kubernetes

Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available.
The name Kubernetes originates from Greek, meaning helmsman or pilot. Initially known by Project 7 or Google's Borg Project. Google open-sourced the Kubernetes project in 2014. Kubernetes combines over 15 years of Google's experience running production workloads at scale with best-of-breed ideas and practices from the community.

### K8S Architecture

![Kubernetes Architecture](k8sarchitecture.png)

### Basic Terminologies :

**Control plane (master):**
The Control plane is made up of the kube-api server, kube scheduler, cloud-controller-manager and kube-controller-manager. Kube proxies and kubelets live on each node, talking to the API and managing the workload of each node.

**Cloud-controller-manager:**
The cloud-controller-manager runs in the control plane as a replicated set of processes (typically, these would be containers in Pods). The cloud-controller-manager is what allows you to connect your clusters with the cloud provider’s API and only runs controllers specific to the cloud provider you’re using.
*Note: If you’re running Kubernetes on-prem, your cluster won’t have a cloud-controller-manager.*

**Cluster:** A kubernetes cluster is set of nodes running an applications. Kubernetes cluster consist of one master node and set of worker nodes. These nodes can be either
physical computers or virtual machines. The master node controls the state of the cluster.

**Controller Manager:** Control Manager is responsible for overall health of the cluster. It ensures correct number of pod running as needed.

**Scheduler:** Kubernetes Scheduler assigns pods to different nodes. It checks the requirements of pods and find 
a node which meets the requirement to run the pod. It also schedules nodes so that the pods are distributed 
among all the nodes.

**Kubelet:** The kubelet functions as an agent within nodes and is responsible for the runnings of pod cycles within each node. Its functionality is watching for new or changed pod specifications from master nodes and ensuring that pods within the node that it resides in are healthy, It tries to create a new pod if pod fails. If it can not 
restart failed pod, master detects a node failure and schedule the pod in another node. and the state of pods matches the pod specification.

**Kube-proxy:** Kube-proxy is a network proxy that runs on each node. It maintains network rules, which allow for network communication to Pods from network sessions inside or outside of a cluster. Kube-proxy is used to reach kubernetes services in addition to load balancing of services.

**Etcd:** etcd is a distributed key-value store and the primary datastore of Kubernetes. It stores and replicates the Kubernetes cluster state. It is called brain of the cluster. Generally, stores the current state of the cluster, nodes, controller and all the state related things.

**API Server:** The Kube-API server, a component within the control plane, validates and configures data for API objects, including pods and services. The API Server provides the frontend to the cluster's shared state, which is where all of the other components interact. This means that any access and authentication such as deployments of pods and other Kubernetes API objects via kubectl are handled by this API server.

**Namespace** provides a degree of isolation withing the cluster. Namespace-based scoping is applicable only for namespaced objects(i.e. Deployments, Services, etc.) and not for cluster
-wide objects(e.g. StorageClass, Nodes, PersistentVolumes, etc.).Each Kubernetes resources can only be in one namespace.

**Labels:** Labels are key-value pairs that are attached to objects such as pods. Labels are intended to be used
to identifying attributes of objects that are meaningful and relevant to users.


## Pod

Pods are the smallest deployable units of computing that you can create and manage in Kubernetes.
A Pod (as in a pod of whales or pea pod) is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers. A Pod's contents are always co-located and co-scheduled, and run in a shared context. A Pod models an application-specific "logical host": it contains one or more application containers which are relatively tightly coupled. In non-cloud contexts, applications executed on the same physical or virtual machine are analogous to cloud applications executed on the same logical host.

Usually we don't need to create Pod manually. Deployment, StatefulSet and DaemonSet manages pods. PodTemplates are specification for creating Pods and are included in workload resources such as Deployment, StatefulSet, DaemonSet. When the pod template is updated, the controller creates new pod based on updated template and replace the
existing one.

**Init Container:** Init container runs in pod before actual container. Init container usually contains utilities or set up scripts not present in an app image.

**Pod Termination:** API Server updates the pod status and the pod in the api server is considered dead beyond grace period(30s). Kubelet notices the pod updates and start graceful shutdown. Control Plane removes the shutting-down Pod from Endpoints. The pod can no longer provide service. Other objects no longer consider the pod valid. When the graceful shutdown period is over, kubelet triggers forcible shutdown.

**Pod Disruption Budget:** Pod disruption budget tries to ensure a minimum number of pod running always. It prevents voluntary disruption such a directly deleting a pos or updating a deployment pod template causing restart. However, it can not prevent involuntary disruption such as hardware failure or kernel panic. Though it counts both disruption.

## Node

Kubernetes runs workloads by placing your pods into nodes. Depending on your environment, a node can represent a virtual or physical machine. Each node will contain the components necessary to run pods. There are two distinct classifications of a node within Kubernetes: Master and Worker nodes.

**WORKER NODES:** Worker nodes will have an instance of kubelet, kube-proxy and a certain container runtime (such as Docker, containerd) running on each node, and are used to run user defined containers. These nodes are managed by the control plane.

**MASTER NODES:** A master node, or sometimes called control-plane nodes will have the control plane binaries bootstrapped, and will only be responsible for all components within the control plane, such as runnings of etcd and the kube-api-server. In order to achieve high availability with etcd to establish a quorum, there are normally more than 3 master nodes within a cluster.

**Heartbeats** sent by k8s nodes to determine health of each node. There are two form of heartbeats.
1. updates to the `.status` of Node
2. updates Lease objects within the kube-node-lease namespace. Each node has an associated Lease Object


**Garbage Collection:** Kubernetes checks for and deletes objects that no longer have owner references, like the pods left behind when ReplicaSet is deletes. We can control how and when garbage collection deletes resources that have owner references using Kubernetes finalizers.


## Deployment

A Deployment provides declarative updates for Pods and ReplicaSets.You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate. You can define Deployments to create new ReplicaSets, or to remove existing Deployments and adopt all their resources with new Deployments.

## ReplicaSet

A ReplicaSet's purpose is to maintain a stable set of replica Pods running at any given time. As such, it is often used to guarantee the availability of a specified number of identical Pods. A ReplicaSet only ensures a certain number of Pods are running at any given time. However, Deployment manages ReplicaSet and provides other useful feature such as rollback. So most of the time we use Deployment.

## StatefulSets

StatefulSet is the workload API object used to manage stateful applications.
Manages the deployment and scaling of a set of Pods, and provides guarantees about the ordering and uniqueness of these Pods.
Like a Deployment, a StatefulSet manages Pods that are based on an identical container spec. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods. These pods are created from the same spec, but are not interchangeable: each has a persistent identifier that it maintains across any rescheduling.
If you want to use storage volumes to provide persistence for your workload, you can use a StatefulSet as part of the solution. Although individual Pods in a StatefulSet are susceptible to failure, the persistent Pod identifiers make it easier to match existing volumes to the new Pods that replace any that have failed.



## DaemonSet

A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created.
Some typical uses of a DaemonSet are:
* running a cluster storage daemon on every node
* running a logs collection daemon on every node
* running a node monitoring daemon on every node

In a simple case, one DaemonSet, covering all nodes, would be used for each type of daemon. A more complex setup might use multiple DaemonSets for a single type of daemon, but with different flags and/or different memory and cpu requests for different hardware types.



## Job

A Job creates one or more Pods and will continue to retry execution of the Pods until a specified number of them successfully terminate. As pods successfully complete, the Job tracks the successful completions. When a specified number of successful completions is reached, the task (ie, Job) is complete. Deleting a Job will clean up the Pods it created. Suspending a Job will delete its active Pods until the Job is resumed again.
A simple case is to create one Job object in order to reliably run one Pod to completion. The Job object will start a new Pod if the first Pod fails or is deleted (for example due to a node hardware failure or a node reboot).



### CronJob

A CronJob creates Jobs on a repeating schedule.
One CronJob object is like one line of a crontab (cron table) file. It runs a job periodically on a given schedule, written in Cron format. CronJobs are meant for performing regular scheduled actions such as backups, report generation, and so on. Each of those tasks should be configured to recur indefinitely (for example: once a day / week / month); you can define the point in time within that interval when the job should start.


## Services

An abstract way to expose an application running on a set of Pods as a network service.
With Kubernetes you don't need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives Pods their own IP addresses and a single DNS name for a set of Pods, and can load-balance across them.


**EndPoint** When a service matches pods with selector, the pods IP address and Port is stored in Endpoint object.
When the pod is restarted, Endpoint object is updated.


#### Multi Port Service
If we need to we can expose more than one port. While using multiple port for a Service, we must give all port  names
so that these are unambiguous.

### Service Type

#### Cluster IP
Service creates a virtual IP inside  the cluster to enable communication between different services such as between a set of backend pods and a set of frontend pods. Cluster IP used 
by the Service proxies.

#### NodePort
NodePort Service listens to a Port on the node(s) and forward requests on that port to a port on the Pod. We can access NodePort service from outside the cluster. We can use any nodes IP and `nodeport` to get service. NodePort Service forward requests to Pod randomly, or we can set up our own load balancing solutions.


#### Load Balancer
LoadBalancer does not use any nodes IP, and it distributes load across different Pods. It is provided by cloud service provider. It is resolves single point failure from NodePort
Service. Load Balancer generates an external IP for public use. Load Balancer uses node port internally which can be disabled. If disabled Load Balancer will 
forward the traffic to Pods directly.
We can specify load balancer implementation overriding default cloud provider implementation.

#### Service IP Address

Unlike Pod IP addresses, which actually route to a fixed destination, Service IPs are not actually answered by a single host. Instead, kube-proxy uses iptables to define virtual IP
addresses which are transparently redirected as needed.

## DNS for Services and Pods

Kubernetes DNS schedules a DNS Pod and Service on the cluster, and configures  kubelet to tell individual containers to use the  DNS Service's IP to resolve DNS names.
A DNS query may return different results based on the namespace of the pod making it. DNS queries that don't specify a namespace are limited to the pod's namespace. To access services in other namespaces we need to specify the namespace in DNS query.

## Ingress

An Ingress controller is a specialized load balancer for Kubernetes (and other containerized) environments. An Ingress controller abstracts away the complexity of Kubernetes application traffic routing and provides a bridge between Kubernetes services and external ones.
The NGINX Ingress Controller is production‑grade Ingress controller (daemon) that runs alongside NGINX Open Source or NGINX Plus instances in a Kubernetes environment. The daemon monitors NGINX Ingress resources and Kubernetes Ingress resources to discover requests for services that require ingress load balancing. 

## EndpointSlice 

EndpointSlices provide a simple way to track network endpoints withing a Kubernetes cluster. They offer a more scalable and extensible alternative to Endpoints. EndpointSlice contains references to a set of network endpoints. The control plane automatically creates EndpointSlices for any k8s Service that has a selector specifies. These Endpoints include references to all the Pods that match the Service selector. EndpointSlice can act as the source of truth for kube-proxy when it comes to how to route internal traffic.

## Volumes

A volume is directory, possible with some data in it which is accessible to the containers in a pod. Ephemeral volume types have a lifetime of a pod but persistent volumes exist beyond the lifetime of a pod. For any kind of volumes in a given pod, data is preserved across container restarts.

### emptyDir

An `emptyDir` volume is first created when a Pod is assigned to a node, and exists as long as that Pod is running
on that node. `emptyDir` volume is initially empty, all containers in the Pod can read and write the same files in the 
`emptyDir` volume, though that volume can be mounted at the same or different paths in each container. `emptyDir` 
can be used to checkpointing a long computation for recovery from crashes. 

### hostPath

A `hostPath` volume mounts a file or directory from the host node`s filesystem into Pod. It presents many security risk.
It should be avoided when possible and should be used in Read-Only mode when needed.


### nfs
An `nfs` volume allows an existing Network File System share to be mounted into a Pod. NFS volume can persist data when Pod is removed, and it can be pre-populated with data and data can be shared between pods.

### persistentVolumeClaim
A `persistentVolumeClaim` volume is used to mount a PersistentVolume into a Pod. PersistentVolumeClaims are a way for users to "claim" durable storage without knowing the details of the particular cloud environment.


### Using subPath

Sometimes it is useful to share one volume for multiple uses/containers in a single Pod. Using subPath we can specify a subPath for different containers.

### CSI
Container Storage Interface(CSI) defines a standard interface for k8s to expose arbitrary storage systems to container workloads. csi volume can be used through a reference to a PersistentVolumeClaim.

### Projected Volumes

A projected volume maps several existing volumes sources into the same directory. `secret`, `downwardAPI`, `configMap` and `serviceAccountToken` volumes can be projected.


## Ephemeral Volumes

Ephemeral Volumes don't care whether the data is stored persistently across restarts. Usually they are used for caching services or provide application some read only files like configuration data or secret keys.

### Types of ephemeral volumes
- emptyDir : empty at Pod startup, with storage coming locally from the kubelet based directory or RAM
- configMap, downwardAPI, secret : inject different kinds of k8s data into a Pod
- CSI ephemeral volumes : Provided by special CSI drivers.
- generic ephemeral volumes : can be provided by all storage drivers that also support persistent volumes

`emptyDir`, `configMap`, `downwardAPI`, `secret` are provided as local ephemeral storage and managed by kubelet.
Generic ephemeral volumes can be provided by third-party CSI storage drivers, but also by any other
storage driver that supports dynamic provisioning.

### Generic ephemeral volumes
Generic ephemeral volumes provide a per-pod directory for scratch data that is usually empty after provisioning.
They may also provide some additional features : 
- Storage can be local or network-attached
- Volumes can have a fixed size that Pods are not able to exceed
- Volumes may have some initial data
- Snapshotting, Cloning, resizing and storage capacity tracking are supported

We use Generic ephemeral volumes when creating a Pod. When such a Pod gets created, the ephemeral volume
controller then creates an actual PVC object in the same namespace as the Pod and ensures that the PVC 
gets deleted when the Pod gets deleted.

## Storage Class

A StorageClass provides a way for admin to describe the "classes" of storage they  offer. Different classes might map to quality-of-service levels, or backup policies. To use different types of storage, we can use storage classes when creating PVC. A cluster admin can define and expose multiple flavors of storage each with different set of parameters using Storage Class.

Each StorageClass contains the fields `provisioner`,
parameters, and `reclaimPolicy` which are used when a PersistentVolume belonging to the class needs to be
dynamically provisioned.

If `volumeBindingMode` is set to `Immediate`, volume binding and dynamic provisioning occurs once
the PersistentVolumeClaim is created.

### Dynamic Volume Provisioning

Dynamic Volume Provisioning allows storage volumes to be created when volume is requested rather than creating
volumes beforehand. It ensures that user don't have to worry about the complexity and nuances of how 
storage is provisioned, but still can select form multiple storage options.

To use dynamic provisioning, a cluster admin needs to pre-create one or more StorageClass objects for users.
StorageClass objects define provisioner and which parameters should be passed to that provisioner when 
dynamic provisioning is invoked.

## Volume Snapshots

A `VolumeSnapshotContent` is a snapshot taken from a volume in the cluster that has been provisioned by an
admin. It is a resource in the cluster just like PV.

A `VolumeSnapshot` is a request for snapshot of a volume by a user. It is similar to a PVC.

A `VolumeSnapshotClass` provides a way to describe the "classes" of storage when provisioning a volume snapshot.
It is like StorageClass.

_VolumeSnapshot support is only available for CSI drivers_

In dynamic provisioning, the snapshot common controller creates `VolumeSnapshotContent` objects automatically.
For pre-provisioned snapshots, cluster admin is responsible for creating `VolumeSnapshotContent`.

Volume snapshot classes have a driver that determines what CSI volume plugin is used for provisioning
VolumeSnapshots.

### CSI Volume Cloning

When creating a PVC, we can refer another existing PVC as data source. It is called Volume Cloning. It is only supported for CSI Drivers. A PVC can only be cloned when source and destination is in the same namespace and same storage class. Cloning can only be performed between two volumes that use the same VolumeMode setting. The storage of new PVC should be same or larger than existing source PVc.

## ConfigMaps

A ConfigMap is an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, CL arguments or as configuration files in a volume. ConfigMap is used to decouple environment specific configuration from container image so that the applications are easily portable.


## Secrets

A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key. Because Secrets can be created independently of the Pods that use them, there is less risk of the Secret being exposed during workflow of creating, viewing, and editing Pods. Secret can be mounted as data volumes or exposed as environment variables to be used by a container in a Pod.


### Types of Secret

**Opaque secrets :** Default Secret type to store any key-value pair.
**Service Account Token Secrets :** It is used to store a token that identifies a service account.
**Docker Config Secrets :** Stores credentials for accessing Docker registry for images.  `~/.coker/config.json` files is provided as base64 encoded string in  the secret.
**Basic authentication Secret :** Contains 'username' and `password` for basic authentication
**SSH authentication secrets :** Used for passing `ssh-privateke` for SSH authentication.
**TLS secrets :** It can be used for storing a certificate(`tls.crt`) and its associated key(`tls.key`) that are typically used for TLS. Can be created form command line using `tls` subcommand.
**Bootstrap token Secrets :** It is designed for tokens used during the node bootstrap process.

Instead of mounting the whole secret, we can take keys and create volume for it, and we can also mount that volume in specific path. Permission can be managed for whole
secret as well as specific keys when mounting it in Pod directory.

### Immutable Secrets

Secrets and ConfigMaps can be immutable. Immutable secrets protects accidental updates and reduce load on kube-apiserver by closing watches for secrets marked as immutable. 


### Node Affinity

Node affinity is conceptually similar to nodeSelector -- it allows us to constrain which nodes your pod is eligible to be scheduled on, based on labels on the node.

We can make some rules which will be used to schedule Pods to Nodes. These rules can be soft/preference so if the scheduler can't satisfy them, the pod will still be scheduled. There is two types of affinity, "node affinity" and "inter-pod affinity/anti-affinity". Node affinity is used to schedule Pods to Nodes. Node affinity is specified as files `nodeAffinity` of field `affinity` in  the PodSpec.

### Taints and Tolerations

Node affinity is a property of Pods that attracts them to a set of nodes (either as a preference or a hard requirement). Taints are the opposite -- they allow a node to repel a set of pods.

Tolerations are applied to pods, and allow (but do not require) the pods to schedule onto nodes with matching taints.

Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node; this marks that the node should not accept any pods that do not tolerate the taints.

`NoSchedule` means if the Pod does not have the taint as toleration, it will not be schedules on that Node.
`NoExecute` is used to evict Pods from the Node that does not tolerate the applied taint.
`PreferNoSchedule` will try not to schedule the Pod on that Node.


### Role Based Access Control (RBAC)

Role-based access control (RBAC) is a method of regulating access to computer or network resources on the roles of individual users within organization.

Role and ClusterRole
An RBAC Role or ClusterRole contains rules that represent a set of permissions. Permissions are purely additive (there are no "deny" rules).

A Role always sets permissions within a particular namespace; when you create a Role, you have to specify the namespace it belongs in.

ClusterRole, by contrast, is a non-namespaced resource. The resources have different names (Role and ClusterRole) because a Kubernetes object always has to be either namespaced or not namespaced; it can't be both.

ClusterRoles have several uses. You can use a ClusterRole to:

define permissions on namespaced resources and be granted within individual namespace(s)
define permissions on namespaced resources and be granted across all namespaces
define permissions on cluster-scoped resources
If you want to define a role within a namespace, use a Role; if you want to define a role cluster-wide, use a ClusterRole.



### Limit Ranges

By default, containers run with unbounded compute resources on a Kubernetes cluster. With resource quotas, cluster administrators can restrict resource consumption and creation on a namespace basis. Within a namespace, a Pod or Container can consume as much CPU and memory as defined by the namespace's resource quota. There is a concern that one Pod or Container could monopolize all available resources. A LimitRange is a policy to constrain resource allocations (to Pods or Containers) in a namespace.

A LimitRange provides constraints that can:

Enforce minimum and maximum compute resources usage per Pod or Container in a namespace.
Enforce minimum and maximum storage request per PersistentVolumeClaim in a namespace.
Enforce a ratio between request and limit for a resource in a namespace.
Set default request/limit for compute resources in a namespace and automatically inject them to Containers at runtime.


### Resource Quotas 

When several users or teams share a cluster with a fixed number of nodes, there is a concern that one team could use more than its fair share of resources. Resource quotas are a tool for administrators to address this concern.

A resource quota, defined by a ResourceQuota object, provides constraints that limit aggregate resource consumption per namespace. It can limit the quantity of objects that can be created in a namespace by type, as well as the total amount of compute resources that may be consumed by resources in that namespace.


### Pod Security Policies

A Pod Security Policy is a cluster-level resource that controls security sensitive aspects of the pod specification. The PodSecurityPolicy objects define a set of conditions that a pod must run with in order to be accepted into the system, as well as defaults for the related fields. They allow an administrator to control the following


### Pod Security Standards

The Pod Security Standards define three different policies to broadly cover the security spectrum. These policies are cumulative and range from highly-permissive to highly-restrictive. This guide outlines the requirements of each policy.

**Privileged:**	Unrestricted policy, providing the widest possible level of permissions. This policy allows for known privilege escalations.
**Baseline:**	Minimally restrictive policy which prevents known privilege escalations. Allows the default (minimally specified) Pod configuration.
**Restricted:**	Heavily restricted policy, following current Pod hardening best practices.


### Controlling Access to the Kubernetes API

Users access the Kubernetes API using kubectl, client libraries, or by making REST requests. Both human users and Kubernetes service accounts can be authorized for API access. When a request reaches the API, it goes through several stages, illustrated in the following diagram:

![Kubernetes API](access-control.svg)


# Basic Commands


###
    kubectl cluster-info
###
    kubectl version --short
###
    kubectl config view
###
    kubectl explain deployment                                                                             //to explain a resource
###
    kubectl apply -f nginx.yaml
###
    kubectl get nodes -o wide
###
    kubectl get pod
###
    kubectl get pod -o wide
###
    kubectl get pods --show-labels                                                                          //to show labels of the pods
###
    kubectl get pods -n test                                                                               //to see pods in namespace called test
###
    kubectl get pod nginx-6799fc88d8-t2vmq -o yaml
###
    kubectl describe pod nginx-6799fc88d8-t2vmq
###
    ping 10.244.0.5
###
### 
    kubectl cordon $NODENAME                                                                               //To mark a Node unschedulable
### 
    kubectl describe node <insert-node-name-here>                                                          //view a Node's status and other details:
### 
    kubectl exec -it nginx-6799fc88d8-t2vmq -- /bin/sh                                                     //to enter a pod
    # hostname
    nginx-6799fc88d8-t2vmq
    # exit
###
    kubectl delete pod nginx-6799fc88d8-t2vmq
###
    kubectl delete -f nginx.yaml
###
    kubectl port-forward nginx 8080:8080
###
    kubectl logs nginx
### 
    kubectl exec nginx-deployment-66b6c48dd5-hc7r7 date
###
    kubectl cp <pod-name>:/captures/capture3.txt ./capture3.txt                         //copy from remote container to local
###
    kubectl cp $HOME/config.txt <pod-name>:/config.txt                                  //copy from local to remote container
###
    kubectl get pods --show-labels
###
    kubectl get pods --selector="app=nginx"                                             //select labels which is app=nginx only
    kubectl get pods -l app=nginx
###
    kubectl get pods --selector="app=nginx,ver=1"
###
    kubectl delete deployments --all                                                    
###
    kubectl logs myapp-pod -c init-myservice                                          //Inspect the first init container
###    
    kubectl logs myapp-pod -c init-mydb                                                //Inspect the second init container
###
    kubectl describe rc nginx-rc
    kubectl describe rc/nginx-rc                                                       //describe replication controller
###
    kubectl scale rc nginx --replicas=5                                                //scale up replicas to 5
###
    kubectl get rc nginx
###
    kubectl delete -f nginx-rc.yml
###
    kubectl create -f nginx-rs.yml
###
    kubectl describe rs/nginx-rs
    kubectl describe rs nginx-rs
###
    kubectl get pods -l tier=frontend                                                  //select which is tier=frontend only
###
    kubectl get rs nginx-rs -o wide
###
    kubectl scale rs nginx-rs --replicas=5
###
    kubectl get rs nginx-rs -o wide
###
    kubectl delete -f nginx-rs.yml
###
    kubectl get deploy -l app=nginx-app
###
    kubectl describe deploy nginx-deploy
###
    kubectl set image deploy nginx-deploy nginx-container=nginx:1.9.1                //update nginx to 1.9.1
###
    kubectl set resources deployment/nginx-deployment -c=nginx --limits=cpu=200m,memory=512Mi               //update resources
###
    kubectl edit deploy nginx-deploy
###
    kubectl rollout status deployment/nginx-deploy
###
    kubectl set image deploy nginx-deploy nginx-container=nginx:1.91 --record         //record command to check with rollout
###
    kubectl rollout history deployment/nginx-deploy                                   //to check rollout history
###
    kubectl rollout history deployment/nginx-deploy --revision=2                      //to check rollout history by revision
###
    kubectl rollout undo deployment/nginx-deploy                                      //to undo the previous deployment
###
    kubectl rollout undo deployment/nginx-deployment --to-revision=2                  //rollback to a specific revision
###
    kubectl scale deployment nginx-deploy --replicas=5                                //scale up replicas to 5
###
    kubectl scale deployment nginx-deploy --replicas=1                                 //scale down replicas to 5 
###
    kubectl delete -f nginx-deploy.yml                                                 //to delete deployment
###
    kubectl autoscale deployment/nginx-deployment --min=10 --max=15 --cpu-percent=80    //autoscale Deployment by min and max number of Pods run based on CPU utilization of existing Pods
###
    kubectl rollout pause deployment/nginx-deployment                                   //to pause a deployment
###
    kubectl rollout resume deployment/nginx-deployment                                  //to resume a deployment
###
    kubectl patch deployment/nginx-deployment -p '{"spec":{"progressDeadlineSeconds":600}}'  //set the spec progressDeadlineSeconds to report lack of progress for a Deployment after 10 min
###
    kubectl get deployment nginx-deployment -o yml                                        //to get the Deployment status
###
    kubectl proxy --port=8080
    curl -X DELETE  'localhost:8080/apis/apps/v1/namespaces/default/replicasets/frontend' \
    > -d '{"kind":"DeleteOptions","apiVersion":"v1","propagationPolicy":"Foreground"}' \
    > -H "Content-Type: application/json"                                                  //delete a ReplicaSet without affecting any of its Pods using kubectl delete with --cascade=orphan
###
    kubectl get ds                                                                         //to check daemonset
###
    kubectl describe ds                                                                    //to describe daemonset
###
    kubectl delete ds fluentd-ds                                                           //to delete daemonset
###
    kubectl get jobs                                                                       //to check jobs
###
    kubectl logs countdown-6fwdf                                                           //to check the logs of specific job
###
    kubectl describe jobs countdown
###
    kubectl delete jobs countdown
###
    kubectl get service
    kubectl get svc
###
    kubectl get svc -l app=nginx-app
###
    kubectl describe service my-service
###
    kubectl get nodes -o wide | awk '{print $7}'                                           //to check external ip of node
###
    kubectl get nodes -o yaml
###
    kubectl delete svc my-service
###
    kubectl describe service my-service | grep Load
###
    kubectl get ep my-nginx                                                                //to get endpoint
###
    kubectl exec my-nginx-3800858182-jr4a2 -- printenv | grep SERVICE                      //to print environment of service
###
    kubectl describe ingress
###
    kubectl get pv
###
    kubectl describe pv <pvname>
###
    kubectl get storageclass
###
    kubectl describe storageclass <storageclassname>
###
    watch kubectl get all -o wide
###
    kubectl get pod redis --watch
###
    kubectl create cm game-config --from-file=configure-pod-container/configmap/                              //to create configmaps
###
    kubectl get configmaps
###
    kubectl get cm game-config -o yaml
###
    kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt           //to create secret
###
    kubectl get secrets
###
    kubectl describe secrets db-user-pass
###
    kubectl exec secret-env-pod -- env | grep SECRET
###
    kubectl get ns kube-system -o=jsonpath='{.metadata.uid}'                                                    //to get cluster id
###