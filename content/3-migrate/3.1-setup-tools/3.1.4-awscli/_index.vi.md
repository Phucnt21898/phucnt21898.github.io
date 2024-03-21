---
title : "Cài đặt awscli tool"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 3.1.4 </b> "
---

**Step 1:**  Cập nhật chỉ mục gói cục bộ và cung cấp cho hệ thống thông tin mới nhất về các gói có sẵn từ repo 

    sudo apt update -y 


**Step 2:** Download awscli binary file 

    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"


**Step 3:** unzip sau đó install 

    unzip awscliv2.zip
    sudo ./aws/install


**Step 4:** Cấu hình cho AWS CLI 

    aws configure
    aws_access_key_id = xxx
    aws_secret_access_key = xxx
    region = xxx
    output = xxx


