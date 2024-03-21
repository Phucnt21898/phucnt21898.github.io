---
title : "Setup Instance Connect để truy cập vào Bastion host thông qua giao thức ssh"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.1.3 </b> "
---

Kết nối Amazon EC2 instance là một cách đơn giản và an toàn để kết nối với các instance bằng Secure Shell (SSH). Với EC2 Instance Connect, ta có thể kiểm soát quyền truy cập SSH vào instance của mình bằng policy AWS Identity and Access Management (IAM) cũng như kiểm tra các yêu cầu kết nối bằng event AWS CloudTrail.

Để AWS Instance Connect có thể truy cập Bastion Host, Bastion Host phải được đặt trên public subnet và security group phải mở Inbound cho dải IP: https://ip-ranges.amazonaws.com/ ip-ranges.json
Để kết nối với instance bằng browser-based client từ bảng điều khiển Amazon EC2:

**Step 1:** Chọn instance và chọn Connect.
![???](/images/003.3-instance-connect/1.png)

**Step 2:** Click vào Connect:
![???](/images/003.3-instance-connect/2.png)

Chúng ta đã truy cập thành công Bastion Host bằng cách sử dụng AWS EC2 Instance Connect:
![???](/images/003.3-instance-connect/3.png)

**_Từ đây chúng ta sẽ bắt đầu cài đặt các công cụ cần thiết cho bastion host_**