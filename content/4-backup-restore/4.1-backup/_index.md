---
title : "Backup mysql database on on-premises enviroment"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

Run command to access to MySQL Pod:  

      kubectcl exec –it <mysql_pod> bash –n phonebook

![???](/images/004-backup-restore/1.png)
 
Run command to backup: 

      mysqldump -u root -p phonebook > /var/lib/mysql/database_phonebook.sql

![???](/images/004-backup-restore/2.png)
 
Because the **_/var/lib/mysql_** folder in the mysql container is mounted with the **_/mnt/data_** folder on the host, so we can see the **database_phonebook.sql** file in the **_/mnt/data_** folder on the host.
 
![???](/images/004-backup-restore/3.png)