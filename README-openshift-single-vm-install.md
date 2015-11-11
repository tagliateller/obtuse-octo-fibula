# Installation einer Single VM mit OpenShift (Executable)

$ azure group create -n demov3group -l "East US"

single-linux-machine]$ azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g demov3group -n demov3deployment

ssh -i ~/azure-key-pair azureuser@demov3.eastus.cloudapp.azure.com

```
sudo yum -y update
sudo yum -y install docker
sudo yum -y install git
sudo systemctl start docker
```

Download

```
[azureuser@demov3 ~]$ curl -L -O https://github.com/openshift/origin/releases/download/v1.0.7/openshift-origin-v1.0.7-67bb208-linux-amd64.tar.gz
```

Entpacken

```
[azureuser@demov3 ~]$ mkdir origin-1.0.7
[azureuser@demov3 ~]$ cd origin-1.0.7/
$ tar zxvf ../openshift-origin-v1.0.7-67bb208-linux-amd64.tar.gz

./
./kube-proxy
./oadm
./kubectl
./kube-controller-manager
./kube-scheduler
./oc
./openshift
./kube-apiserver
./kubelet
```

Export in Path und Start OpenShift Origin

```
[azureuser@demov3 origin-1.0.7]$ export PATH=$(pwd):$PATH
[azureuser@demov3 origin-1.0.7]$ sudo ./openshift start
```

Einrichtung Registry

```
sudo ./oadm registry --config=openshift.local.config/master/admin.kubeconfig --credentials=openshift.local.config/master/openshift-registry.kubeconfig 
```

Laden der Examples

```
sudo yum -y install unzip
unzip examples.zip (git openshift-ansible)
```

geht nicht, da oauth auf 10.0.0.11 umgeleitet wird. auch die nachträgliche korrektur der url hilft nicht.
https://demov3.eastus.cloudapp.azure.com:8443/console/

[azureuser@demov3 ~]$ origin-1.0.7/oc login

[azureuser@demov3 ~]$ origin-1.0.7/oc new-project smoke-project

origin-1.0.7/oc new-app https://github.com/gshipley/smoke.git
--> geht, es ist aber keine registry vorhanden

## Anleitung nach https://docs.openshift.org/latest/getting_started/administrators.html#try-it-out

[azureuser@demov3 ~]$ origin-1.0.7/oc new-app openshift/deployment-example --> geht

--> aber Fehler in Deployment

ok, 2. Versuch: Deployment ok (wie Tutorial), weiter ...
#

