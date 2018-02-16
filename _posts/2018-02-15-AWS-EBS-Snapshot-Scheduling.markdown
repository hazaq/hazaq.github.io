---
layout: post
title:  "AWS: EBS Snapshot Scheduling"
date:   2018-02-15 12:56:00 +0400
categories: AWS
---

**Overview**  
In Amazon RDS you can schedule snapshot creation and deletion were snapshots are automatically created and delete after the retention period and you can do all this with in the RDS management console while or after creating the RDS. To do this with Amazon EBS volumes for EC2 there are couple of things you need to do, first schedule automated EBS snapshots using CloudWatch Event rule and then delete old snapshots using AWS Lambda function.  

**Schedule Automated Amazon EBS Snapshots Using CloudWatch Events**  
You can follow [these](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/TakeScheduledSnapshot.html) instructions  
Create a Rule  
 **1)** Open the CloudWatch  
 **2)** In the navigation pane, choose Events, Create rule  
 **3)** For Event Source: Choose Schedule. Choose Fixed rate of and specify the schedule interval.  
 **4)** For Targets, choose Add target and then select EC2 Create Snapshot API call.  
 **5)** For Volume ID, choose an EBS volume.  
 **6)** Choose Configure details.  
 **7)** For Rule definition, type a name and description for the rule.  
 **8)** For AWS permissions, choose the option to create a new role and create. This opens the IAM console in a new tab. The new role grants the built-in target permissions to access resources on your behalf. Choose Allow. The tab with the IAM window closes.  
 **9)** Choose Create rule.

**AWS Lambda function to delete old Snapshots**  
Now lets setup something to delete old snapshots else the snapshots will keep on piling up and it is impractical to delete them everyday manually. You can use lambda function to delete old snapshots.  
Lets first create the IAM permission required by the Lambda function. In the below template we are giving the CloudWatch log permissions so we can log the output of the lambda function as we purge the snapshots.  
```ruby
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:*"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": "ec2:Describe*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateSnapshot",
                "ec2:DeleteSnapshot",
                "ec2:CreateTags",
                "ec2:ModifySnapshotAttribute",
                "ec2:ResetSnapshotAttribute"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

Now we can create the lambda function.   
**1)** Go to the lambda function page and click on **Create a function**  
**2)** Select **Author from scratch** 
![Lambda image 1](/assets/images/lambdaimage1.png)  
**3)** Enter a Name like PurgeOldSnaphosts  
**4)** under Runtime select python 2.x  
**5)** For Roles select **Choose an exsisting role**, select the rule that you created previously and create function.  
**6)** Now under the Configuration page scroll down to **Function Code** and copy [this](https://github.com/hazaq/purgeOldSnapshots/blob/master/purge_snapshots.py) code.  
Don't forget to change the following variables in the function, according to your environment.  
* snap_vol = 'vol-12345678asdfg' # The volume ID that needs to purged  
* snap_exclude = [ 'snap-09876wxyz' ] # Any old snapshot you want to exclude from getting purged
* region = 'us-west-2' # Region where the purge should occure   
* owner_ids = [ '111111111111' ] # Owner id of the snapshot creater  
* days = 31 # Retention period of old snapshots  

Finally we need to create a CloudWatch Event rule to trigger the function just like we did for creating the snapshots. So follow the same steps in **Schedule Automated Amazon EBS Snapshots Using CloudWatch Events** with the exception of step 4 where you need to choose a Lambda function as a Target.  
Our lambda function should look like this now.  
![lambdaimage2](/assets/images/lambdaimg2.png)
