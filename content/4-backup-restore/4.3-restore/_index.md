---
title : "Restore data for mysql database on AWS cloud enviroment"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---


Similar to the On-premises environment, we pull the file from the S3 bucket and copy it to the /mnt/data folder on the Host. Then access MySQL Pod and restore with command:

      mysql -u root -p phonebook < database_ phonebook.sql 

After the restore process is complete, we check the data again in the Cloud environment:

![???](/images/004-backup-restore/5.png)

**We have seen that the data has been restored successfully.**

**So we have successfully migrated services deployed using Kubernetes from On-premises environment to Cloud environment.**
