---
title : "Đặt vấn đề"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Một công ty XYZ đã triển khai Service của họ bằng nền tảng Kubernetes trên môi trường On-premsise. Tuy nhiên, sau một thời gian, Công ty nhận thấy nhiều vấn đề về việc vận hành, như phải tự quản lý cơ sở hạ tầng, cần đảm bảo tính sẵn sàng cao cho Control Plane, Etcd, v.v. nên mất rất nhiều thời gian và nguồn nhân lực.

Trong bối cảnh đó, team DevOps đã nhận được yêu cầu từ Leadership về sự kiện đặc biệt của công ty, khi được dự đoán số lượng request tới Service sẽ tăng gấp 3 lần. Team DevOps đã kiểm tra rằng tài nguyên hiện tại khó đáp ứng được yêu cầu tăng gấp 3 lần trong sự kiện đặc biệt của công ty. Do đó, để giải quyết vấn đề mở rộng nhanh chóng và tiết kiệm chi phí, team DevOps phải dự đoán và đưa ra phương án dự phòng cho nhu cầu mở rộng, bao gồm mua thêm bao nhiêu server, mở rộng cơ sở hạ tầng mạng và tăng dung lượng lưu trữ..v.v. Quá trình mở rộng quy mô này có thể tốn kém và mất thời gian, chưa kể khi kết thúc sự kiện sẽ có một lượng lớn tài nguyên nhàn rỗi không được sử dụng.

Do đó, team DevOps đã quyết định di chuyển service lên môi trường Cloud để nhận được những benefits sau:


- Tính mở rộng linh hoạt: Cloud cho phép ta mở rộng quy mô tài nguyên khi cần mà không cần phải đầu tư vào phần cứng và cơ sở hạ tầng. Ta có thể dễ dàng tăng hoặc giảm số lượng server để đáp ứng khối lượng công việc. Mở rộng linh hoạt giúp tiết kiệm chi phí và tối ưu hóa việc sử dụng tài nguyên.
- Đơn giản hóa quản lý cơ sở hạ tầng: CLoud loại bỏ khó khăn và công việc liên quan đến việc quản lý cơ sở hạ tầng Kubernetes. AWS quản lý và duy trì toàn bộ cơ sở hạ tầng, bao gồm cài đặt, định cấu hình, thay đổi quy mô và nâng cấp các thành phần Kubernetes. Điều này giúp giảm bớt gánh nặng quản lý và giúp ta tập trung vào phát triển ứng dụng.
- Tích hợp dịch vụ AWS: CLoud tích hợp tốt với các dịch vụ và công cụ AWS khác. ta có thể sử dụng các dịch vụ như Amazon EC2, Amazon RDS, Amazon S3, Amazon DynamoDB..v..v để bổ sung cho ứng dụng của mình. Điều này giúp thúc đẩy các dịch vụ quản lý cơ sở dữ liệu, lưu trữ và xử lý dữ liệu, giảm thiểu công việc tích hợp và tăng độ tin cậy của hệ thống.
- Chi phí linh hoạt: Di chuyển từ môi trường On-premsise sang Cloud giúp ta chuyển từ mô hình trả trước (CAPEX) sang mô hình trả theo mức sử dụng (OPEX). Thay vì phải đầu tư vào phần cứng và cơ sở hạ tầng, ta chỉ trả tiền để sử dụng dịch vụ theo mô hình pay-as-you-go. Điều này giúp ta tối ưu hóa chi phí và linh hoạt trong việc phát triển và vận hành ứng dụng.


{{% notice info %}}
Workshop này tập trung vào việc demo di chuyển một ứng dụng Micro-service Phonebook đơn giản được triển khai trên cụm Kubernetes trong môi trường On-premsise lên môi trường AWS Cloud, cũng như xây dựng kiến trúc cụm EKS bằng cách sử dụng tool Infrastrure as code là Terraform. Workshop không bao gồm CI/CD, monitoring, logging hay tích hợp các dịch vụ AWS khác như RDS, Cloudwatch, v.v.
{{% /notice %}}
