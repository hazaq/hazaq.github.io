---
layout: post
title:  "OpenStack"
date:   2018-05-17 15:52:00 +0400
categories: OpenStack
---

#OpenStack components 

Keystone: Identity Service  
Glance: Image Service  
Nova: Compute Service  
Neutron: Networking  
Cinder: Storage Service  
Horizon: Web UI  

# Installing OpenStack
Two ways to install OpenStack.  
1. Packstack installation  
2. Manual installation  

**PackStack**  
PackStack is an automation tool to install Openstack.  
Make sure there is swapon the system. Before running the packstack maker sure that NetworkManager is stopped and disabled. And setting a static IP would be good idea. 
```
#Install the openstack repo
yum install centos-release-openstack-ocata 
yum install openstack-packstack 
```
