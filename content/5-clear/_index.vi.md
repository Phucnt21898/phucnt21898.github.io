---
title : "Dọn dẹp tài nguyên"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---


Vì tất cả tài nguyên, ứng dụng Phonebook, dữ liệu ứng dụng đều được triển khai trên môi trường AWS Cloud nên sau khi hoàn thành Workshop này, nếu muốn dọn sạch tài nguyên, chúng ta chỉ cần chạy lệnh: terraform destroy và nhập **yes**. Khi đó quá trình dọn dẹp tài nguyên sẽ diễn ra tự động:

![???](/images/005-clear/1.png)


**Quá trình sẽ tiếp tục cho đến khi tài nguyên do Terraform tạo ra được xóa hoàn toàn**
