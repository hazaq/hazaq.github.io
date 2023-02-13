---
layout: post
title: ECS Custom Constraints for Instance Lifecycle 
date: 26 August 2020 11:33:00 +0400
categories: [AWS, ECS, DevOps]
tags: [Lambda, ECS]
---
<br/>
### Amazon ECS Task Placement Constraints
Amazon ECS task placement constraints are a very useful way to handle the placement of containers in an ECS cluster. Task placement constraints are rules that are considered during task placement. We may want to apply these rules to make sure that the task that we are launching are placed on an EC2 instance with the correct Amazon Machine Image (AMI), Operationg System (OS), CPU architecture or the instance type. We may have situations where we are running a heterogenous ECS cluster, for example, a mix of different EC2 instance types and we want to ensure that our tasks run on the correct instance type. In these situations we have to use ECS task placement constraints to ensure that we get the desired task placement. Unfortunately, Amazon ECS provides only a handful of task placement constraint attributes out of the box. However, we can leverage custom attributes to build and apply our own constraints on ECS tasks. In this blog post I will demonstrate, how we can use custom attributes to handle a very common use case of managing tasks to run on the correct instance lifecycle type (On-Demand | Spot).  

<br/>
Spot instances are a great way to save compute cost on AWS, you can get up to 90% discount on the On-Demand cost for spare compute capacity available. But these spot instances can be terminated or unavailable if the Availability Zone (AZ) is running out of capacity or for other reasons with only few a seconds of notice. This may not be ideal for some workloads, say, if you are running stateful applications.  

