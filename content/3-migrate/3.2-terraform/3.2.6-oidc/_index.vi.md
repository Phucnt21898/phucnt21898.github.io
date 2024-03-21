---
title : "Tạo IAM OIDC provider EKS bằng cách sử dụng Terraform"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 3.2.6 </b> "
---

Để quản lý quyền cho các ứng dụng được triển khai trong Kubernetes. Chúng ta có thể attach policy trực tiếp vào các node Kubernetes. Trong trường hợp đó, Node Group sẽ có cùng quyền truy cập vào tài nguyên AWS. Hoặc chúng ta có thể tạo nhà cung cấp kết nối OpenID, nhà cung cấp này sẽ cho phép cấp quyền IAM dựa trên service account sử dụng bởi pod..

OpenID Connect là một giao thức xác thực cho phép xác minh danh tính người dùng khi người dùng đang cố truy cập điểm cuối HTTP được bảo vệ. OIDC là sự phát triển tiến hóa của các ý tưởng được triển khai trước đó trong OAuth và OpenID.

    data "tls_certificate" "eks" {
        url = aws_eks_cluster.phonebook-eks-cluster.identity[0].oidc[0].issuer 
    }

    resource "aws_iam_openid_connect_provider" "eks" {
      client_id_list  = ["sts.amazonaws.com"]
      thumbprint_list = [data.tls_certificate.eks.certificates[0].sha1_fingerprint]
      url             = aws_eks_cluster.phonebook-eks-cluster.identity[0].oidc[0].issuer
    }

Chúng ta hoàn thành việc tạo file main.tf, sau đó chúng ta sẽ chạy lệnh apply code Terraform

**terraform init:** khởi tạo một thư mục làm việc và tải xuống các plugin và mô-đun nhà cung cấp cần thiết, đồng thời thiết lập phần phụ trợ để lưu trữ trạng thái cơ sở hạ tầng của bạn

![???](/images/003.4-terraform/1.png)

**terraform plan:** tạo một lần chạy thử, xác định những hành động nào là cần thiết để đạt được trạng thái mong muốn được xác định trong tệp cấu hình Terraform

![???](/images/003.4-terraform/2.png)

**terraform apply:** để triển khai lên AWS cloud

![???](/images/003.4-terraform/3.png)

Nhập **yes**, sau AWS resources bắt đầu được khởi tạo

![???](/images/003.4-terraform/4.png)

**Chúng tôi đã hoàn thành việc tạo Cơ sở hạ tầng trên môi trường AWS Cloud bằng Terraform (Infrasture as code tool)**