---
title : "Tạo Bastion host"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1.1 </b> "
---
**Step 1:** Tuy cập Amazon EC2 console tại link **https://console.aws.amazon.com/ec2/** -> Chọn Launch Instance.

**Step 2:** Cấu hình EC2 Instance:

Name and tags: Bastion host, chọn AMI:
![???](/images/003.1-create-bastion-host/1.png)

EC2 Instance type: t2.small
Cấu hình Network:
![???](/images/003.1-create-bastion-host/2.png)

Trong đó: Security Group gắn vào bastion cho phép các rule cần thiết để Instance Connect service có thể truy cập vào bastion

**Step 3:** Chọn **Review and Launch**, sau đó chọn **Launch**

