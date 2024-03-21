---
title : "Cài đặt eksctl tool"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 3.1.5 </b> "
---

**Step 1:** Chỉ định biến môi trường cho ARM systems, set ARCH: `arm64`, `armv6` or `armv7` 
    
    ARCH=amd64
    PLATFORM=$(uname -s)_$ARCH

**Step 2:** Download eksctl-install file
    
    curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
    (Optional) Verify checksum
    
    curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check

**Step 3:** Giải nén sau đó move vào folder bin
   
    tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
    
    sudo mv /tmp/eksctl /usr/local/bin

