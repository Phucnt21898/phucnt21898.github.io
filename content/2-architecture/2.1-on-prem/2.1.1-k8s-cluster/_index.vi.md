---
title : "Kubernetes cluster architecture on On-premises environment"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1.1 </b> "
---

![k8s-cluster](/images/002-architect/k8s-cluster.png)

Kiến trúc này đảm bảo High Availability nên có 3 Server Control Plane với một cụm external etcd gồm 3 Server, 2 Server chịu trách nhiệm cân bằng tải theo mô hình active-standby và Worker node.
Vì vậy trên mô hình On-Premise chúng ta phải quản lý toàn bộ kiến trúc như trên và xử lý các sự cố xảy ra, luôn đảm bảo tính High Availability và đảm nhiệm các kịch bản backup & recovery cho Infra khi có sự cố xảy ra.
