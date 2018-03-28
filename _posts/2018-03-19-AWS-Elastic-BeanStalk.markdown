---
layout: post
title:  "Knowledge: AWS Elastic Beanstalk"
date:   2018-03-19 10:30:00 +0400
categories: AWS
---

## Elastic BeanStalk  
Elastic Beanstalk provides developers and systems administrators an easy, fast way to deploy and manage their applications without having to worry about AWS infrastructure.  
*Terminology:*    
Application  
An Elastic Beanstalk application is a logical collection of Elastic Beanstalk components, including environments, versions, and environment configurations. It is a containor for all the other components that make up elastic beanstalk. An application itself does not cost anything. 
Application Version  
In Elastic Beanstalk, an application version refers to a specific, labeled iteration of deployable code for a web application. An application version points to an Amazon Simple Storage Service (Amazon S3) object that contains the deployable code.  
Environment  
An application might have 1 or more environment. The infrastructure components are assosiated with an environment like EC2 ELB etc. Each environment runs only a single application version at a time. When you create an environment, Elastic Beanstalk provisions the resources needed to run the application version you specified.  
Environment Tier  
When you launch an Elastic Beanstalk environment, you first choose an environment tier. The environment tier that you choose determines whether Elastic Beanstalk provisions resources to support an application that handles HTTP requests or an application that pulls tasks from a queue.  
Environment Configuration  
An environment configuration identifies a collection of parameters and settings that define how an environment and its associated resources behave.  
Configuration Template  
A configuration template is a starting point for creating unique environment configurations.  

If an EC2 instance in an application becomes unhealthy and can not recover on its own Elastic Beanstalk plane will terminate the instance and Auto Scaling will launch the new instance. 
Using Route53 elastic beanstalk environment can be added to a domain.  

**Deployment Options**  
All at Once   
Rolling Deployment  
Rolling Deployment w/additional Batch  
Immutable   

# Configuration Options
configuration options precedence from highest to lowest  
Settings applied directly to the environment: Settings specified during a create environment or update environment operation on the Elastic Beanstalk API by any client, including the AWS Management Console, EB CLI, AWS CLI, and SDKs.  
Saved Configurations: Settings for any options that are not applied directly to the environment are loaded from a saved configuration, if specified.  
Configuration Files (.ebextensions): Settings for any options that are not applied directly to the environment, and also not specified in a saved configuration, are loaded from configuration files in the .ebextensions folder at the root of the application source bundle.   
Default Values: If a configuration option has a default value, it only applies when the option is not set at any of the above levels.  

**Configuration Files** (.ebextensions)  
Use .ebextensions to configure options that are required to make your application work, and provide default values for other options that can be overridden at a higher level of precedence. Options specified in .ebextensions have the lowest level of precedence and are overridden by settings at any other level.  
Configuration files support the following keys that affect the Linux server your application runs on.  
Keys
Packages, Groups, Users, Sources, Files, Commands, Services, Container Commands  
**Saved Configurations**  
Saved configurations are stored in the Elastic Beanstalk S3 bucket in a folder named after your application. For example, configurations for an application named my-app in the us-west-2 region for account number 0123456789012 can be found at s3://elasticbeanstalk-us-west-2-0123456789012/resources/templates/my-app.   

# Configuring HTTPS  
You can configure a secure listener on your load balancer with a configuration file like the following.  
```
option_settings:
  aws:elb:listener:443:
    SSLCertificateId: arn:aws:acm:us-east-2:1234567890123:certificate/####################################
    ListenerProtocol: HTTPS
    InstancePort: 80
```

# Creating .ebextensions  
Few tips for creating the .ebextension file.  
If you are getting this error `java.lang.Long cannot be cast to java.lang.String` it might be because you are missing quotes` " ` in any of the values.  
If you want to define setting such as AutoScaling Group, Load balancer type etc in .ebextensions configuration then choose **Custom configuration** from **configure more options** while creating the environment.   

# Launch Now URL  
You can construct launch now url for any application which help you launch an application build in elastic beanstalk with a single url.  
```
https://console.aws.amazon.com/elasticbeanstalk/?region=us-west-2#/newApplication?applicationName=YourCompanySampleApp&solutionStackName=PHP&sourceBundleUrl=http://s3.amazonaws.com/mybucket/myobject&environmentType=SingleInstance&tierName=WebServer&instanceType=m1.small&withVpc=true&withRds=true&rdsDBEngine=postgres&rdsDBAllocatedStorage=6&rdsDBInstanceClass=db.m1.small&rdsMultiAZDatabase=true&rdsDBDeletionPolicy=Snapshot
```
