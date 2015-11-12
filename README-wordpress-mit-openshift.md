# Wordpress mit OpenShift

## Erstellung eines entsprechenden Templates

```
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

