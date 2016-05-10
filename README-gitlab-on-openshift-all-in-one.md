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

redis-2-deploy bringt einen Fehler, ignorieren wir erst einmal

robert@c3p0:~/workspace/boxes/openshift-all-in-one$ oc get pods
NAME                 READY     STATUS    RESTARTS   AGE
postgresql-1-tfenm   1/1       Running   1          2d
redis-1-e66al        1/1       Running   1          2d
redis-2-deploy       0/1       Error     0          2d
robert@c3p0:~/workspace/boxes/openshift-all-in-one$ 

robert@c3p0:~/workspace/boxes/openshift-all-in-one$ oc logs redis-2-deploy
I0508 16:15:20.505739       1 deployer.go:202] Deploying from gitlab/redis-1 to gitlab/redis-2 (replicas: 1)
I0508 16:15:21.594834       1 rolling.go:228] RollingUpdater: Continuing update with existing controller redis-2.
I0508 16:15:21.655914       1 rolling.go:228] RollingUpdater: Scaling up redis-2 from 0 to 1, scaling down redis-1 from 1 to 0 (keep 1 pods available, don't exceed 2 pods)
I0508 16:15:21.656010       1 rolling.go:228] RollingUpdater: Scaling redis-2 up to 1
F0508 16:25:24.787182       1 deployer.go:70] timed out waiting for any update progress to be made
robert@c3p0:~/workspace/boxes/openshift-all-in-one$ 


robert@c3p0:~/workspace/boxes/openshift-all-in-one$ oc get svc postgresql redis
NAME         CLUSTER_IP       EXTERNAL_IP   PORT(S)    SELECTOR                           AGE
postgresql   172.30.103.210   <none>        5432/TCP   name=postgresql                    2d
redis        172.30.144.246   <none>        6379/TCP   app=redis,deploymentconfig=redis   2d
robert@c3p0:~/workspace/boxes/openshift-all-in-one$ 

oc new-app sameersbn/gitlab --name=gitlab-ce-3 -e 'GITLAB_HOST=http://gitlab.apps.10.2.2.2.xip.io' -e 'DB_TYPE=postgres' -e 'DB_HOST=172.30.103.210' -e 'DB_PORT=5432'    -e 'DB_NAME=gitlab'   -e 'DB_USER=admin' -e 'DB_PASS=redhat'   -e 'REDIS_HOST=172.30.144.246' -e 'REDIS_PORT=6379' -e 'GITLAB_SECRETS_DB_KEY_BASE=1234567890' -e 'SMTP_ENABLED=true' -e 'SMTP_HOST=smtp.mycompany.com' -e 'SMTP_PORT=25' -e 'GITLAB_EMAIL=no-reply@mycompany.com'

oc volumes dc/gitlab-ce-3 --add --claim-name=gitlab-log --mount-path=/var/log/gitlab -t persistentVolumeClaim --overwrite
oc volumes dc/gitlab-ce-3 --add --claim-name=gitlab-data --mount-path=/home/git/data -t persistentVolumeClaim --overwrite

