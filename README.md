# WordPress Kubernetes Basic App (wp-k8)

This repository contains a basic Kubernetes setup for deploying a WordPress application with a MySQL database. The configuration includes two main files: `mysql.yaml` and `wordpress.yaml`, which define the MySQL deployment and service, and the WordPress deployment and service respectively.

## Prerequisites
Before deploying this WordPress application, make sure you have the following:

1. Kubernetes cluster up and running.
2. `kubectl` command-line tool installed and configured to communicate with your Kubernetes cluster.

## File 1 - mysql.yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.5
        ports:
        - containerPort: 3306
        imagePullPolicy: IfNotPresent
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: root
        - name: MYSQL_DATABASE
          value: wordpress
        - name: MYSQL_USER
          value: wordpress
        - name: MYSQL_PASSWORD
          value: root
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
```

## File 2 - wordpress.yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress
  labels:
    app: wp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wp
  template:
    metadata:
      labels:
        app: wp
    spec:
      containers:
      - name: wordpress
        image: wordpress
        ports:
        - containerPort: 80
        env:
        - name: WORDPRESS_DB_HOST
          value: mysql
        - name: WORDPRESS_DB_PASSWORD
          value: root
        - name: WORDPRESS_DB_USER
          value: wordpress
---
apiVersion: v1
kind: Service
metadata:
  name: wp-http
spec:
  type: NodePort
  selector:
    app: wp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

## Deploying the WordPress App

1. Make sure you are in the same directory as the YAML files.
2. Apply the MySQL deployment and service:

   ```
   kubectl apply -f mysql.yaml
   ```

3. Apply the WordPress deployment and service:

   ```
   kubectl apply -f wordpress.yaml
   ```

## Accessing the WordPress App

Once the resources are deployed successfully, you can access the WordPress application using the `minikube service` command. This command simplifies the process of accessing the application in a local development environment using Minikube.

To open the WordPress application in your default web browser, run the following command:

```
minikube service wp-http
```

This will automatically launch the WordPress application in your default web browser, and you can start using it immediately.

Please note that the `minikube service` command handles the port forwarding and IP resolution for you, making it easy to interact with your Kubernetes services directly from your local machine during development.

