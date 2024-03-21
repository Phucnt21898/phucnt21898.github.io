---
title : "Setup Instance Connect to access Bastion host via ssh protocol"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.1.3 </b> "
---

Amazon EC2 Instance Connect is a simple and secure way to connect to your instances using Secure Shell (SSH). With EC2 Instance Connect, you can control SSH access to your instances using AWS Identity and Access Management (IAM) policies as well as audit connection requests with AWS CloudTrail events.

For AWS Instance Connect to be able to access Bastion Host, the Bastion Host must be located on the public network and the Security Group must have an Inbound rule opened for the refer IP range: https://ip-ranges.amazonaws.com/ip-ranges.json
To connect to your instance using the browser-based client from the Amazon EC2 console:

**Step 1:** Select the instance and choose Connect.
![???](/images/003.3-instance-connect/1.png)

**Step 2:** Click to Connect:
![???](/images/003.3-instance-connect/2.png)

We have successfully accessed Bastion Host using AWS EC2 Instance Connect:
![???](/images/003.3-instance-connect/3.png)

**_From here we will start installing the necessary tools_**