Vorlage:

https://github.com/arun-gupta/kubernetes-java-sample/blob/master/app-mysql-pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysql-pod
  labels:
    name: mysql-pod
    context: docker-k8s-lab
spec:
  containers: 
    - 
      name: mysql
      image: mysql:latest
      env: 
        - 
          name: "MYSQL_USER"
          value: "mysql"
        - 
          name: "MYSQL_PASSWORD"
          value: "mysql"
        - 
          name: "MYSQL_DATABASE"
          value: "sample"
        - 
          name: "MYSQL_ROOT_PASSWORD"
          value: "supersecret"
      ports: 
        - 
          containerPort: 3306
```

```console
core@kube-00 ~ $ kubectl create -f app-mysql-pod.yaml 
pods/mysql-pod
core@kube-00 ~ $ kubectl get pods
NAME        READY     STATUS                                         RESTARTS   AGE
mysql-pod   0/1       Image: mysql:latest is not ready on the node   0          20s
core@kube-00 ~ $ kubectl get -w pods
NAME        READY     STATUS                                         RESTARTS   AGE
mysql-pod   0/1       Image: mysql:latest is not ready on the node   0          31s
^Ccore@kube-00 ~ $ kubectl get pods
NAME        READY     STATUS                                         RESTARTS   AGE
mysql-pod   0/1       Image: mysql:latest is not ready on the node   0          2m
core@kube-00 ~ $ kubectl get pods
NAME        READY     STATUS    RESTARTS   AGE
mysql-pod   1/1       Running   0          2m
core@kube-00 ~ $ 
```

```yaml
apiVersion: v1
kind: Service
metadata: 
  name: mysql-service
  labels: 
    name: mysql-pod
    context: docker-k8s-lab
spec: 
  ports:
    # the port that this service should serve on
    - port: 3306
  # label keys and values that must match in order to receive traffic for this service
  selector: 
    name: mysql-pod
    context: docker-k8s-lab
```

core@kube-00 ~ $ kubectl create -f app-mysql-service.yaml 
services/mysql-service
core@kube-00 ~ $ 

core@kube-00 ~ $ kubectl get services
NAME            LABELS                                    SELECTOR                                IP(S)          PORT(S)
kubernetes      component=apiserver,provider=kubernetes   <none>                                  10.16.0.1      443/TCP
mysql-service   context=docker-k8s-lab,name=mysql-pod     context=docker-k8s-lab,name=mysql-pod   10.21.94.189   3306/TCP
core@kube-00 ~ $ 

apiVersion: v1
kind: ReplicationController
metadata:
  name: wildfly-rc
  labels:
    name: wildfly
    context: docker-k8s-lab
spec:
  replicas: 1
  template:
    metadata:
      labels:
        name: wildfly
    spec:
      containers:
      - name: wildfly-rc-pod
        image: arungupta/wildfly-mysql-javaee7:k8s
        ports:
        - containerPort: 8080

core@kube-00 ~ $ kubectl create -f app-wildfly-rc.yaml
replicationcontrollers/wildfly-rc
core@kube-00 ~ $ kubectl get pods

core@kube-00 ~ $ kubectl get pods
NAME               READY     STATUS    RESTARTS   AGE
mysql-pod          1/1       Running   0          18m
wildfly-rc-ubvl0   1/1       Running   0          8m
core@kube-00 ~ $ 

core@kube-00 ~ $ kubectl get -o template po wildfly-rc-ubvl0 --template={{.status.podIP}}
10.44.0.3
core@kube-00 ~ $ 

core@kube-00 ~ $ curl http://10.44.0.3:8080
<!--
  ~ JBoss, Home of Professional Open Source.
  ~ Copyright (c) 2014, Red Hat, Inc., and individual contributors
  ~ as indicated by the @author tags. See the copyright.txt file in the
  ~ distribution for a full listing of individual contributors.
  ~
  ~ This is free software; you can redistribute it and/or modify it
  ~ under the terms of the GNU Lesser General Public License as
  ~ published by the Free Software Foundation; either version 2.1 of
  ~ the License, or (at your option) any later version.
  ~
  ~ This software is distributed in the hope that it will be useful,
  ~ but WITHOUT ANY WARRANTY; without even the implied warranty of
  ~ MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
  ~ Lesser General Public License for more details.
  ~
  ~ You should have received a copy of the GNU Lesser General Public
  ~ License along with this software; if not, write to the Free
  ~ Software Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA
  ~ 02110-1301 USA, or see the FSF site: http://www.fsf.org.
  -->
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">

<html>
<head>
    <title>Welcome to WildFly 9</title>
    <link rel="shortcut icon" href="favicon.ico" type="image/x-icon">
    <link rel="StyleSheet" href="wildfly.css" type="text/css">
</head>

<body>
<div class="wrapper">
    <div class="content">
        <div class="logo">
                <img src="wildfly_logo.png" alt="WildFly 9... it's here." border="0" />
        </div>
        <h1>Welcome to WildFly 9</h1>

        <h3>Your WildFly 9 is running.</h3>

        <p><a href="documentation.html">Documentation</a> | <a href="http://github.com/wildfly/quickstart">Quickstarts</a> | <a href="/console">Administration
            Console</a> </p>

        <p><a href="http://wildfly.org">WildFly Project</a> |
            <a href="https://community.jboss.org/en/wildfly">User Forum</a> |
            <a href="https://issues.jboss.org/browse/WFLY">Report an issue</a></p>
        <p class="logos"><a href="http://jboss.org"><img src="jbosscommunity_logo_hori_white.png" alt="JBoss and JBoss Community" width=
                "195" height="37" border="0"></a></p>

        <p class="note">To replace this page simply deploy your own war with / as its context path.<br />
            To disable it, remove "welcome-content" handler for location / in undertow subsystem</p>
    </div>
</div>
</body>
</html>
core@kube-00 ~ $ curl http://10.44.0.3:8080/employees/resources/employees/
<?xml version="1.0" encoding="UTF-8" standalone="yes"?><collection><employee><id>1</id><name>Penny</name></employee><employee><id>2</id><name>Sheldon</name></employee><employee><id>3</id><name>Amy</name></employee><employee><id>4</id><name>Leonard</name></employee><employee><id>5</id><name>Bernadette</name></employee><employee><id>6</id><name>Raj</name></employee><employee><id>7</id><name>Howard</name></employee><employee><id>8</id><name>Priya</name></employee></collection>core@kube-00 ~ $ 


