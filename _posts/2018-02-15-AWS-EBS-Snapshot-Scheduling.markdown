---
layout: post
title:  "AWS: EBS Snapshot Scheduling"
date:   2018-02-15 12:56:00 +0400
categories: AWS
---

**Overview**  
In Amazon RDS you can schedule snapshot creation and deletion were snapshots are automatically created and delete after the retention period and you can do all this with in the RDS management console while or after creating the RDS. To do this with Amazon EBS volumes for EC2there are couple of things you need to do, first cuhedule automated EBS snapshots using CloudWatch Event rule and then delete old snapshots using AWS Lambda function.  

**Schedule Automated Amazon EBS Snapshots Using CloudWatch Events**  
You can follow [these](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/TakeScheduledSnapshot.html) instructions  
Create a Rule  
 1) Open the CloudWatch  
 2) In the navigation pane, choose Events, Create rule  
 3) For Event Source: Choose Schedule. Choose Fixed rate of and specify the schedule interval.  
 4) For Targets, choose Add target and then select EC2 Create Snapshot API call.  
 5) For Volume ID, choose an EBS volume.  
 6) Choose Configure details.  
 7) For Rule definition, type a name and description for the rule.  
 8) For AWS permissions, choose the option to create a new role and create. This opens the IAM console in a new tab. The new role grants the built-in target permissions to access resources on your behalf. Choose Allow. The tab with the IAM window closes.  
 9) Choose Create rule.

**AWS Lambda function to delete old Snapshots**
