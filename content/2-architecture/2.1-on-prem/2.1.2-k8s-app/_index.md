---
title : "The application architecture is deployed using Kubernetes on On-premises environment"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.1.2 </b> "
---

![app-phonebook](/images/002-architect/app-k8s.png)

The phonebook application architecture includes 3 modules (decouple services):
-	**Database Module**: Store data in MySQL database, deployed using StatefulSet, data is stored in static volume
-	**web-service module**: Responsible for implementing add/update/detele phonebook APIs that interact with the Database module, deployed using Deployment and auto scaling using HPA (Horizontal Pod Autoscaling)
-	**result-service module**: Responsible for implementing the phonebook search APIs to interact with the Dataabse module, similar to the web-service module

Users access through the domain, Nginx Ingress Controller is responsible for directing requests according to Ingress rules, requests will be redirected to Serivce and from Service redirected to Pod.
