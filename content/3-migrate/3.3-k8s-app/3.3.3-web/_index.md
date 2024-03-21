---
title : "Module web server"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3.3 </b> "
---

**Create Configmap, secret**

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

**Create Configmap for server: Specify the database to which the server connects**

      apiVersion: v1
      kind: ConfigMap
      metadata:
         name: servers-configmap
         namespace: phonebook
      data:
         MYSQL_DATABASE_HOST: mysql-service.phonebook.svc.cluster.local
         MYSQL_USER: devenes
         MYSQL_DATABASE: phonebook


**Create Deployment & Service type “ClusterIP” for web-server**

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


Create **HorizontalPodAutoscaler** to auto scale web-server Pod, It perform action CPU scaling to operate at 50% of the CPU required by the Pod. (Make sure Metric Server is installed)

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