[azureuser@demov3 origin]$ curl http://172.30.84.60:8080
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Deployment Demonstration</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    HTML{height:100%;}
    BODY{font-family:Helvetica,Arial;display:flex;display:-webkit-flex;align-items:center;justify-content:center;-webkit-align-items:center;-webkit-box-align:center;-webkit-justify-content:center;height:100%;}
    .box{background:#006e9c;color:white;text-align:center;border-radius:10px;display:inline-block;}
    H1{font-size:10em;line-height:1.5em;margin:0 0.5em;}
    H2{margin-top:0;}
  </style>
</head>
<body>
<div class="box"><h1>v1</h1><h2></h2></div>
</body>
</html>[azureuser@demov3 origin]$ 


 
--> es fehlen die Templates ... oder ?

```
## Doku verlorengegangen, also später nochmal lt. history

    1  sudo yum -y update
    2  sudo yum -y install git
    3  sudo yum -y install docker
    4  sudo vi /etc/sysconfig/docker
    5  sudo systemctl start docker
    6  sudo systemctl status docker
    7  sudo vi /etc/sysconfig/docker
    8  sudo systemctl status docker
    9  sudo systemctl start docker
   10  sudo systemctl status docker
   11  sudo systemctl stop docker
   12  sudo systemctl status docker
   13  sudo vi /etc/sysconfig/docker
   14  sudo systemctl start docker
   15  sudo systemctl status docker
   16  curl -L -O https://github.com/openshift/origin/releases/download/v1.0.7/openshift-origin-v1.0.7-67bb208-linux-amd64.tar.gz
   17  mkdir origin-1.0.7
   18  cd origin-1.0.7/
   19  tar zxvf ../openshift-origin-v1.0.7-67bb208-linux-amd64.tar.gz 
   20  export PATH=$(pwd):$PATH
   21  sudo openshift start &> openshift.log &
   22  sudo openshift start
   23  export PATH=$(pwd):$PATH
   24  sudo export PATH=$(pwd):$PATH
   25  sudo ./openshift start &> openshift.log &
   26  history
```

```
https://github.com/openshift/origin/tree/master/examples/sample-app

    1  cd origin-1.0.7/
    2  export PATH=$(pwd):$PATH
    3  export CURL_CA_BUNDLE=`pwd`/openshift.local.config/master/ca.crt
    4  sudo chmod a+rwX openshift.local.config/master/admin.kubeconfig
    5* 
    6  ls -la
    7  export CURL_CA_BUNDLE=`pwd`/openshift.local.config/master/ca.crt
    8  sudo chmod a+rwX openshift.local.config/master/admin.kubeconfig
    9  sudo chmod +r openshift.local.config/master/openshift-registry.kubeconfig
   10  oadm registry --create --credentials=openshift.local.config/master/openshift-registry.kubeconfig --config=openshift.local.config/master/admin.kubeconfig
   11  oc describe service docker-registry --config=openshift.local.config/master/admin.kubeconfig
   12  oc login --certificate-authority=openshift.local.config/master/ca.crt
   13  oc new-project test --display-name="OpenShift 3 Sample" --description="This is an example project to demonstrate OpenShift v3"
   14  vi application-template-stibuild.json
   15  oc new-app application-template-stibuild.json
   16  oc status
   17  oc get builds
   18  oc logs build/ruby-sample-build-1 -n test
   19  oc get pods
   20  oc get services
   21  curl http://172.30.196.225:5434
   22  curl http://172.30.140.127:5432
   23  history > log
   
   # /etc/sysconfig/docker
```

```
# Modify these options if you want to change the way the docker daemon runs
OPTIONS='--selinux-enabled'

DOCKER_CERT_PATH=/etc/docker

# If you want to add your own registry to be used for docker search and docker
# pull use the ADD_REGISTRY option to list a set of registries, each prepended
# with --add-registry flag. The first registry added will be the first registry
# searched.
# ADD_REGISTRY='--add-registry registry.access.redhat.com'

# If you want to block registries from being used, uncomment the BLOCK_REGISTRY
# option and give it a set of registries, each prepended with --block-registry
# flag. For example adding docker.io will stop users from downloading images
# from docker.io
# BLOCK_REGISTRY='--block-registry'

# If you have a registry secured with https but do not have proper certs
# distributed, you can tell docker to not look for full authorization by
# adding the registry to the INSECURE_REGISTRY line and uncommenting it.
INSECURE_REGISTRY='--insecure-registry 172.30.0.0/16'

# On an SELinux system, if you remove the --selinux-enabled option, you
# also need to turn on the docker_transition_unconfined boolean.
# setsebool -P docker_transition_unconfined 1

# Location used for temporary files, such as those created by
# docker load and build operations. Default is /var/lib/docker/tmp
# Can be overriden by setting the following environment variable.
# DOCKER_TMPDIR=/var/tmp

# Controls the /etc/cron.daily/docker-logrotate cron job status.
# To disable, uncomment the line below.
# LOGROTATE=false
```

GIT + Secret anpassen:

```
{
  "kind": "Template",
  "apiVersion": "v1",
  "metadata": {
    "name": "ruby-helloworld-sample",
    "creationTimestamp": null,
    "annotations": {
      "description": "This example shows how to create a simple ruby application in openshift origin v3",
      "iconClass": "icon-ruby",
      "tags": "instant-app,ruby,mysql"
    }
  },
  "objects": [
    {
      "kind": "Service",
      "apiVersion": "v1",
      "metadata": {
        "name": "frontend",
        "creationTimestamp": null
      },
      "spec": {
        "ports": [
          {
            "name": "web",
            "protocol": "TCP",
            "port": 5432,
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
      "kind": "Route",
      "apiVersion": "v1",
      "metadata": {
        "name": "route-edge",
        "creationTimestamp": null
      },
      "spec": {
        "host": "www.example.com",
        "to": {
          "kind": "Service",
          "name": "frontend"
        },
        "tls": {
          "termination": "edge",
          "certificate": "-----BEGIN CERTIFICATE-----\nMIIDIjCCAgqgAwIBAgIBATANBgkqhkiG9w0BAQUFADCBoTELMAkGA1UEBhMCVVMx\nCzAJBgNVBAgMAlNDMRUwEwYDVQQHDAxEZWZhdWx0IENpdHkxHDAaBgNVBAoME0Rl\nZmF1bHQgQ29tcGFueSBMdGQxEDAOBgNVBAsMB1Rlc3QgQ0ExGjAYBgNVBAMMEXd3\ndy5leGFtcGxlY2EuY29tMSIwIAYJKoZIhvcNAQkBFhNleGFtcGxlQGV4YW1wbGUu\nY29tMB4XDTE1MDExMjE0MTk0MVoXDTE2MDExMjE0MTk0MVowfDEYMBYGA1UEAwwP\nd3d3LmV4YW1wbGUuY29tMQswCQYDVQQIDAJTQzELMAkGA1UEBhMCVVMxIjAgBgkq\nhkiG9w0BCQEWE2V4YW1wbGVAZXhhbXBsZS5jb20xEDAOBgNVBAoMB0V4YW1wbGUx\nEDAOBgNVBAsMB0V4YW1wbGUwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMrv\ngu6ZTTefNN7jjiZbS/xvQjyXjYMN7oVXv76jbX8gjMOmg9m0xoVZZFAE4XyQDuCm\n47VRx5Qrf/YLXmB2VtCFvB0AhXr5zSeWzPwaAPrjA4ebG+LUo24ziS8KqNxrFs1M\nmNrQUgZyQC6XIe1JHXc9t+JlL5UZyZQC1IfaJulDAgMBAAGjDTALMAkGA1UdEwQC\nMAAwDQYJKoZIhvcNAQEFBQADggEBAFCi7ZlkMnESvzlZCvv82Pq6S46AAOTPXdFd\nTMvrh12E1sdVALF1P1oYFJzG1EiZ5ezOx88fEDTW+Lxb9anw5/KJzwtWcfsupf1m\nV7J0D3qKzw5C1wjzYHh9/Pz7B1D0KthQRATQCfNf8s6bbFLaw/dmiIUhHLtIH5Qc\nyfrejTZbOSP77z8NOWir+BWWgIDDB2//3AkDIQvT20vmkZRhkqSdT7et4NmXOX/j\njhPti4b2Fie0LeuvgaOdKjCpQQNrYthZHXeVlOLRhMTSk3qUczenkKTOhvP7IS9q\n+Dzv5hqgSfvMG392KWh5f8xXfJNs4W5KLbZyl901MeReiLrPH3w=\n-----END CERTIFICATE-----",
          "key": "-----BEGIN PRIVATE KEY-----\nMIICeAIBADANBgkqhkiG9w0BAQEFAASCAmIwggJeAgEAAoGBAMrvgu6ZTTefNN7j\njiZbS/xvQjyXjYMN7oVXv76jbX8gjMOmg9m0xoVZZFAE4XyQDuCm47VRx5Qrf/YL\nXmB2VtCFvB0AhXr5zSeWzPwaAPrjA4ebG+LUo24ziS8KqNxrFs1MmNrQUgZyQC6X\nIe1JHXc9t+JlL5UZyZQC1IfaJulDAgMBAAECgYEAnxOjEj/vrLNLMZE1Q9H7PZVF\nWdP/JQVNvQ7tCpZ3ZdjxHwkvf//aQnuxS5yX2Rnf37BS/TZu+TIkK4373CfHomSx\nUTAn2FsLmOJljupgGcoeLx5K5nu7B7rY5L1NHvdpxZ4YjeISrRtEPvRakllENU5y\ngJE8c2eQOx08ZSRE4TkCQQD7dws2/FldqwdjJucYijsJVuUdoTqxP8gWL6bB251q\nelP2/a6W2elqOcWId28560jG9ZS3cuKvnmu/4LG88vZFAkEAzphrH3673oTsHN+d\nuBd5uyrlnGjWjuiMKv2TPITZcWBjB8nJDSvLneHF59MYwejNNEof2tRjgFSdImFH\nmi995wJBAMtPjW6wiqRz0i41VuT9ZgwACJBzOdvzQJfHgSD9qgFb1CU/J/hpSRIM\nkYvrXK9MbvQFvG6x4VuyT1W8mpe1LK0CQAo8VPpffhFdRpF7psXLK/XQ/0VLkG3O\nKburipLyBg/u9ZkaL0Ley5zL5dFBjTV2Qkx367Ic2b0u9AYTCcgi2DsCQQD3zZ7B\nv7BOm7MkylKokY2MduFFXU0Bxg6pfZ7q3rvg8gqhUFbaMStPRYg6myiDiW/JfLhF\nTcFT4touIo7oriFJ\n-----END PRIVATE KEY-----",
          "caCertificate": "-----BEGIN CERTIFICATE-----\nMIIEFzCCAv+gAwIBAgIJALK1iUpF2VQLMA0GCSqGSIb3DQEBBQUAMIGhMQswCQYD\nVQQGEwJVUzELMAkGA1UECAwCU0MxFTATBgNVBAcMDERlZmF1bHQgQ2l0eTEcMBoG\nA1UECgwTRGVmYXVsdCBDb21wYW55IEx0ZDEQMA4GA1UECwwHVGVzdCBDQTEaMBgG\nA1UEAwwRd3d3LmV4YW1wbGVjYS5jb20xIjAgBgkqhkiG9w0BCQEWE2V4YW1wbGVA\nZXhhbXBsZS5jb20wHhcNMTUwMTEyMTQxNTAxWhcNMjUwMTA5MTQxNTAxWjCBoTEL\nMAkGA1UEBhMCVVMxCzAJBgNVBAgMAlNDMRUwEwYDVQQHDAxEZWZhdWx0IENpdHkx\nHDAaBgNVBAoME0RlZmF1bHQgQ29tcGFueSBMdGQxEDAOBgNVBAsMB1Rlc3QgQ0Ex\nGjAYBgNVBAMMEXd3dy5leGFtcGxlY2EuY29tMSIwIAYJKoZIhvcNAQkBFhNleGFt\ncGxlQGV4YW1wbGUuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA\nw2rK1J2NMtQj0KDug7g7HRKl5jbf0QMkMKyTU1fBtZ0cCzvsF4CqV11LK4BSVWaK\nrzkaXe99IVJnH8KdOlDl5Dh/+cJ3xdkClSyeUT4zgb6CCBqg78ePp+nN11JKuJlV\nIG1qdJpB1J5O/kCLsGcTf7RS74MtqMFo96446Zvt7YaBhWPz6gDaO/TUzfrNcGLA\nEfHVXkvVWqb3gqXUztZyVex/gtP9FXQ7gxTvJml7UkmT0VAFjtZnCqmFxpLZFZ15\n+qP9O7Q2MpsGUO/4vDAuYrKBeg1ZdPSi8gwqUP2qWsGd9MIWRv3thI2903BczDc7\nr8WaIbm37vYZAS9G56E4+wIDAQABo1AwTjAdBgNVHQ4EFgQUugLrSJshOBk5TSsU\nANs4+SmJUGwwHwYDVR0jBBgwFoAUugLrSJshOBk5TSsUANs4+SmJUGwwDAYDVR0T\nBAUwAwEB/zANBgkqhkiG9w0BAQUFAAOCAQEAaMJ33zAMV4korHo5aPfayV3uHoYZ\n1ChzP3eSsF+FjoscpoNSKs91ZXZF6LquzoNezbfiihK4PYqgwVD2+O0/Ty7UjN4S\nqzFKVR4OS/6lCJ8YncxoFpTntbvjgojf1DEataKFUN196PAANc3yz8cWHF4uvjPv\nWkgFqbIjb+7D1YgglNyovXkRDlRZl0LD1OQ0ZWhd4Ge1qx8mmmanoBeYZ9+DgpFC\nj9tQAbS867yeOryNe7sEOIpXAAqK/DTu0hB6+ySsDfMo4piXCc2aA/eI2DCuw08e\nw17Dz9WnupZjVdwTKzDhFgJZMLDqn37HQnT6EemLFqbcR0VPEnfyhDtZIQ==\n-----END CERTIFICATE-----"
        }
      },
      "status": {}
    },
    {
      "kind": "ImageStream",
      "apiVersion": "v1",
      "metadata": {
        "name": "origin-ruby-sample",
        "creationTimestamp": null
      },
      "spec": {},
      "status": {
        "dockerImageRepository": ""
      }
    },
    {
      "kind": "ImageStream",
      "apiVersion": "v1",
      "metadata": {
        "name": "ruby-20-centos7",
        "creationTimestamp": null
      },
      "spec": {
        "dockerImageRepository": "openshift/ruby-20-centos7"
      },
      "status": {
        "dockerImageRepository": ""
      }
    },
    {
      "kind": "BuildConfig",
      "apiVersion": "v1",
      "metadata": {
        "name": "ruby-sample-build",
        "creationTimestamp": null,
        "labels": {
          "name": "ruby-sample-build"
        }
      },
      "spec": {
        "triggers": [
          {
            "type": "GitHub",
            "github": {
              "secret": "secret101"
            }
          },
          {
            "type": "Generic",
            "generic": {
              "secret": "secret101"
            }
          },
          {
            "type": "ImageChange",
            "imageChange": {}
          },
          {
            "type": "ConfigChange"
          }
        ],
        "source": {
          "type": "Git",
          "git": {
            "uri": "https://github.com/openshift/ruby-hello-world.git"
          }
        },
        "strategy": {
          "type": "Source",
          "sourceStrategy": {
            "from": {
              "kind": "ImageStreamTag",
              "name": "ruby-20-centos7:latest"
            }
          },
          "env": [
            {
              "name": "EXAMPLE",
              "value": "sample-app"
            }
          ]
        },
        "output": {
          "to": {
            "kind": "ImageStreamTag",
            "name": "origin-ruby-sample:latest"
          }
        },
        "resources": {}
      },
      "status": {
        "lastVersion": 0
      }
    },
    {
      "kind": "DeploymentConfig",
      "apiVersion": "v1",
      "metadata": {
        "name": "frontend",
        "creationTimestamp": null
      },
      "spec": {
        "strategy": {
          "type": "Rolling",
          "rollingParams": {
            "updatePeriodSeconds": 1,
            "intervalSeconds": 1,
            "timeoutSeconds": 120,
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
                "containerName": "ruby-helloworld"
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
                "containerName": "ruby-helloworld"
              }
            }
          },
          "resources": {}
        },
        "triggers": [
          {
            "type": "ImageChange",
            "imageChangeParams": {
              "automatic": true,
              "containerNames": [
                "ruby-helloworld"
              ],
              "from": {
                "kind": "ImageStreamTag",
                "name": "origin-ruby-sample:latest"
              }
            }
          },
          {
            "type": "ConfigChange"
          }
        ],
        "replicas": 2,
        "selector": {
          "name": "frontend"
        },
        "template": {
          "metadata": {
            "creationTimestamp": null,
            "labels": {
              "name": "frontend"
            }
          },
          "spec": {
            "containers": [
              {
                "name": "ruby-helloworld",
                "image": "origin-ruby-sample",
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
                "containerName": "ruby-helloworld-database"
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
                "containerName": "ruby-helloworld-database"
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
                "name": "ruby-helloworld-database",
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

# Startup

```
sudo systemctl start docker
cd origin-1.0.7/
sudo ./openshift start &> openshift.log &
curl http://172.30.140.127:5432 --> liefert Seite zurück
```

# Route
Firewall auf 80, 8443 und 5432 aufmachen - ...

8443/console - geht nicht, da oauth auf interne IP verzweigt. auch manuelles austauschen der url wirkt nicht und führt zu error auf der console

80 - kein erfolg
5432 - kein erfolg

# Router erzeugen

```
export KUBECONFIG=<homedir>/openshift.local.config/master/admin.kubeconfig # erspart die Angabe --config
oc login -u system:admin -n default
echo '{"kind":"ServiceAccount","apiVersion":"v1","metadata":{"name":"router"}}' | oc create -f -
oc edit scc privileged

...
users:
- system:serviceaccount:openshift-infra:build-controller
- system:serviceaccount:default:router

oadm router --credentials=openshift.local.config/master/openshift-router.kubeconfig --service-account=router

```

# Service bereitstellen (in der EC2-Instanz muss Port 80 freigeschaltet sein)

```
oc expose service frontend --hostname=EC2INSTANCE
```

Damit ist die Applikation worldwide erreichbar (Port 80).

# wordpress + co


