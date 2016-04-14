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

## Test auf HP-NB mit OpenShift Installation

https://github.com/openshift/origin/tree/master/examples/wordpress

[vagrant@ose3-master ~]$ oc get pv
NAME      CAPACITY   ACCESSMODES   STATUS    CLAIM                 REASON    AGE
pv0001    1Gi        RWO,RWX       Bound     default/claim-wp                11m
pv0002    5Gi        RWO           Bound     default/claim-mysql             11m
[vagrant@ose3-master ~]$ oc get pvc
NAME          STATUS    VOLUME    CAPACITY   ACCESSMODES   AGE
claim-mysql   Bound     pv0002    5Gi        RWO           24m
claim-wp      Bound     pv0001    1Gi        RWO,RWX       25m
[vagrant@ose3-master ~]$ 


[vagrant@ose3-master ~]$ oc create -f origin/examples/wordpress/pod-mysql.yaml
pod "mysql" created
[vagrant@ose3-master ~]$ 

FEHLER: die Pods werden nicht erzeugt. Liegt vermutlich daran, dass die registry nicht geht ...

```
robert@c3p0:~/workspace/openshift-ansible$ vagrant ssh master
Last login: Thu Apr 14 11:05:23 2016 from 192.168.100.1
[vagrant@ose3-master ~]$ curl -k https://ose3-master.example.com:8443/healthz
ok[vagrant@ose3-master ~]$ curl -k https://ose3-node1.example.com:8443/healthz
curl: (7) Failed connect to ose3-node1.example.com:8443; Keine Route zum Zielrechner
[vagrant@ose3-master ~]$ curl -k https://ose3-node2.example.com:8443/healthz
curl: (7) Failed connect to ose3-node2.example.com:8443; Keine Route zum Zielrechner
[vagrant@ose3-master ~]$ Abgemeldet
Connection to 127.0.0.1 closed.
robert@c3p0:~/workspace/openshift-ansible$ vagrant ssh node1
Last login: Thu Apr 14 11:05:15 2016 from 192.168.100.1
[vagrant@ose3-node1 ~]$ curl -k https://ose3-node2.example.com:8443/healthz
curl: (7) Failed connect to ose3-node2.example.com:8443; Keine Route zum Zielrechner
[vagrant@ose3-node1 ~]$ curl -k https://ose3-master.example.com:8443/healthz
ok[vagrant@ose3-node1 ~]$ Abgemeldet
Connection to 127.0.0.1 closed.
robert@c3p0:~/workspace/openshift-ansible$ vagrant ssh master
Last login: Thu Apr 14 11:25:39 2016 from 10.0.2.2
[vagrant@ose3-master ~]$ curl -k https://ose3-node2.example.com:8443/healthz
curl: (7) Failed connect to ose3-node2.example.com:8443; Keine Route zum Zielrechner
[vagrant@ose3-master ~]$ 
```


http://developerblog.redhat.com/2015/11/19/dns-your-openshift-v3-cluster/

https://access.redhat.com/solutions/1520803

