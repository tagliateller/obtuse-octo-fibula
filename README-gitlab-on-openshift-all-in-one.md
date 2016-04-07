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

