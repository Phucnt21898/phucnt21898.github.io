---
title : "Restore dữ liệu mysql trên môi trường AWS cloud"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---


Tương tự như môi trường on-premises, chúng tôi kéo tệp từ S3 bucket và sao chép tệp vào thư mục /mnt/data trên server. Sau đó truy cập MySQL Pod và khôi phục bằng lệnh:

      mysql -u root -p phonebook < database_ phonebook.sql 

Sau khi quá trình restore hoàn tất, chúng ta kiểm tra lại dữ liệu trên môi trường Cloud:

![???](/images/004-backup-restore/5.png)

**Chúng ta đã thấy rằng dữ liệu đã được restore thành công.**

**Vậy là chúng ta đã di chuyển thành công service được triển khai bằng Kubernetes từ môi trường on-premises lên môi trường Cloud.**