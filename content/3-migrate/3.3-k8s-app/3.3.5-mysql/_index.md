---
title : "Module MySQL database"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 3.3.5 </b> "
---

**Create Static Volume: Create PersistentVolumeClaim bound to PersistentVolume**

      apiVersion: v1
      kind: PersistentVolume
      metadata:
      name: mysql-pv-volume
      namespace: phonebook
      labels:
         type: local
      spec:
      storageClassName: manual
      capacity:
         storage: 2Gi
      accessModes:
         - ReadWriteOnce
      hostPath:
         path: "/mnt/data"
      ---
      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
      name: mysql-pv-claim
      namespace: phonebook
      spec:
      storageClassName: manual
      accessModes:
         - ReadWriteOnce
      resources:
         requests:
            storage: 2Gi

Create **StatefulSet** & **Service** type “ClusterIP” for mysql database: Specify static volume and configmap and secret just created above

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
      name: mysql-deploy
      namespace: phonebook
      labels:
         name: mysql-deploy
      spec:
      replicas: 1
      serviceName: mysql-service
      selector:
         matchLabels:
            name: mysql-pod
      template:
         metadata:
            name: mysql-pod
            labels:
            name: mysql-pod
         spec: 
            containers:
            - image: mysql:5.7
               resources:
               name: mysql
               imagePullPolicy: Always
               env:
                  - name: MYSQL_PASSWORD
                  valueFrom:
                     secretKeyRef:
                        name: mysql-secret
                        key: mysql-admin-password
                  - name: MYSQL_ROOT_PASSWORD
                  valueFrom:
                     secretKeyRef:
                        name: mysql-secret
                        key: mysql-root-password
               envFrom:
                  - configMapRef:
                     name: database-configmap
               ports:
                  - containerPort: 3306
               volumeMounts:
                  - name: mysql-persistent-storage
                  mountPath: /var/lib/mysql
            volumes:
            - name: mysql-persistent-storage
               persistentVolumeClaim:
                  claimName: mysql-pv-claim
      ---
      apiVersion: v1
      kind: Service
      metadata:
      name: mysql-service
      namespace: phonebook
      labels:
         name: mysql-service
      spec:
      ports:
         - port: 3306
            targetPort: 3306
      selector:
         name: mysql-pod


We have prepared the manifest files defining the architecture of the Phonebook App on the AWS Cloud environment. To deploy, we will run command: 

      kubectl apply --f app-phonebook/. 

   -  Where app-phonebook is the folder containing manifest files. 

We have successfully deployed Phonebook App on EKS Cluster:

![???](/images/003.5-k8s-app/4.png)

**Application interface:**

**_Add/Update/Delete phone book APIs:_**

![???](/images/003.5-k8s-app/5.png)

**_Result phone book APIs:_**

![???](/images/003.5-k8s-app/6.png)

**At this time, the company's services have been deployed to the EKS cluster, but no data yet. We need to backup and restore App data on On-premises environment to EKS cluster**