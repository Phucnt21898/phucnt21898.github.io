---
title : "Module MySQL database"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 3.3.5 </b> "
---

**Tạo Static Volume: Tạo PersistentVolumeClaim bound vào PersistentVolume**

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

Tạo **StatefulSet** & **Service** type “ClusterIP” cho mysql database: Chỉ định static volume và configmap & secret được khởi tạo ở trên

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


Chúng ta đã chuẩn bị file manifest để define architecture của Phonebook App trên môi trường AWS Cloud. Để triển khai, chúng ta sẽ chạy câu lệnh sau: 

      kubectl apply --f app-phonebook/. 

   -  Trong đó app-phonebook là folder chứa file manifest.

Chúng ta đã triển khai thành công Phonebook App trên EKS Cluster:

![???](/images/003.5-k8s-app/4.png)

**Application interface:**

**_Add/Update/Delete phone book APIs:_**

![???](/images/003.5-k8s-app/5.png)

**_Result phone book APIs:_**

![???](/images/003.5-k8s-app/6.png)

**Tại thời điểm này, service của công ty đã được triển khai vào cụm EKS nhưng chưa có dữ liệu. Chúng ta cần sao lưu và khôi phục dữ liệu Ứng dụng từ môi trường On-premises lên cụm EKS**