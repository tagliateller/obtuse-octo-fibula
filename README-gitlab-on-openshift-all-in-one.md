* Gitlab on OpenShift

http://akrambenaissi.com/2016/02/23/deploy-gitlab-on-openshift/

https://blog.openshift.com/deploy-gitlab-openshift/

* Erzeugen Projekt

oc login 

robert / robert

oc new-project gitlab

oc new-app --template=postgresql-persistent -p POSTGRESQL_USER=admin,POSTGRESQL_PASSWORD=redhat,POSTGRESQL_DATABASE=gitlab

robert@c3p0:~$ oc new-app --template=postgresql-persistent -p POSTGRESQL_USER=admin,POSTGRESQL_PASSWORD=redhat,POSTGRESQL_DATABASE=gitlab
--> Deploying template postgresql-persistent in project openshift for "postgresql-persistent"
     With parameters:
      Memory limit=512Mi
      Database service name=postgresql
      PostgreSQL user=admin
      PostgreSQL password=redhat
      PostgreSQL database name=gitlab
      Volume capacity=512Mi
--> Creating resources ...
    Service "postgresql" created
    PersistentVolumeClaim "postgresql" created
    DeploymentConfig "postgresql" created
--> Success
    Run 'oc status' to view your app.
robert@c3p0:~$ 

** neuer Versuch

robert@c3p0:~$ oc new-app --template=postgresql-persistent \
>            -p POSTGRESQL_USER=admin,POSTGRESQL_PASSWORD=redhat,POSTGRESQL_DATABASE=gitlab
--> Deploying template postgresql-persistent in project openshift for "postgresql-persistent"
     With parameters:
      Memory Limit=512Mi
      Namespace=openshift
      Database Service Name=postgresql
      PostgreSQL User=admin
      PostgreSQL Password=redhat
      PostgreSQL Database Name=gitlab
      Volume Capacity=1Gi
--> Creating resources ...
    Service "postgresql" created
    PersistentVolumeClaim "postgresql" created
    DeploymentConfig "postgresql" created
--> Success
    Run 'oc status' to view your app.
robert@c3p0:~$ oc status
In project gitlab on server https://localhost:8443

svc/postgresql - 172.30.103.210:5432
  dc/postgresql deploys openshift/postgresql:latest 
    #1 deployment running for 16 seconds - 1 pod

Errors:
  * The image trigger for dc/postgresql will have no effect because is/postgresql does not exist.

View details with 'oc describe <resource>/<name>' or list everything with 'oc get all'.
robert@c3p0:~$ 

... mal schauen .. pvc von postgresql wird an pv gebunden

robert@c3p0:~$ oc get pv
NAME       LABELS       CAPACITY   ACCESSMODES   STATUS      CLAIM               REASON    AGE
nexus-pv   type=local   5Gi        RWO           Bound       ci/nexus-claim                3d
pv01       <none>       1Gi        RWO,RWX       Bound       gitlab/postgresql             31d
pv02       <none>       2Gi        RWO,RWX       Available                                 31d
pv03       <none>       3Gi        RWO,RWX       Available                                 31d
pv04       <none>       4Gi        RWO,RWX       Available                                 31d
pv05       <none>       5Gi        RWO,RWX       Available                                 31d
robert@c3p0:~$ 

OK - postgresql läuft 

robert@c3p0:~$ oc get all
NAME                 TRIGGERS                    LATEST
postgresql           ConfigChange, ImageChange   1
CONTROLLER           CONTAINER(S)                IMAGE(S)                                                                                               SELECTOR                                                              REPLICAS          AGE
postgresql-1         postgresql                  centos/postgresql-94-centos7@sha256:18b2de4a1ad747675549aec4c3b8632efa2504919e4113e0785b8a03d95b9e3a   deployment=postgresql-1,deploymentconfig=postgresql,name=postgresql   1                 6m
NAME                 CLUSTER_IP                  EXTERNAL_IP                                                                                            PORT(S)                                                               SELECTOR          AGE
postgresql           172.30.103.210              <none>                                                                                                 5432/TCP                                                              name=postgresql   6m
NAME                 READY                       STATUS                                                                                                 RESTARTS                                                              AGE
postgresql-1-tfenm   1/1                         Running                                                                                                0                                                                     6m
robert@c3p0:~$ 

** weiter geht's

oc edit scc anyuid

supplementalGroups:
  type: RunAsAny
users: 
- system:serviceaccount:gitlab:default

robert@c3p0:~$ oc edit scc anyuid
securitycontextconstraints/anyuid
robert@c3p0:~$ 

robert@c3p0:~$ oc new-app  sameersbn/redis
--> Found Docker image 3bb7dd5 (4 days old) from Docker Hub for "sameersbn/redis"
    * An image stream will be created as "redis:latest" that will track this image
    * This image will be deployed in deployment config "redis"
    * [WARNING] Image "redis" runs as the 'root' user which may not be permitted by your cluster administrator
    * Port 6379/tcp will be load balanced by service "redis"
--> Creating resources with label app=redis ...
    ImageStream "redis" created
    DeploymentConfig "redis" created
    Service "redis" created
--> Success
    Run 'oc status' to view your app.
