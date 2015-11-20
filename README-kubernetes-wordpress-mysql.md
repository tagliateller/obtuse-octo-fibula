https://github.com/kubernetes/kubernetes/tree/master/examples/mysql-wordpress-pd

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
kubectl create -f mysql.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
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

```console
kubectl create -f mysql-service.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: wordpress
  labels: 
    name: wordpress
spec: 
  containers: 
    - image: wordpress
      name: wordpress
      env:
        - name: WORDPRESS_DB_PASSWORD
          # change this - must match mysql.yaml password
          value: supersecret
      ports: 
        - containerPort: 80
          name: wordpress
```

```console
kubectl create -f wordpress.yaml
```

```console
core@kube-00 ~ $ kubectl get pods
NAME        READY     STATUS    RESTARTS   AGE
mysql-pod   1/1       Running   0          41m
wordpress   1/1       Running   2          22m
core@kube-00 ~ $
```

```console
core@kube-00 ~ $ kubectl logs wordpress
index.html
WordPress not found in /var/www/html - copying now...
WARNING: /var/www/html is not empty - press Ctrl+C now if this is an error!
+ ls -A
+ sleep 10
Complete! WordPress has been successfully copied to /var/www/html

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known

Warning: mysqli::mysqli(): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

Warning: mysqli::mysqli(): (HY000/2002): php_network_getaddresses: getaddrinfo failed: Name or service not known in - on line 10

MySQL Connection Error: (2002) php_network_getaddresses: getaddrinfo failed: Name or service not known
core@kube-00 ~ $
```