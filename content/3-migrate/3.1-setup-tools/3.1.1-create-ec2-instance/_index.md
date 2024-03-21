---
title : "Create Bastion host"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1.1 </b> "
---
**Step 1:** Open the Amazon EC2 console at **https://console.aws.amazon.com/ec2/** -> Choose Launch Instance.

**Step 2:** Configure EC2 Instance:

Name and tags: Bastion host, select AMI:
![???](/images/003.1-create-bastion-host/1.png)

EC2 Instance type: t2.small
Configure Network:
![???](/images/003.1-create-bastion-host/2.png)

In which: Security Group for bastion allow rule is necessary for the Instance Connect service to be accessible

**Step 3:** Choose **Review and Launch**, then choose **Launch**
