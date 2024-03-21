---
title : "Module web server"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3.3 </b> "
---

**Tạo Configmap, secret**

      apiVersion: v1
      kind: ConfigMap
      metadata:
         name: servers-configmap
         namespace: phonebook
      data:
         MYSQL_DATABASE_HOST: mysql-service.phonebook.svc.cluster.local
         MYSQL_USER: devenes
         MYSQL_DATABASE: phonebook

      ---
      apiVersion: v1
      kind: Secret
      metadata:
         name: mysql-secret
         namespace: phonebook
      type: Opaque
      data:
         mysql-root-password: SjEyMzQ1ag==
         # value: J12345j
         mysql-admin-password: RGV2ZW5lc18x
         # value: Devenes_1
      ---
      apiVersion: v1
      kind: ConfigMap
      metadata:
         name: database-configmap
         namespace: phonebook
      data:
         MYSQL_USER: devenes
         MYSQL_DATABASE: phonebook

**Tạo Configmap cho server: Chỉ định database cái mà server connect tới**

      apiVersion: v1
      kind: ConfigMap
      metadata:
         name: servers-configmap
         namespace: phonebook
      data:
         MYSQL_DATABASE_HOST: mysql-service.phonebook.svc.cluster.local
         MYSQL_USER: devenes
         MYSQL_DATABASE: phonebook


**Tạo Deployment & Service type “ClusterIP” cho web-server**

      apiVersion: apps/v1
      kind: Deployment
      metadata:
      name: phonebook-app-deploy
      namespace: phonebook
      labels:
         app: phonebook-app-deploy
      spec:
      replicas: 1
      selector:
         matchLabels:
            name: phonebook-app-pod
      template:
         metadata:
            name: phonebook-app-pod
            labels:
            name: phonebook-app-pod
         spec:
            containers:
            - name: phonebook-app
               resources:
                  limits:
                  cpu: 200m
                  memory: 400Mi
                  requests:
                  cpu: 200m
                  memory: 400Mi
               image: devenes/python_web_server:2
               ports:
                  - containerPort: 80
               env:
                  - name: MYSQL_PASSWORD
                  valueFrom:
                     secretKeyRef:
                        name: mysql-secret
                        key: mysql-admin-password
               envFrom:
                  - configMapRef:
                     name: servers-configmap

      ---
      apiVersion: v1
      kind: Service
      metadata:
      name: phonebook-service
      namespace: phonebook
      labels:
         name: phonebook-service
      spec:
      type: ClusterIP
      ports:
         - port: 80
            targetPort: 80
      selector:
         name: phonebook-app-pod


Tạo **HorizontalPodAutoscaler** để auto scale web-server Pod, nó thực hiện action scale CPU khi mà CPU đạt 50%. (Đảm bảo Metric Server đã được cài đặt)

      apiVersion: autoscaling/v1
      kind: HorizontalPodAutoscaler
      metadata:
      name: hpa-deploy-web-server
      namespace: phonebook
      spec:
      scaleTargetRef:
         apiVersion: apps/v1
         kind: Deployment
         name: phonebook-app-deploy
      minReplicas: 1
      maxReplicas: 10
      targetCPUUtilizationPercentage: 50
