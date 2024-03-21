---
title : "Backup mysql database on on-premises enviroment"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

Run command để access vào MySQL Pod:  

      kubectcl exec –it <mysql_pod> bash –n phonebook

![???](/images/004-backup-restore/1.png)
 
Run command để backup: 

      mysqldump -u root -p phonebook > /var/lib/mysql/database_phonebook.sql

![???](/images/004-backup-restore/2.png)
 
Vì thư mục **_/var/lib/mysql_** trong pod mysql được mount với thư mục **_/mnt/data_** trên server nên chúng ta có thể thấy file **database_phonebook.sql** trong thư mục **_/mnt/data_** trên server.
 
![???](/images/004-backup-restore/3.png)