```
robert@c3p0:~/workspace/openshift-ansible$ vagrant up --no-provision
Bringing machine 'node1' up with 'virtualbox' provider...
Bringing machine 'node2' up with 'virtualbox' provider...
Bringing machine 'master' up with 'virtualbox' provider...
==> node1: Importing base box 'centos/7'...
==> node1: Matching MAC address for NAT networking...
==> node1: Checking if box 'centos/7' is up to date...
==> node1: Setting the name of the VM: openshift-ansible_node1_1460648293870_7084
==> node1: Clearing any previously set network interfaces...
==> node1: [landrush] Need to configure the host.
==> node1: [landrush] Momentarily using sudo to put the host config in place...
[sudo] password for robert: 
==> node1: [landrush] starting dns server
[landrush] Starting daemon...
[landrush] Waiting for daemon to start...
==> node1: Preparing network interfaces based on configuration...
    node1: Adapter 1: nat
    node1: Adapter 2: hostonly
==> node1: Forwarding ports...
    node1: 22 (guest) => 2222 (host) (adapter 1)
==> node1: Running 'pre-boot' VM customizations...
==> node1: Booting VM...
==> node1: Waiting for machine to boot. This may take a few minutes...
    node1: SSH address: 127.0.0.1:2222
    node1: SSH username: vagrant
    node1: SSH auth method: private key
==> node1: Machine booted and ready!
==> node1: Checking for guest additions in VM...
    node1: No guest additions were detected on the base box for this VM! Guest
    node1: additions are required for forwarded ports, shared folders, host only
    node1: networking, and more. If SSH fails on this machine, please install
    node1: the guest additions and repackage the box to continue.
    node1: 
    node1: This is not an error message; everything may continue to work properly,
    node1: in which case you may ignore this message.
==> node1: Setting hostname...
==> node1: Configuring and enabling network interfaces...
==> node1: [landrush] setting up machine's DNS to point to our server
==> node1: [landrush] network: :private_network, {:ip=>"192.168.100.200", :protocol=>"tcp", :id=>"f3884a01-f28e-42d8-b277-2408d851a154"}
==> node1: [landrush] network: :forwarded_port, {:guest=>22, :host=>2222, :host_ip=>"127.0.0.1", :id=>"ssh", :auto_correct=>true, :protocol=>"tcp"}
==> node1: [landrush] adding machine entry: ose3-node1.example.com => 192.168.100.200
==> node1: Rsyncing folder: /home/robert/workspace/openshift-ansible/ => /home/vagrant/sync
==> node1: Updating /etc/hosts file on active guest machines...
==> node1: Updating /etc/hosts file on host machine (password may be required)...
==> node1: Machine not provisioned because `--no-provision` is specified.
==> node2: Importing base box 'centos/7'...
==> node2: Matching MAC address for NAT networking...
==> node2: Checking if box 'centos/7' is up to date...
==> node2: Setting the name of the VM: openshift-ansible_node2_1460650635897_24914
==> node2: Fixed port collision for 22 => 2222. Now on port 2200.
==> node2: Clearing any previously set network interfaces...
==> node2: [landrush] Host DNS resolver config looks good.
==> node2: Preparing network interfaces based on configuration...
    node2: Adapter 1: nat
    node2: Adapter 2: hostonly
==> node2: Forwarding ports...
    node2: 22 (guest) => 2200 (host) (adapter 1)
==> node2: Running 'pre-boot' VM customizations...
==> node2: Booting VM...
==> node2: Waiting for machine to boot. This may take a few minutes...
    node2: SSH address: 127.0.0.1:2200
    node2: SSH username: vagrant
    node2: SSH auth method: private key
    node2: Warning: Remote connection disconnect. Retrying...
==> node2: Machine booted and ready!
==> node2: Checking for guest additions in VM...
    node2: No guest additions were detected on the base box for this VM! Guest
    node2: additions are required for forwarded ports, shared folders, host only
    node2: networking, and more. If SSH fails on this machine, please install
    node2: the guest additions and repackage the box to continue.
    node2: 
    node2: This is not an error message; everything may continue to work properly,
    node2: in which case you may ignore this message.
==> node2: Setting hostname...
==> node2: Configuring and enabling network interfaces...
==> node2: [landrush] setting up machine's DNS to point to our server
==> node2: [landrush] network: :private_network, {:ip=>"192.168.100.201", :protocol=>"tcp", :id=>"216f48b2-b773-430c-949c-9072935fecfd"}
==> node2: [landrush] network: :forwarded_port, {:guest=>22, :host=>2200, :host_ip=>"127.0.0.1", :id=>"ssh", :auto_correct=>true, :protocol=>"tcp"}
==> node2: [landrush] adding machine entry: ose3-node2.example.com => 192.168.100.201
==> node2: Rsyncing folder: /home/robert/workspace/openshift-ansible/ => /home/vagrant/sync
==> node2: Updating /etc/hosts file on active guest machines...
==> node2: Updating /etc/hosts file on host machine (password may be required)...
==> node2: Machine not provisioned because `--no-provision` is specified.
==> master: Importing base box 'centos/7'...
==> master: Matching MAC address for NAT networking...
==> master: Checking if box 'centos/7' is up to date...
==> master: Setting the name of the VM: openshift-ansible_master_1460650710877_48745
==> master: Fixed port collision for 22 => 2222. Now on port 2201.
==> master: Clearing any previously set network interfaces...
==> master: [landrush] Host DNS resolver config looks good.
==> master: Preparing network interfaces based on configuration...
    master: Adapter 1: nat
    master: Adapter 2: hostonly
==> master: Forwarding ports...
    master: 8443 (guest) => 8443 (host) (adapter 1)
    master: 22 (guest) => 2201 (host) (adapter 1)
==> master: Running 'pre-boot' VM customizations...
==> master: Booting VM...
==> master: Waiting for machine to boot. This may take a few minutes...
    master: SSH address: 127.0.0.1:2201
    master: SSH username: vagrant
    master: SSH auth method: private key
    master: Warning: Remote connection disconnect. Retrying...
==> master: Machine booted and ready!
==> master: Checking for guest additions in VM...
    master: No guest additions were detected on the base box for this VM! Guest
    master: additions are required for forwarded ports, shared folders, host only
    master: networking, and more. If SSH fails on this machine, please install
    master: the guest additions and repackage the box to continue.
    master: 
    master: This is not an error message; everything may continue to work properly,
    master: in which case you may ignore this message.
==> master: Setting hostname...
==> master: Configuring and enabling network interfaces...
==> master: [landrush] setting up machine's DNS to point to our server
==> master: [landrush] network: :private_network, {:ip=>"192.168.100.100", :protocol=>"tcp", :id=>"e270b1ec-b004-4cb9-82fe-ee8137a806fe"}
==> master: [landrush] network: :forwarded_port, {:guest=>8443, :host=>8443, :protocol=>"tcp", :id=>"tcp8443"}
==> master: [landrush] network: :forwarded_port, {:guest=>22, :host=>2201, :host_ip=>"127.0.0.1", :id=>"ssh", :auto_correct=>true, :protocol=>"tcp"}
==> master: [landrush] adding machine entry: ose3-master.example.com => 192.168.100.100
==> master: Rsyncing folder: /home/robert/workspace/openshift-ansible/ => /home/vagrant/sync
==> master: Updating /etc/hosts file on active guest machines...
==> master: Updating /etc/hosts file on host machine (password may be required)...
==> master: Machine not provisioned because `--no-provision` is specified.
```

```
[vagrant@ose3-master master]$ sudo oadm registry --config=admin.kubeconfig --credentials=openshift-registry.kubeconfig
Flag --credentials has been deprecated, use --service-account to specify the service account the registry will use to make API calls
deploymentconfig "docker-registry" created
service "docker-registry" created
[vagrant@ose3-master master]$ 
```
date gibt falsche Uhrzeit zurück --> vagrant ??