Configuring gitlab::project_features...
Configuring gitlab::smtp_settings...
Configuring gitlab::oauth...
Configuring gitlab::ldap...
Configuring gitlab::backups...
Configuring gitlab-shell...
Configuring nginx...
Configuring nginx::gitlab...
Setting up GitLab for firstrun. Please be patient, this could take a while...
PG::InsufficientPrivilege: ERROR:  permission denied to create database
: CREATE DATABASE "gitlab" ENCODING = 'unicode'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql/database_statements.rb:155:in `async_exec'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql/database_statements.rb:155:in `block in execute'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/abstract_adapter.rb:472:in `block in log'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activesupport-4.2.6/lib/active_support/notifications/instrumenter.rb:20:in `instrument'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/abstract_adapter.rb:466:in `log'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql/database_statements.rb:154:in `execute'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql/schema_statements.rb:78:in `create_database'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/postgresql_database_tasks.rb:15:in `create'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:93:in `create'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:107:in `block in create_current'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:275:in `block in each_current_configuration'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:274:in `each'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:274:in `each_current_configuration'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:106:in `create_current'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/railties/databases.rake:17:in `block (2 levels) in <top (required)>'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:240:in `call'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:240:in `block in execute'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:235:in `each'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:235:in `execute'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:179:in `block in invoke_with_call_chain'
/usr/lib/ruby/2.1.0/monitor.rb:211:in `mon_synchronize'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:172:in `invoke_with_call_chain'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:201:in `block in invoke_prerequisites'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:199:in `each'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:199:in `invoke_prerequisites'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:178:in `block in invoke_with_call_chain'
/usr/lib/ruby/2.1.0/monitor.rb:211:in `mon_synchronize'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:172:in `invoke_with_call_chain'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:201:in `block in invoke_prerequisites'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:199:in `each'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:199:in `invoke_prerequisites'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:178:in `block in invoke_with_call_chain'
/usr/lib/ruby/2.1.0/monitor.rb:211:in `mon_synchronize'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:172:in `invoke_with_call_chain'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:165:in `invoke'
/home/git/gitlab/lib/tasks/gitlab/setup.rake:17:in `setup_db'
/home/git/gitlab/lib/tasks/gitlab/setup.rake:4:in `block (2 levels) in <top (required)>'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:240:in `call'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:240:in `block in execute'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:235:in `each'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:235:in `execute'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:179:in `block in invoke_with_call_chain'
/usr/lib/ruby/2.1.0/monitor.rb:211:in `mon_synchronize'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:172:in `invoke_with_call_chain'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/task.rb:165:in `invoke'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:150:in `invoke_task'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:106:in `block (2 levels) in top_level'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:106:in `each'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:106:in `block in top_level'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:115:in `run_with_threads'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:100:in `top_level'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:78:in `block in run'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:176:in `standard_exception_handling'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/lib/rake/application.rb:75:in `run'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/rake-10.5.0/bin/rake:33:in `<top (required)>'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/bin/rake:23:in `load'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/bin/rake:23:in `<main>'
Couldn't create database for {"adapter"=>"postgresql", "encoding"=>"unicode", "database"=>"gitlab", "host"=>"172.30.103.210", "port"=>5432, "username"=>"admin", "password"=>"redhat", "pool"=>10}
rake aborted!
ActiveRecord::StatementInvalid: PG::InsufficientPrivilege: ERROR:  permission denied to create extension "pg_trgm"
HINT:  Must be superuser to create this extension.
: CREATE EXTENSION IF NOT EXISTS "pg_trgm"
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:592:in `async_exec'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:592:in `block in exec_no_cache'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/abstract_adapter.rb:472:in `block in log'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activesupport-4.2.6/lib/active_support/notifications/instrumenter.rb:20:in `instrument'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/abstract_adapter.rb:466:in `log'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:592:in `exec_no_cache'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:584:in `execute_and_clear'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql/database_statements.rb:160:in `exec_query'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:338:in `enable_extension'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/migration.rb:665:in `block in method_missing'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/migration.rb:634:in `block in say_with_time'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/migration.rb:634:in `say_with_time'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/migration.rb:654:in `method_missing'
/home/git/gitlab/db/schema.rb:18:in `block in <top (required)>'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/schema.rb:41:in `instance_eval'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/schema.rb:41:in `define'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/schema.rb:61:in `define'
/home/git/gitlab/db/schema.rb:14:in `<top (required)>'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activesupport-4.2.6/lib/active_support/dependencies.rb:268:in `load'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activesupport-4.2.6/lib/active_support/dependencies.rb:268:in `block in load'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activesupport-4.2.6/lib/active_support/dependencies.rb:240:in `load_dependency'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activesupport-4.2.6/lib/active_support/dependencies.rb:268:in `load'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:218:in `load_schema_for'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:235:in `block in load_schema_current'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:275:in `block in each_current_configuration'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:274:in `each'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:274:in `each_current_configuration'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/tasks/database_tasks.rb:234:in `load_schema_current'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/railties/databases.rake:247:in `block (3 levels) in <top (required)>'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/railties/databases.rake:251:in `block (3 levels) in <top (required)>'
/home/git/gitlab/lib/tasks/gitlab/setup.rake:17:in `setup_db'
/home/git/gitlab/lib/tasks/gitlab/setup.rake:4:in `block (2 levels) in <top (required)>'
PG::InsufficientPrivilege: ERROR:  permission denied to create extension "pg_trgm"
HINT:  Must be superuser to create this extension.
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:592:in `async_exec'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:592:in `block in exec_no_cache'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/abstract_adapter.rb:472:in `block in log'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activesupport-4.2.6/lib/active_support/notifications/instrumenter.rb:20:in `instrument'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/abstract_adapter.rb:466:in `log'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:592:in `exec_no_cache'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:584:in `execute_and_clear'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql/database_statements.rb:160:in `exec_query'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/connection_adapters/postgresql_adapter.rb:338:in `enable_extension'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/migration.rb:665:in `block in method_missing'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/migration.rb:634:in `block in say_with_time'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/migration.rb:634:in `say_with_time'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/migration.rb:654:in `method_missing'
/home/git/gitlab/db/schema.rb:18:in `block in <top (required)>'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/schema.rb:41:in `instance_eval'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/schema.rb:41:in `define'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activerecord-4.2.6/lib/active_record/schema.rb:61:in `define'
/home/git/gitlab/db/schema.rb:14:in `<top (required)>'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/activesupport-4.2.6/lib/active_support/dependencies.rb:268:in `load'
/home/git/gitlab/vendor/bundle/ruby/2.1.0/gems/acti

fehlende privilegien ???


