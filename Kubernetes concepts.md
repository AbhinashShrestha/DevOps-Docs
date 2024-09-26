Kubernetes provides you with:
- **Service discovery and load balancing** Kubernetes can expose a container using the DNS name or using their own IP address. If traffic to a container is high, Kubernetes is able to load balance and distribute the network traffic so that the deployment is stable.
- **Storage orchestration** Kubernetes allows you to automatically mount a storage system of your choice, such as local storages, public cloud providers, and more.
- **Automated rollouts and rollbacks** You can describe the desired state for your deployed containers using Kubernetes, and it can change the actual state to the desired state at a controlled rate. For example, you can automate Kubernetes to create new containers for your deployment, remove existing containers and adopt all their resources to the new container.
- **Automatic bin packing** You provide Kubernetes with a cluster of nodes that it can use to run containerized tasks. You tell Kubernetes how much CPU and memory (RAM) each container needs. Kubernetes can fit containers onto your nodes to make the best use of your resources.
- **Self-healing** Kubernetes restarts containers that fail, replaces containers, kills containers that don't respond to your user-defined health check, and doesn't advertise them to clients until they are ready to serve.
- **Secret and configuration management** Kubernetes lets you store and manage sensitive information, such as passwords, OAuth tokens, and SSH keys. You can deploy and update secrets and application configuration without rebuilding your container images, and without exposing secrets in your stack configuration.
- **Batch execution** In addition to services, Kubernetes can manage your batch and CI workloads, replacing containers that fail, if desired.
- **Horizontal scaling** Scale your application up and down with a simple command, with a UI, or automatically based on CPU usage.
- **IPv4/IPv6 dual-stack** Allocation of IPv4 and IPv6 addresses to Pods and Services
- **Designed for extensibility** Add features to your Kubernetes cluster without changing upstream source code

## Important
 Kubernetes is not a mere orchestration system. In fact, it eliminates the need for orchestration. The technical definition of orchestration is execution of a defined workflow: first do A, then B, then C. In contrast, Kubernetes comprises a set of independent, composable control processes that continuously drive the current state towards the provided desired state. It shouldn't matter how you get from A to C. Centralized control is also not required. This results in a system that is easier to use and more powerful, robust, resilient, and extensible.
![[Pasted image 20240728142445.png]]

#### Kubernetes has a "hub-and-spoke" API pattern.

#### Incoming Network Access resource [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) is a completely different type of resource from _Services_. If you've got your [OSI model](https://en.wikipedia.org/wiki/OSI_model) memorized, it works in layer 7 while services work on layer 4.
### Kubernetes DNS, also known as kube-dns or CoreDNS, is a built-in service within Kubernetes that provides a reliable and scalable DNS-based mechanism for service discovery. It acts as a central lookup service, allowing services and pods to locate each other by name within the cluster.

### Kubernetes DNS operates by creating a DNS record for each service and pod within the cluster. When a pod or service is created, Kubernetes automatically assigns it a DNS name in the form `<service-name>.<namespace>.svc.cluster.local` or `<pod-name>.<pod-namespace>.<svc>.cluster.local`.

### Kubernetes publishes information about Pods and Services which is used to program DNS. Kubelet configures Pods' DNS so that running containers can lookup Services by name rather than IP.

Kubernetes provides several built-in workload resources: && The built-in APIs for managing workloads are:

- [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) and [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) (replacing the legacy resource [ReplicationController](https://kubernetes.io/docs/reference/glossary/?all=true#term-replication-controller)). Deployment is a good fit for managing a stateless application workload on your cluster, where any Pod in the Deployment is interchangeable and can be replaced if needed.
- [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) lets you run one or more related Pods that do track state somehow. For example, if your workload records data persistently, you can run a StatefulSet that matches each Pod with a [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/). Your code, running in the Pods for that StatefulSet, can replicate data to other Pods in the same StatefulSet to improve overall resilience.
- [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) defines Pods that provide facilities that are local to nodes. Every time you add a node to your cluster that matches the specification in a DaemonSet, the control plane schedules a Pod for that DaemonSet onto the new node. Each pod in a DaemonSet performs a job similar to a system daemon on a classic Unix / POSIX server. A DaemonSet might be fundamental to the operation of your cluster, such as a plugin to run [cluster networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-network-model), it might help you to manage the node, or it could provide optional behavior that enhances the container platform you are running.
- [Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/) and [CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/) provide different ways to define tasks that run to completion and then stop. You can use a [Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/) to define a task that runs to completion, just once. You can use a [CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/) to run the same Job multiple times according a schedule.


In the wider Kubernetes ecosystem, you can find third-party workload resources that provide additional behaviors. Using a [custom resource definition](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/), you can add in a third-party workload resource if you want a specific behavior that's not part of Kubernetes' core. For example, if you wanted to run a group of Pods for your application but stop work unless _all_ the Pods are available (perhaps for some high-throughput distributed task), then you can implement or install an extension that does provide that feature.


### Pods are created  using workload resources such as [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) or [Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/). If your Pods need to track state, consider the [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) resource.


### Restarting a container in a Pod should not be confused with restarting a Pod. A Pod is not a process, but an environment for running container(s). A Pod persists until it is deleted.
\

#### the StatefulSet controller ensures that the running Pods match the current pod template for each StatefulSet object. If you edit the StatefulSet to change its pod template, the StatefulSet starts to create new Pods based on the updated template. Eventually, all of the old Pods are replaced with new Pods, and the update is complete.

### Inside a Pod (and **only** then), the containers that belong to the Pod can communicate with one another using `localhost`. When containers in a Pod communicate with entities _outside the Pod_, they must coordinate how they use the shared network resources (such as ports). Within a Pod, containers share an IP address and port space, and can find each other via `localhost`. The containers in a Pod can also communicate with each other using standard inter-process communications like SystemV semaphores or POSIX shared memory. Containers in different Pods have distinct IP addresses and can not communicate by OS-level IPC without special configuration. Containers that want to interact with a container running in a different Pod can use IP networking to communicate.

Kubernetes IP addresses exist at the `Pod` scope - containers within a `Pod` share their network namespaces - including their IP address and MAC address. This means that containers within a `Pod` can all reach each other's ports on `localhost`. This also means that containers within a `Pod` must coordinate port usage, but this is no different from processes in a VM. This is called the "IP-per-pod" model.

How this is implemented is a detail of the particular container runtime in use.### ## The Kubernetes network model[](https://kubernetes.io/docs/concepts/services-networking/#the-kubernetes-network-model)

Every [`Pod`](https://kubernetes.io/docs/concepts/workloads/pods/) in a cluster gets its own unique cluster-wide IP address (one address per IP address family). This means you do not need to explicitly create links between `Pods` and you almost never need to deal with mapping container ports to host ports.  
This creates a clean, backwards-compatible model where `Pods` can be treated much like VMs or physical hosts from the perspectives of port allocation, naming, service discovery, [load balancing](https://kubernetes.io/docs/concepts/services-networking/ingress/#load-balancing), application configuration, and migration.

Kubernetes imposes the following fundamental requirements on any networking implementation (barring any intentional network segmentation policies):

- pods can communicate with all other pods on any other [node](https://kubernetes.io/docs/concepts/architecture/nodes/) without NAT
- agents on a node (e.g. system daemons, kubelet) can communicate with all pods on that node

Kubernetes networking addresses four concerns:

- Containers within a Pod [use networking to communicate](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/) via loopback.
- Cluster networking provides communication between different Pods.
- The [Service](https://kubernetes.io/docs/concepts/services-networking/service/) API lets you [expose an application running in Pods](https://kubernetes.io/docs/tutorials/services/connect-applications-service/) to be reachable from outside your cluster.
    - [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) provides extra functionality specifically for exposing HTTP applications, websites and APIs.
    - [Gateway API](https://kubernetes.io/docs/concepts/services-networking/gateway/) is an [add-on](https://kubernetes.io/docs/concepts/cluster-administration/addons/) that provides an expressive, extensible, and role-oriented family of API kinds for modeling service networking.
- You can also use Services to [publish services only for consumption inside your cluster](https://kubernetes.io/docs/concepts/services-networking/service-traffic-policy/).

##### [Service](https://kubernetes.io/docs/concepts/services-networking/service/)

Expose an application running in your cluster behind a single outward-facing endpoint, even when the workload is split across multiple backends.

##### [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

Make your HTTP (or HTTPS) network service available using a protocol-aware configuration mechanism, that understands web concepts like URIs, hostnames, paths, and more. The Ingress concept lets you map traffic to different backends based on rules you define via the Kubernetes API.

##### [Ingress Controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)

In order for an [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) to work in your cluster, there must be an _ingress controller_ running. You need to select at least one ingress controller and make sure it is set up in your cluster. This page lists common ingress controllers that you can deploy.

##### [Gateway API](https://kubernetes.io/docs/concepts/services-networking/gateway/)

Gateway API is a family of API kinds that provide dynamic infrastructure provisioning and advanced traffic routing.

##### [EndpointSlices](https://kubernetes.io/docs/concepts/services-networking/endpoint-slices/)

The EndpointSlice API is the mechanism that Kubernetes uses to let your Service scale to handle large numbers of backends, and allows the cluster to update its list of healthy backends efficiently.

##### [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

If you want to control traffic flow at the IP address or port level (OSI layer 3 or 4), NetworkPolicies allow you to specify rules for traffic flow within your cluster, and also between Pods and the outside world. Your cluster must use a network plugin that supports NetworkPolicy enforcement.

##### [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)

Your workload can discover Services within your cluster using DNS; this page explains how that works.

##### [IPv4/IPv6 dual-stack](https://kubernetes.io/docs/concepts/services-networking/dual-stack/)

Kubernetes lets you configure single-stack IPv4 networking, single-stack IPv6 networking, or dual stack networking with both network families active. This page explains how.

##### [Topology Aware Routing](https://kubernetes.io/docs/concepts/services-networking/topology-aware-routing/)

_Topology Aware Routing_ provides a mechanism to help keep network traffic within the zone where it originated. Preferring same-zone traffic between Pods in your cluster can help with reliability, performance (network latency and throughput), or cost.

##### [Networking on Windows](https://kubernetes.io/docs/concepts/services-networking/windows-networking/)

##### [Service ClusterIP allocation](https://kubernetes.io/docs/concepts/services-networking/cluster-ip-allocation/)

##### [Service Internal Traffic Policy](https://kubernetes.io/docs/concepts/services-networking/service-traffic-policy/)

If two Pods in your cluster want to communicate, and both Pods are actually running on the same node, use _Service Internal Traffic Policy_ to keep network traffic within that node. Avoiding a round trip via the cluster network can help with reliability, performance (network latency and throughput), or cost.


#### If your workload speaks HTTP, you might choose to use an [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) to control how web traffic reaches that workload. Ingress is not a Service type, but it acts as the entry point for your cluster. An Ingress lets you consolidate your routing rules into a single resource, so that you can expose multiple components of your workload, running separately in your cluster, behind a single listener.

#### A Service is an [object](https://kubernetes.io/docs/concepts/overview/working-with-objects/#kubernetes-objects) (the same way that a Pod or a ConfigMap is an object). You can create, view or modify Service definitions using the Kubernetes API. Usually you use a tool such as `kubectl` to make those API calls for you.

### At its core, a volume is a directory, possibly with some data in it, which is accessible to the containers in a pod. How that directory comes to be, the medium that backs it, and the contents of it are determined by the particular volume type used.
