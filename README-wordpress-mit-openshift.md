# Wordpress mit OpenShift

## Erstellung eines entsprechenden Templates

```json
{
  "kind": "Template",
  "apiVersion": "v1",
  "metadata": {
    "name": "wordpress-helloworld-sample",
    "creationTimestamp": null,
    "annotations": {
      "description": "Dieses Template stellt eine Wordpress-Instanz einschl. MySQL-DB bereit",
      "tags": "wordpress,mysql"
    }
  },
  "objects": [
    {
      "kind": "Service",
      "apiVersion": "v1",
      "metadata": {
        "name": "wordpress",
        "creationTimestamp": null
      },
      "spec": {
        "ports": [
          {
            "name": "web",
            "protocol": "TCP",
            "port": 8080,
            "targetPort": 8080,
            "nodePort": 0
          }
        ],
        "selector": {
          "name": "frontend"
        },
        "portalIP": "",
        "type": "ClusterIP",
        "sessionAffinity": "None"
      },
      "status": {
        "loadBalancer": {}
      }
    },
    {
      "kind": "ImageStream",
      "apiVersion": "v1",
      "metadata": {
        "name": "wordpress",
        "creationTimestamp": null
      },
      "spec": {},
      "status": {
        "dockerImageRepository": ""
      }
    },
    {
      "kind": "DeploymentConfig",
      "apiVersion": "v1",
      "metadata": {
        "name": "wordpress",
        "creationTimestamp": null
      },
      "spec": {
        "template": {
          "metadata": {
            "creationTimestamp": null,
            "labels": {
              "name": "wordpress"
            }
          },
          "spec": {
            "containers": [
              {
                "name": "wordpress",
                "image": "wordpress",
                "ports": [
                  {
                    "containerPort": 8080,
                    "protocol": "TCP"
                  }
                ],
                "env": [
                  {
                    "name": "ADMIN_USERNAME",
                    "value": "${ADMIN_USERNAME}"
                  },
                  {
                    "name": "ADMIN_PASSWORD",
                    "value": "${ADMIN_PASSWORD}"
                  },
                  {
                    "name": "MYSQL_USER",
                    "value": "${MYSQL_USER}"
                  },
                  {
                    "name": "MYSQL_PASSWORD",
                    "value": "${MYSQL_PASSWORD}"
                  },
                  {
                    "name": "MYSQL_DATABASE",
                    "value": "${MYSQL_DATABASE}"
                  }
                ],
                "resources": {},
                "terminationMessagePath": "/dev/termination-log",
                "imagePullPolicy": "IfNotPresent",
                "securityContext": {
                  "capabilities": {},
                  "privileged": false
                }
              }
            ],
            "restartPolicy": "Always",
            "dnsPolicy": "ClusterFirst"
          }
        }
      },
      "status": {}
    },
    {
      "kind": "Service",
      "apiVersion": "v1",
      "metadata": {
        "name": "database",
        "creationTimestamp": null
      },
      "spec": {
        "ports": [
          {
            "name": "db",
            "protocol": "TCP",
            "port": 5434,
            "targetPort": 3306,
            "nodePort": 0
          }
        ],
        "selector": {
          "name": "database"
        },
        "portalIP": "",
        "type": "ClusterIP",
        "sessionAffinity": "None"
      },
      "status": {
        "loadBalancer": {}
      }
    },
    {
      "kind": "DeploymentConfig",
      "apiVersion": "v1",
      "metadata": {
        "name": "database",
        "creationTimestamp": null
      },
      "spec": {
        "strategy": {
          "type": "Recreate",
          "recreateParams": {
            "pre": {
              "failurePolicy": "Abort",
              "execNewPod": {
                "command": [
                  "/bin/true"
                ],
                "env": [
                  {
                    "name": "CUSTOM_VAR1",
                    "value": "custom_value1"
                  }
                ],
                "containerName": "wordpress-database"
              }
            },
            "post": {
              "failurePolicy": "Ignore",
              "execNewPod": {
                "command": [
                  "/bin/false"
                ],
                "env": [
                  {
                    "name": "CUSTOM_VAR2",
                    "value": "custom_value2"
                  }
                ],
                "containerName": "wordpress-database"
              }
            }
          },
          "resources": {}
        },
        "triggers": [
          {
            "type": "ConfigChange"
          }
        ],
        "replicas": 1,
        "selector": {
          "name": "database"
        },
        "template": {
          "metadata": {
            "creationTimestamp": null,
            "labels": {
              "name": "database"
            }
          },
          "spec": {
            "containers": [
              {
                "name": "wordpress-database",
                "image": "openshift/mysql-55-centos7:latest",
                "ports": [
                  {
                    "containerPort": 3306,
                    "protocol": "TCP"
                  }
                ],
                "env": [
                  {
                    "name": "MYSQL_USER",
                    "value": "${MYSQL_USER}"
                  },
                  {
                    "name": "MYSQL_PASSWORD",
                    "value": "${MYSQL_PASSWORD}"
                  },
                  {
                    "name": "MYSQL_DATABASE",
                    "value": "${MYSQL_DATABASE}"
                  }
                ],
                "resources": {},
                "terminationMessagePath": "/dev/termination-log",
                "imagePullPolicy": "Always",
                "securityContext": {
                  "capabilities": {},
                  "privileged": false
                }
              }
            ],
            "restartPolicy": "Always",
            "dnsPolicy": "ClusterFirst"
          }
        }
      },
      "status": {}
    }
  ],
  "parameters": [
    {
      "name": "ADMIN_USERNAME",
      "description": "administrator username",
      "generate": "expression",
      "from": "admin[A-Z0-9]{3}"
    },
    {
      "name": "ADMIN_PASSWORD",
      "description": "administrator password",
      "generate": "expression",
      "from": "[a-zA-Z0-9]{8}"
    },
    {
      "name": "MYSQL_USER",
      "description": "database username",
      "generate": "expression",
      "from": "user[A-Z0-9]{3}",
      "required": true
    },
    {
      "name": "MYSQL_PASSWORD",
      "description": "database password",
      "generate": "expression",
      "from": "[a-zA-Z0-9]{8}",
      "required": true
    },
    {
      "name": "MYSQL_DATABASE",
      "description": "database name",
      "value": "root",
      "required": true
    }
  ],
  "labels": {
    "template": "application-template-stibuild"
  }
}
```

