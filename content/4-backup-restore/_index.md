---
title : "Backup & restore application data from on-premises enviroment to AWS cloud enviroment"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

Depending on whether the application's data is large or small and requires a short or long time to upload data, we will choose different backup & restore scenarios.

With this application, we will use mysqldump to backup the phonebook database on the On-premises environment, then push it to the S3 bucket so that the Server on the AWS Cloud environment can pull it back. Finally, restore on the mysql database deployed on AWS Cloud


### Content:

   - [Backup mysql database on On-premsise enviroment](./4.1-backup/)
   - [Push mysql backup file from On-premsise enviroment to AWS cloud enviroment using awscli](./4.2-push/)
   - [Restore data for mysql database on AWS cloud enviroment](./4.3-restore/)
