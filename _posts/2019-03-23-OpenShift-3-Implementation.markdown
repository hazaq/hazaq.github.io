---
layout: post
title:  "Implementing OpenShift"
date:   2019-03-23 22:07:00 +0400
categories: Containers
---

## Overview
Red Hat OpenShift Container Platform is Platform-as-a-Service (PaaS). Supports a wide selection of programming languages and frameworks such as Java, Ruby, PHP. Built on Red Hat Enterprise Linux and Google Kubernetes.  
## Core Concept  
**Pod:**  
OpenShift runs Docker images in containers wrapped by meta object called pod.  
One or more containers deployed together on one host.  
Smallest Atomic unit in OpenShift.  
**Service:**  
Services allow simple internal load balancing across application components.  
Service represents group of pods, provides this permanent IP and host name.  
**Labels:**  
Used to organize, group, or select API objects.  
Labels are simple key-value pairs.  
**Node Host:**  
Runs on RHEL and managed by master host.  
**Master Host:**  
Master provides API that tools and systems interacts wtih.  
Authoraization is handled via fine-grained role-based access control (RBAC)  
Web UI, CLI, IDEs all go through authenticated, RBAC-controlled API.  
Scheduler; Determines pod placement, Takes current memory, CPU, and other environment utilization into account when placing pods on nodes.  
**Service Broker and Service Catalog:**  
Service catalog allows cluster administrators to integrate multiple platforms using single API specification.  
**Routing layer:**  
Provides eternal cleints access to application running inside openshift.  
Provides automated load balancing, perfrom health check on pods  
**Replication Controller**  
Ensures that specified number of pod replicas running at all times  
Selector is set of labels assigned to all pods managed by replication controller.  
RC is not responsible for Auto Scalling  
**Route:**  
Exposes service by giving it externally reachable host name

### Pods  
One or more containers deployed together on host. Each pod allocated own internal IP address, owns entire port range. Containers within pods can share local storage and networking.  
Cannot change pod definition while running. Recommended to use higher-level controllers such as a DeploymentConfig or DaemonSet to manage pods.  
Pod definition file is also called manifest, normally created by a hider-level controller.  
```
 apiVersion: v1
 kind: Pod
 metadata:
 image: openshift3/example-image:v1.1.0.6 
 imagePullPolicy: IfNotPresent
 name: registry
 ports:                              
 - containerPort: 5000
   protocol: TCP
 resources: {}
 securityContext: { ... }            
 volumeMounts:                       
 - mountPath: /registry
   name: registry-storage
 - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
   name: default-token-br6yz
   readOnly: true
```

### Services  
service serves as internal load balancer. Provides consistent internal IP address. Service uses label selector to find running containers that provide certain network service on certain port. Can access server by IP address and DNS name.  
When you use Default Router (HAProxy), you bypass service load balancing. You use the service only to find out which pods the service represents. Default Router does the load balancing.  
```
apiVersion: v1
kind: Service
metadata:
  annotations:
    openshift.io/generated-by: OpenShiftNewApp
  creationTimestamp: 2018-02-07T16:04:28Z
  labels:
    app: myapp
    createdBy: myapp-template
    name: myapp
  name: myapp
  namespace: myprojectname
spec:
  clusterIP: 172.30.246.190
  ports:
  - name: myapp
    port: 9000 
    protocol: TCP
    targetPort: 9090 
  selector:  
    name: myapp
  sessionAffinity: None
  type: ClusterIP 
status:
  loadBalancer: {}
```

### Lables  
Lets services reference groups of pods. Most objects can include labels in metadata.  

### Scheduler  
Determines placement of new pods onto nodes within OpenShift cluster. Reads pod data, tries to find node that is good fit.  
three-step operation:  
Filters, Runs nodes through list of filter functions called predicates.  
Prioritizes, Passes each node through series of priority functions.  
Selects, Selects node with highest score to host pod.  

### Builds  
used to transform source code into runnable image.  
BuildConfig object: definition of entire build process.  
Three build strategies: Docker build, S2I build, Custom build  

