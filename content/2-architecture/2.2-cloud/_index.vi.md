---
title : "Kiến trúc hệ thống khi được triển khai vào cụm AWS EKS"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---
![eks-cluster](/images/002-architect/eks-cluster.png)

Đây là Cơ sở hạ tầng trên môi trường AWS Cloud khi được di chuyển từ On-premises. Trên môi trường AWS Cloud, AWS chịu trách nhiệm quản lý Control Plane thay vì chúng ta phải tự quản lý Control Plane, đảm bảo tính high availability và các tình huống sao lưu & khôi phục khi xảy ra sự cố trên môi trường On-premises. Giảm gánh nặng quản lý cơ sở hạ tầng và giúp chúng ta tập trung vào phát triển ứng dụng.

Trên môi trường AWS Cloud, người dùng sẽ truy cập Phonebook service bằng domain được phân giải bằng AWS Route53. Sau đó, requests sẽ đi đến Bộ cân bằng tải AWS được quản lý bởi AWS ALB Ingress Controller thay vì đi qua Ingress Controller Nginx trên môi trường On-premises, khi đó requests sẽ đi thẳng đến các Pod được triển khai trên worker node
Team DevOps sẽ quản lý cụm AWS EKS bằng cách thực hiện các hành động trên server Bastion. Team DevOps sẽ truy cập server Bastion thông qua AWS Instance Connect

**Chúng ta sẽ tiến hành di chuyển service từ môi trường On-premises sang môi trường AWS Cloud theo các bước sau:**
   1. Đầu tiên, chúng ta sẽ tạo server Bastion và cài đặt các công cụ cần thiết để thực hiện apply code Terraform và quản lý cụm AWS EKS.

   2. Tiếp theo, chúng ta sẽ xây dựng Cơ sở hạ tầng trên môi trường AWS Cloud (Sử dụng tool Infrastructure as code là Terraform)

   3. Tiếp theo chúng ta sẽ triển khai kiến trúc của Phonebook App sử dụng nền tảng Kubernetes trên cluster EKS tương tự như kiến trúc của Phonebook App trên môi trường On-premises (Lúc này các service của công ty đã được triển khai lên cluster EKS, nhưng chưa có dữ liệu)
  
   4. Cuối cùng, chúng ta sẽ triển khai sao lưu và khôi phục dữ liệu Ứng dụng trên môi trường On-premises vào cụm EKS