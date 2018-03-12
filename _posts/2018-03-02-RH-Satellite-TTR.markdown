---
layout: post
title:  "Knowledge: Red Hat Satellite 6 things to remember"
date:   2018-03-02 10:30:00 +0400
categories: RedHat
---
**Architecture**  
Key components built on mature open source projects:  
* Puppet: Configuration Management  
* FOREMAN: Provisioning  
* Pulp: Repository Management  
* KATELLO: Content/LifeCycle Management   
* Candlepin: Subscription Management  

Difference between redhat satellite vs capsule is the web UI, you dont have web UI in capsule server.  
LifeCycle Management: The lifecycle envirnment is a means to manage versions with in the datacenter. By default all content that come in to Satellite goes into the evnirnment called 'Library' from the Library administrators can move content to other envirnments such as Dev, Testing, Prod etc 
![LifeCycle](/assets/images/rhSat.png) 
 
Content View is a new feature that comes with satellite 6, it is means for modeling content  
Discovry: RH Satellite can automatically identify non provisioned host  
Hammer: Is the cli tool to manage Red Hat Satellite 6  

**Disconnected Satellite**  
Red Hat Satellite Server can still provision systems with the latest security updates, errata, and packages. This is achieved by using *katello-disconnected utility* and *Synchronization host*  
synchronization host is an intermediary system with an Internet connection. This host is in a separate network from the Satellite server.  
The synchronization host download content through Pulp. The content is then exported onto physical media and transferred to the disconnected Satellite server.  
![Disconnected Host ](/assets/images/rhSatDhost.png)  

**FOREMAN Plugins**  
foreman-tasks: Provides Dynflow infrastructure for using Dynflow for managing the tasks.  
foreman_bootdisk: Satellite plug-in for creating iPXE-based boot disks to provision hosts without the need for PXE infrastructure.  
foreman_discovery: MaaS Discovery plug-in engine for Satellite.  
foreman_docker: Provisions and manages Docker containers and images from Satellite.  
foreman_openscap: Satellite plug-in for managing security compliance reports.  
foreman_remote_execution: Brings remote execution to Satellite, completing the configuration management functionality with remote management functionality.  
foreman_theme_satellite: Enables building themes for Satellite.  
Katello: Content and subscription management plug-in for Satellite. 
redhat_access: Adds Red Hat Access knowledgebase search, case management, and diagnostics to Satellite.  

*Activation Keys*: Defines life-cycle environment, host collection, organization, subscription usage limit.  
*Host Groups*: Defines a set of default values for a host

**System Requirements**  
* Red Hat Enterprise Linux 7 Server 64-bit  
* A minimum of 2 CPU cores, 4 CPU cores are recommended  
* A minimum of 12 GB memory is required for the Satellite Server to function, 16 GB of memory or more is recommended  
* A unique host name, which can contain lower-case letters, numbers, dots (.) and hyphens (-)  
* A current Red Hat Satellite subscription  
* Full forward and reverse DNS resolution using a fully-qualified domain name  
The XFS file system is recommended for Red Hat Satellite 6 because it does not have the inode limitations that ext4 does.  

Repos Required :  
Red Hat Enterprise Linux $releasever - $basearch  
Red Hat Enterprise Linux Software collections  
Satellite6.x  

Installing Satellite6: 
```
yum install satellite -y
#list satellite scenarios
satellite-installer --list-scenarios
#The installer can be run multiple times to add, disable, and configure features.
satellite-installer --foreman-initial-organization hazaq-me \
--foreman-initial-location dxb \
--scenario satellite \
--foreman-proxy-tftp  true \
--foreman-proxy-dhcp true \
--foreman-proxy-dhcp-gateway "192.168.0.2" \
--foreman-proxy-dhcp-interface "eth0" \
--foreman-proxy-dhcp-range "192.168.0.200 192.168.0.250" \
--foreman-proxy-dns true \
--foreman-proxy-dns-forwarders "192.168.0.10" \
--foreman-proxy-dns-interface "eth0" \
--foreman-proxy-dns-reverse 0.168.192.in-addr.arpa \
--foreman-proxy-dns-server "127.0.0.1" \
--foreman-proxy-dns-zone "example.com" \
--foreman-admin-password 'password'

```