## Deployment des Templates in der OpenShift-Instanz

```
oc new-app openshift/mysql-55-centos7 -e MYSQL_DATABASE=wordpress -e MYSQL_ROOT_PASSWORD=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress
```

```
oc new-app wordpress -e WORDPRESS_DB_PASSWORD=wordpress -e WORDPRESS_DB_USER=wordpress -e WORDPRESS_DB_NAME=wordpress -e WORDPRESS_DB_HOST=172.30.75.19:3306
```
ERROR: Wordpress startet nicht, wie kommt man an die Logs heran ??

# Versuch lt. Roadshow - JBoss-Image nicht in origin zu beziehen, mit Wildfly geht es nicht

```
oc new-app openshift/wildfly-8-centos~https://github.com/tagliateller/openshift3mlbparks.git
```

# Versuch mit "reinem" Kubernetes

```console
[azureuser@demov3 origin-1.0.7]$ kubectl run wpmysql --image=mysql --env="MYSQL_ROOT_PASSWORD=wordpress" --port=3306
deploymentconfig "wpmysql" created
[azureuser@demov3 origin-1.0.7]$ kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
docker-registry-1-y17gc   1/1       Running   0          3h
router-1-ay7rf            1/1       Running   0          3h
wpmysql-1-deploy          1/1       Running   0          11s
wpmysql-1-jjn5g           0/1       Pending   0          6s
[azureuser@demov3 origin-1.0.7]$ 

[azureuser@demov3 origin-1.0.7]$ kubectl get pods
NAME                      READY     STATUS             RESTARTS   AGE
docker-registry-1-y17gc   1/1       Running            0          3h
router-1-ay7rf            1/1       Running            0          3h
wpmysql-1-jjn5g           0/1       CrashLoopBackOff   6          6m
[azureuser@demov3 origin-1.0.7]$ 
```

# Das gleiche noch einmal mit AWS

Vorlage: https://github.com/kubernetes/kubernetes/tree/master/examples/mysql-wordpress-pd