<br/>
### Configuring the Cluster with Spot instances  
If the ECS cluster was launched using the ECS console with the default setting we may not have the option to add spot instances to our cluster or auto-scale the cluster on spot instances. In order to enable this capability we need to make some modification to our autoscaling group. First we need to convert our launch configuration to a launch template.  
<br/>
Go to the launch configuration's tab and select the lunch configuration created by our ECS cluster.  
From 'Actions' click 'Copy to launch template'  
<br/>
![Copy-launch-template](/assets/images/ecs-custom-constrains-01.png)
<br/>  
Once the launch template is created we need to update the autoscaling group, with the launch template.  
Select the ECS Autoscaling group and click Edit  
<br/>
![Autoscaling-group](/assets/images/ecs-custom-constrains-02.png)  
<br/>  
Under the Launch Configuration dialog box click '<span style="color:blue">Switch to launch template</span>'  
<br/>
![Autoscaling-group-2](/assets/images/ecs-custom-constrains-03.png)
<br/>
Select the launch template that was created in the previous step  
<br/>
![Autoscaling-group-3](/assets/images/ecs-custom-constrains-04.png)
<br/>  
Now we can select spot instances as part of our ECS cluster  
<br/>
![Autoscaling-group-4](/assets/images/ecs-custom-constrains-05.png)
<br/>
I have configured my autoscaling group to launch the first 4 EC2 instances to be On-Demand but next 4 EC2 instances will have 1 EC2 (25%) with On-Demand and 3 EC2 (75%) with Spot instances.  
<br/>
### Applying the Custom Attributes
<br/>
While this saves us a lot of cost it also creates a problem, for some critical applications where it is not acceptable to interrupt the service if a spot instance is terminated and this can happen quite randomly from few times a day to few times a month. We need to restrict our ECS services to only launch task on On-Demand instances but AWS ECS instances does not have a built in attribute to handle instance lifecycle (On-Demand | Spot). In this case we can use custom attributes, and create a constraint based on custom attribute.  
<br/>
Go to the ECS console and select the container instance click on 'Action' -> 'View/Edit Attributes'   
<br/>
![ecs-cluster](/assets/images/ecs-custom-constrains-06.png)
<br/> 
Under 'Custom attributes' click '<span style="color:blue">Add attribute</span>' and now you can add a custom attributes for example I am adding the following attribute   
```
{  
	'Name' : 'LifeCycle', 
	'Value' : 'On-Demand' 
} 
```
<br/>
![ecs-attribute](/assets/images/ecs-custom-constrains-07.png)  
<br/>
You also use the below aws cli command to add the attribute.  
<br/>
```terminal
$ aws ecs put-attributes --attributes name=LifeCycle,value=On-Demand,targetId=arn
```
<br/>
Similarly we can tag spot instances with LifeCycle: Spot  
<br/>
In the task definition of the our service we can add a constraint of type memberOf with the following Expression `attribute:LifeCycle == On-Demand`  
<br/>
![ecs-task](/assets/images/ecs-custom-constrains-08.png)  
<br/>
### Automate the update of Instance attributes 
So far so good, but the container instances in our cluster are part of an autoscaling group which means that new instances could be added or removed dynamically and it will be impractical to add these attributes manually every time.  
<br/>
We can add custom attributes by update the `/etc/ecs/ecs.config` file. Below is a script that I have added into the user data of the my launch template.  
<br/>
```terminal
#!/bin/bash
echo ECS_CLUSTER=ecs-demo >> /etc/ecs/ecs.config
Instance_lifecycle=$(curl http://169.254.169.254/latest/meta-data/instance-life-cycle)
if [ "$Instance_lifecycle" == "on-demand" ]
then
  echo 'ECS_INSTANCE_ATTRIBUTES={"LifeCycle": "On-Demand"}' >> /etc/ecs/ecs.config
else
  echo 'ECS_INSTANCE_ATTRIBUTES={"LifeCycle": "Spot"}' >> /etc/ecs/ecs.config
fi
```
<br/>
Now every time a new container instance starts up its attributes will be populated automatically.  
<br/>
### Automate the update of Instance attributes (<span style="color:red">The Hard Way</span>)
<br/>
(*Just for fun I wanted to try something different, here I have used a lambda function to update the same attributes.*)  
<br/>
Let's create a Cloudwatch event to capture every time a new container instances is registered with our cluster and use a simple lambda function to update the attributes of the container instances.  
First the lambda function.  
<br/>
```ruby
import boto3
import os

ecs_cluster_name = os.environ['ECS_CLUSTER_NAME']
ec2 = boto3.client('ec2')
ecs = boto3.client('ecs')

def lambda_handler(event, context):
    
    ecs_instance_arn = event['detail']['responseElements']['containerInstance']['containerInstanceArn']
    ecs_instance_id = event['detail']['responseElements']['containerInstance']['ec2InstanceId']
    
    if ecs_cluster_name == event['detail']['requestParameters']['cluster']:
        ec2_response = ec2.describe_instances( InstanceIds=[ ecs_instance_id ])
        try:
            lifecycle = ec2_response['Reservations'][0]['Instances'][0]['InstanceLifecycle']
        except KeyError:
            lifecycle = "On-Demand"
        
        try:
            ecs.put_attributes(cluster=ecs_cluster_name, attributes=[ { 'name':'LifeCycle', 'value': lifecycle, \
                                    'targetType': 'container-instance', 'targetId': ecs_instance_arn }])
            print('Added the attribute "LifeCycle": "' + lifecycle + '" to the instance with id ' + ecs_instance_id)
        except:
            print ('Failed to add the attribute to ECS cluster' + ecs_cluster_name)
    else:
        print ('ECS cluster name does not match')
        print ('Looking for Cluster: ' + ecs_cluster_name + ' event was for Cluster: ' + \
                        event['detail']['requestParameters']['cluster'])
        
```
<br/>
The IAM role for the lambda function should have update attributes permission on the ECS cluster.   
And finally the Cloudwatch event  
<br/>
![cloudwatch-event](/assets/images/ecs-custom-constrains-09.png)  
<br/>
The target of the above Cloudwatch event should be the lambda function we created previously.  
Now every time a new container instance is registered with ECS cluster the Cloudwatch event triggers the lambda function which will update our attribute.  
<br/>
### Conclusion  
Amazon ECS task placement constrains are a very powerful feature available to the developers and cluster administrators. Not all attributes for the task placements are available but with custom attributes you can practically apply any constraint that you can think of and there will always be more than one way of doing it.  

#### Special Thanks 
Leonard Lin @Peoplegeist
