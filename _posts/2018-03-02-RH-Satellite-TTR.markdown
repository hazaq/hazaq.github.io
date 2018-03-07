---
layout: post
title:  "Knowledge: Red Hat Satellite Things TO Remember"
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