```console
source ~/.aws_creds
[ec2-user@ip-172-31-11-251 ~]$ aws ec2 create-volume --availability-zone=us-east-1a --size 10 --volume-type gp2 --region=us-east-1
{
    "AvailabilityZone": "us-east-1a", 
    "Encrypted": false, 
    "VolumeType": "gp2", 
    "VolumeId": "vol-d1ae7732", 
    "State": "creating", 
    "Iops": 30, 
    "SnapshotId": "", 
    "CreateTime": "2015-11-17T18:32:16.823Z", 
    "Size": 10
}
[ec2-user@ip-172-31-11-251 ~]$ aws ec2 create-volume --availability-zone=us-east-1a --size 10 --volume-type gp2 --region=us-east-1
{
    "AvailabilityZone": "us-east-1a", 
    "Encrypted": false, 
    "VolumeType": "gp2", 
    "VolumeId": "vol-89ae776a", 
    "State": "creating", 
    "Iops": 30, 
    "SnapshotId": "", 
    "CreateTime": "2015-11-17T18:32:24.641Z", 
    "Size": 10
}
[ec2-user@ip-172-31-11-251 ~]$

[centos@ip-172-31-5-243 origin-1.0.7]$ vi mysql.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysql
  labels:
    name: mysql
spec:
  containers:
    - resources:
        limits :
          cpu: 0.5
      image: mysql:5.6
      name: mysql
      env:
        - name: MYSQL_ROOT_PASSWORD
          # change this
          value: wordpress
      ports:
        - containerPort: 3306
          name: mysql
      volumeMounts:
          # name must match the volume name below
        - name: mysql-persistent-storage
          # mount path within the container
          mountPath: /var/lib/mysql
  volumes:
    - name: mysql-persistent-storage
      awsElasticBlockStore:
        volumeID: aws://us-east-1a/vol-d1ae7732
        fsType: ext4
~                                                                                                                                                             
~                      
```

```console
[centos@ip-172-31-5-243 origin-1.0.7]$ kubectl create -f mysql.yaml
pod "mysql" created
[centos@ip-172-31-5-243 origin-1.0.7]$ kubectl get pods
NAME                      READY     STATUS              RESTARTS   AGE
docker-registry-1-kfvj1   1/1       Running             0          4m
mysql                     0/1       ContainerCreating   0          17s
router-1-5vzsb            1/1       Running             0          4m
[centos@ip-172-31-5-243 origin-1.0.7]$ 

nach 5 minuten

[centos@ip-172-31-5-243 origin-1.0.7]$ kubectl get pods
NAME                      READY     STATUS              RESTARTS   AGE
docker-registry-1-kfvj1   1/1       Running             0          10m
mysql                     0/1       ContainerCreating   0          5m
router-1-5vzsb            1/1       Running             0          9m
[centos@ip-172-31-5-243 origin-1.0.7]$ 

```

auch das wird so nix ... muss das volume gemountet sein ? geht aus https://github.com/kubernetes/kubernetes/blob/master/docs/user-guide/volumes.md#aws-ebs-example-configuration nicht wirklich hervor.

Nächster möglicher Versuch - Mysql ohne persistent storage, aber wie ?

http://blog.arungupta.me/

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
[centos@ip-172-31-19-180 origin-1.0.7]$ kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
docker-registry-1-kpdbl   1/1       Running   0          5m
mysql-pod                 1/1       Running   1          1m
router-1-4jkqe            1/1       Running   0          4m
[centos@ip-172-31-19-180 origin-1.0.7]$
```

```console
[centos@ip-172-31-19-180 origin-1.0.7]$ kubectl logs mysql-pod
Error from server: Internal error occurred: Pod "mysql-pod" in namespace "default" : pod is not in 'Running', 'Succeeded' or 'Failed' state - State: "Pending"
[centos@ip-172-31-19-180 origin-1.0.7]$ kubectl get pods
NAME                      READY     STATUS             RESTARTS   AGE
docker-registry-1-kpdbl   1/1       Running            0          8m
mysql-pod                 0/1       CrashLoopBackOff   5          3m
router-1-4jkqe            1/1       Running            0          7m
[centos@ip-172-31-19-180 origin-1.0.7]$
```

Vielleicht mit Wildfly + MySQL

http://blog.arungupta.me/mysql-kubernetes-service-access-wildfly-pod-techtip72/

## Aus der OpenShift Dokumentation

https://github.com/openshift/origin/tree/master/examples/wordpress

