---
title : "Backup & restore dữ liệu ứng dụng từ môi trường on-premises lên AWS cloud"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

Tùy vào dữ liệu của ứng dụng lớn hay nhỏ và yêu cầu thời gian upload dữ liệu ngắn hay dài mà chúng ta sẽ lựa chọn các kịch bản sao lưu & khôi phục khác nhau.

Với ứng dụng này, chúng ta sẽ sử dụng mysqldump để sao lưu cơ sở dữ liệu App-phonebook trên môi trường On-premises, sau đó đẩy nó lên S3 bucket để server trên môi trường AWS Cloud có thể kéo về. Cuối cùng khôi phục trên cơ sở dữ liệu mysql đã triển khai trên AWS Cloud


### Content:

   - [Backup mysql database trên môi trường on-premises](./4.1-backup/)
   - [Push mysql backup file từ on-premises lên AWS cloud sử dụng awscli](./4.2-push/)
   - [Restore dữ liệu mysql trên môi trường AWS cloud](./4.3-restore/)
