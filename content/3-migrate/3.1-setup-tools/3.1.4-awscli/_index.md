---
title : "Install awscli tool"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 3.1.4 </b> "
---

**Step 1:**  Update the local package index and to provide the system with the most recent information about available packages from the repositories 

    sudo apt update -y 


**Step 2:** Download awscli binary file 

    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"


**Step 3:** unzip then install 

    unzip awscliv2.zip
    sudo ./aws/install


**Step 4:** Configure for AWS CLI 

    aws configure
    aws_access_key_id = xxx
    aws_secret_access_key = xxx
    region = xxx
    output = xxx


