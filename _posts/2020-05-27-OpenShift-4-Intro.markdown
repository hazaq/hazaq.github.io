---
layout: post
title:  "Red Hat OpenShift 4 Notes"
date:   2020-05-27 12:45:00 +0400
categories: RedHat
---

# Introduction  
Red Hat Openshift Container Platform is a container orchestration platform based on kubernetes and extends the platform with features to bring flexibility and scalability.  
Some of the important components of RHOCP includes  
CoreOS for immutable operating system  
CRI-O engine (replacing the Docker engine)  
Kubernetes for orchestration.  
  
## Openshift features  
**High Availability** : RHOCP uses kubernetes to provide high availability for user application and internal components containers. uses etcd to store the state of the containers.  
**Lightweight OS** : RHEL CoreOS is a Lightweight immutable OS which means that the entire OS is update using a singe image instead of updating it package by package. RHOCP manages the update and configuration of the RHEL CoreOS.  
**Load Balancing** : Three types of load balancers are available; an external load balancer provides access to openshift api, the HAproxy load balancer for applications and an internal load balancer based on netfilter.  
**Logging and Monitoring** : uses Prometheus for monitoring the cluster and elasticsearch for log collection.  
**Service Discovery** : Runs an internal DNS service for name resolution and service discovery.  
**Storage** : Provides an abstraction layer for storage backend, application can use block and file system storage using unified storage definitions independent of the storage backend.  
**Application Management** : uses the Source-to-Image (S2I) to automatically build containers and run them on OpenShift. The Developer catalog has prebuild templates for different runtime languages, databases and messaging services. You can use operators to extent the catalog.  
**Cluster Extensibility** : To extend the openshift capabilities you can install new operators by using Operator Lifecycle Manager (OLM) (https://operatorhub.io).  

## OpenShift Architecture  
**Declarative Architecture** : Declarative architectures allow for self-optimizing and self-healing systems that are easier to manage than imperative architectures.  
**Control Plan** : Master nodes only runs control plan services which includes REST API, etcd and the platform controllers. User pods can only run on worker nodes.  
[Image]
Static pods are pods that do not require connection to the API server to start.  
**Extensions** : RHOCP comes with preinstalled extensions such as the container image registry and the web console manages by operators.  
**Storage Class** :  OpenShift ships with integrated storage plug-ins and storage classes that rely on the underlying cloud or virtualization platform to provide dynamically provisioned storage.  
