---
title : "Cài đặt kubectl tool"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 3.1.6 </b> "
---
**Step 1:** Download binary file 

    curl -LO https://dl.k8s.io/release/v1.28.4/bin/linux/amd64/kubectl

**Step 2:** Install kubectl

    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

**Step 3:** Lệnh check version của kubectl

    kubectl version --client
