---
title : "Kiến trúc ứng dụng được triển khai bằng Kubernetes trên môi trường on-premises"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.1.2 </b> "
---

![app-phonebook](/images/002-architect/app-k8s.png)

Kiến trúc ứng dụng danh bạ bao gồm 3 module (dịch vụ decouple):
- **Module database**: Lưu trữ dữ liệu trong cơ sở dữ liệu MySQL, được triển khai bằng StatefulSet, dữ liệu được lưu trữ trong static volume
- **Module web-service**: Chịu trách nhiệm triển khai các API Phonebook add/update/delete tương tác với module Cơ sở dữ liệu, được triển khai bằng cách sử dụng Deployment và tự động mở rộng quy mô bằng HPA (Horizontal Pod Autoscaling)
- **Module result-service**: Chịu trách nhiệm triển khai API search Phonebook để tương tác với module Dataabse, tương tự như module web-service

Người dùng truy cập thông qua domain, Nginx Ingress Controller có trách nhiệm điều hướng các request theo các rule Ingress, các request sẽ được chuyển hướng đến Serivce và từ Service được chuyển hướng đến Pod.