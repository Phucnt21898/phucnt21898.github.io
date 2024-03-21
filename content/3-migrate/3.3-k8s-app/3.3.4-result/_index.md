---
title : "Module result server"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 3.3.4 </b> "
---

**Create Deployment & Service type “ClusterIP” for result-server**

      apiVersion: apps/v1
      kind: Deployment
      metadata:
      name: result-app-deploy
      namespace: phonebook
      labels:
         name: result-app-deploy
      spec:
      replicas: 1
      selector:
         matchLabels:
            name: result-app-pod
      template:
         metadata:
            name: result-app-pod
            labels:
            name: result-app-pod
         spec:
            containers:
            - name: result-app
               resources:
               image: devenes/python_result_server:2
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
      name: result-service
      namespace: phonebook
      labels:
         name: result-service
      spec:
      type: ClusterIP
      ports:
         - port: 80
            targetPort: 80
      selector:
         name: result-app-pod


Create **HorizontalPodAutoscaler** to auto scale result-server Pod, It perform action CPU scaling to operate at 50% of the CPU required by the Pod. (Make sure Metric Server is installed)

      apiVersion: autoscaling/v1
      kind: HorizontalPodAutoscaler
      metadata:
      name: hpa-deploy-result-server
      namespace: phonebook
      spec:
      scaleTargetRef:
         apiVersion: apps/v1
         kind: Deployment
         name: result-app-deploy
      minReplicas: 1
      maxReplicas: 10
      targetCPUUtilizationPercentage: 50