robert@c3p0:~$ 

robert@c3p0:~$ oc status
In project gitlab on server https://localhost:8443

svc/postgresql - 172.30.103.210:5432
  dc/postgresql deploys openshift/postgresql:latest 
    #1 deployed 18 minutes ago - 1 pod

svc/redis - 172.30.144.246:6379
  dc/redis deploys imagestreamtag/redis:latest 
    #1 deployment running for 42 seconds - 1 pod

Errors:
  * The image trigger for dc/postgresql will have no effect because is/postgresql does not exist.

View details with 'oc describe <resource>/<name>' or list everything with 'oc get all'.
robert@c3p0:~$ 

robert@c3p0:~$ oc get all
NAME                 DOCKER REPO                 TAGS      UPDATED
redis                sameersbn/redis             latest    About a minute ago
NAME                 TRIGGERS                    LATEST
postgresql           ConfigChange, ImageChange   1
redis                ConfigChange, ImageChange   1
CONTROLLER           CONTAINER(S)                IMAGE(S)                                                                                               SELECTOR                                                              REPLICAS                           AGE
postgresql-1         postgresql                  centos/postgresql-94-centos7@sha256:18b2de4a1ad747675549aec4c3b8632efa2504919e4113e0785b8a03d95b9e3a   deployment=postgresql-1,deploymentconfig=postgresql,name=postgresql   1                                  18m
redis-1              redis                       sameersbn/redis@sha256:3bb7dd506c1712db60cd76e86c5a3f970bccc8963a9edb9639eeaf5dcef52bae                app=redis,deployment=redis-1,deploymentconfig=redis                   1                                  1m
NAME                 CLUSTER_IP                  EXTERNAL_IP                                                                                            PORT(S)                                                               SELECTOR                           AGE
postgresql           172.30.103.210              <none>                                                                                                 5432/TCP                                                              name=postgresql                    18m
redis                172.30.144.246              <none>                                                                                                 6379/TCP                                                              app=redis,deploymentconfig=redis   1m
NAME                 READY                       STATUS                                                                                                 RESTARTS                                                              AGE
postgresql-1-tfenm   1/1                         Running                                                                                                0                                                                     18m
redis-1-deploy       1/1                         Running                                                                                                0                                                                     1m
redis-1-e66al        0/1                         ContainerCreating                                                                                      0                                                                     1m
robert@c3p0:~$ 

robert@c3p0:~$ oc volume dc/redis --add --overwrite -t persistentVolumeClaim \
>                         --claim-name=redis-data --name=redis-volume-1 \
>                         --mount-path=/var/lib/redis
deploymentconfigs/redis
robert@c3p0:~$ 

robert@c3p0:~$ oc get all
NAME                 DOCKER REPO                 TAGS      UPDATED
redis                sameersbn/redis             latest    6 minutes ago
NAME                 TRIGGERS                    LATEST
postgresql           ConfigChange, ImageChange   1
redis                ConfigChange, ImageChange   2
CONTROLLER           CONTAINER(S)                IMAGE(S)                                                                                               SELECTOR                                                              REPLICAS                           AGE
postgresql-1         postgresql                  centos/postgresql-94-centos7@sha256:18b2de4a1ad747675549aec4c3b8632efa2504919e4113e0785b8a03d95b9e3a   deployment=postgresql-1,deploymentconfig=postgresql,name=postgresql   1                                  24m
redis-1              redis                       sameersbn/redis@sha256:3bb7dd506c1712db60cd76e86c5a3f970bccc8963a9edb9639eeaf5dcef52bae                app=redis,deployment=redis-1,deploymentconfig=redis                   1                                  6m
redis-2              redis                       sameersbn/redis@sha256:3bb7dd506c1712db60cd76e86c5a3f970bccc8963a9edb9639eeaf5dcef52bae                app=redis,deployment=redis-2,deploymentconfig=redis                   1                                  3m
NAME                 CLUSTER_IP                  EXTERNAL_IP                                                                                            PORT(S)                                                               SELECTOR                           AGE
postgresql           172.30.103.210              <none>                                                                                                 5432/TCP                                                              name=postgresql                    24m
redis                172.30.144.246              <none>                                                                                                 6379/TCP                                                              app=redis,deploymentconfig=redis   6m
NAME                 READY                       STATUS                                                                                                 RESTARTS                                                              AGE
postgresql-1-tfenm   1/1                         Running                                                                                                0                                                                     24m
redis-1-e66al        1/1                         Running                                                                                                0                                                                     6m
redis-2-deploy       1/1                         Running                                                                                                0                                                                     3m
redis-2-qia94        0/1                         Pending                                                                                                0                                                                     3m
robert@c3p0:~$ 

oc create -f - <<-EOF
{
    "apiVersion": "v1",
    "kind": "PersistentVolume",
    "metadata": {
        "name": "redis-data",
        "labels": {
           "type": "local"
        }
    },
    "spec": {
        "hostPath": {
            "path": "/tmp/redis-data"
        },
        "accessModes": [
            "ReadWriteOnce"
        ],
        "capacity": {
            "storage": "5Gi"
        },
        "persistentVolumeReclaimPolicy": "Retain"
    }
}
EOF