---
title : "Kubernetes cluster architecture on On-premises environment"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1.1 </b> "
---

![k8s-cluster](/images/002-architect/k8s-cluster.png)

This architecture ensures High Availability, so there are 3 Server Control Plane with an External etcd cluster consisting of 3 Servers, 2 Servers are responsible for load balancing according to the active-standby model and Worker nodes.
So on the On-premise model, we have to manage and take care of the entire architecture as above and handle issues that occur, always ensuring High Availability and take care of backup & restore scenarios for Infrastructure when an incident occurs
