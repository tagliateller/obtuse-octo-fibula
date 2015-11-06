Öffnen der Web-Console
----------------------

https://master.url:8443/console/

OK

Einrichtung Authentifizierung
-----------------------------

sudo vi /etc/openshift/master/master-config.yaml

oauthConfig:
  assetPublicURL: https://54.165.85.104:8443/console/
    grantConfig:
        method: auto
	  identityProviders:
	    - name: my_htpasswd_provider
	        challenge: True
		    login: True
		        provider:
			      apiVersion: v1
			            kind: HTPasswdPasswordIdentityProvider
				          file: /etc/openshift/users.htpasswd

sudo yum -y install httpd-tools - zur Installation von htpasswd

sudo htpasswd -c /etc/openshift/users.htpasswd john

Neustart Master:

[centos@172 ~]$ sudo systemctl restart openshift-master.service
[centos@172 ~]$ sudo systemctl status openshift-master.service
openshift-master.service - OpenShift Master
   Loaded: loaded (/usr/lib/systemd/system/openshift-master.service; enabled)
   Active: active (running) since Thu 2015-09-17 07:57:56 UTC; 3s ago
     Docs: https://github.com/openshift/origin
 Main PID: 26146 (openshift)
   CGroup: /system.slice/openshift-master.service
           +-26146 /usr/bin/openshift start master --config=/etc/openshift/master/master-config.yaml --loglevel=2

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: I0917 07:57:57.126958   26146 nodecontroller.go:356] Recording Registered Node 172.31.6.162 in ...31.6.162

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: W0917 07:57:57.126976   26146 nodecontroller.go:420] Missing timestamp for Node 172.31.1.127. A...mestamp.

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: W0917 07:57:57.126991   26146 nodecontroller.go:420] Missing timestamp for Node 172.31.1.128. A...mestamp.

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: W0917 07:57:57.127000   26146 nodecontroller.go:420] Missing timestamp for Node 172.31.3.185. A...mestamp.

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: W0917 07:57:57.127009   26146 nodecontroller.go:420] Missing timestamp for Node 172.31.6.162. A...mestamp.

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: I0917 07:57:57.127487   26146 event.go:203] Event(api.ObjectReference{Kind:"Node", Namespace:"", Name:"...

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: I0917 07:57:57.127543   26146 event.go:203] Event(api.ObjectReference{Kind:"Node", Namespace:"", Name:"...

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: I0917 07:57:57.127558   26146 event.go:203] Event(api.ObjectReference{Kind:"Node", Namespace:"", Name:"...

Sep 17 07:57:57 172.31.6.162 openshift-master[26146]: I0917 07:57:57.127572   26146 event.go:203] Event(api.ObjectReference{Kind:"Node", Namespace:"", Name:"...

Sep 17 07:57:58 172.31.6.162 openshift-master[26146]: I0917 07:57:58.336356   26146 common.go:82] Self IP: 172.31.6.162.
Hint: Some lines were ellipsized, use -l to show in full.
[centos@172 ~]$

OK - john/smith kann sich anmelden, alles andere nicht

Docker-Registry
---------------

[centos@172 ~]$ sudo oadm registry --credentials=/etc/openshift/master/admin.kubeconfig --config=/etc/openshift/master/admin.kubeconfig
deploymentconfigs/docker-registry
services/docker-registry
[centos@172 ~]$ oadm registry --dry-run
Docker registry "docker-registry" service exists
[centos@172 ~]$

Router
------

[centos@172 ~]$ echo '{"kind":"ServiceAccount","apiVersion":"v1","metadata":{"name":"router"}}' | oc create -f -
serviceaccount "router" created

[centos@172 ~]$ oc edit scc privileged

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#

allowHostDirVolumePlugin: true
allowHostNetwork: true
allowHostPorts: true
allowPrivilegedContainer: true
allowedCapabilities: null
apiVersion: v1
groups:
- system:cluster-admins
- system:nodes
kind: SecurityContextConstraints
metadata:
  creationTimestamp: 2015-09-17T07:36:22Z
  name: privileged
  resourceVersion: "56"
  selfLink: /api/v1/securitycontextconstraints/privileged
  uid: cb59fef5-5d0e-11e5-9d12-0ac0cd2dd675
runAsUser:
  type: RunAsAny
seLinuxContext:
  type: RunAsAny
users:
- system:serviceaccount:openshift-infra:build-controller
- system:serviceaccount:default:router <-- diese Zeile hinzufügen

Speichern, dann:

securitycontextconstraints/privileged
[centos@172 ~]$

[centos@172 ~]$ oadm router --credentials="$KUBECONFIG" --dry-run --service-account=router
error: router "router" does not exist (no service)
[centos@172 ~]$


Das eigentliche Erzeugen des Routers geht so:


[centos@172 ~]$ sudo oadm router default-region --replicas=2 --credentials=/etc/openshift/master/admin.kubeconfig --service-account=router
password for stats user admin has been set to 4MtFGsvjqq
deploymentconfigs/default-region
services/default-region
[centos@172 ~]$


Image-Streams erzeugen
----------------------

[centos@172 ~]$ oc create -f /usr/share/openshift/examples/image-streams/image-streams-centos7.json
imagestream "ruby" created
imagestream "nodejs" created
imagestream "perl" created
imagestream "php" created
imagestream "python" created
imagestream "wildfly" created
imagestream "mysql" created
imagestream "postgresql" created
imagestream "mongodb" created
imagestream "jenkins" created
[centos@172 ~]$

Bei der Erzeugung der DB-Templates gibt es eine kleine Abweichung:

mongodb-persistent-template.json     mysql-persistent-template.json       postgresql-persistent-template.json
[centos@172 ~]$ oc create -f /usr/share/openshift/examples/db-templates -n openshift
Error from server: error when creating "/usr/share/openshift/examples/db-templates/mongodb-ephemeral-template.json": templates "mongodb-ephemeral" already exist
s
Error from server: error when creating "/usr/share/openshift/examples/db-templates/mongodb-persistent-template.json": templates "mongodb-persistent" already exi
sts
Error from server: error when creating "/usr/share/openshift/examples/db-templates/mysql-ephemeral-template.json": templates "mysql-ephemeral" already exists
Error from server: error when creating "/usr/share/openshift/examples/db-templates/mysql-persistent-template.json": templates "mysql-persistent" already exists
Error from server: error when creating "/usr/share/openshift/examples/db-templates/postgresql-ephemeral-template.json": templates "postgresql-ephemeral" already
 exists
Error from server: error when creating "/usr/share/openshift/examples/db-templates/postgresql-persistent-template.json": templates "postgresql-persistent" alrea
dy exists
[centos@172 ~]$

Auch gut ...

Und auch die Quickstart-Templates sind bereits da:

[centos@172 ~]$ oc create -f /usr/share/openshift/examples/db-templates -n openshift
Error from server: error when creating "/usr/share/openshift/examples/db-templates/mongodb-ephemeral-template.json": templates "mongodb-ephemeral" already exist
s
Error from server: error when creating "/usr/share/openshift/examples/db-templates/mongodb-persistent-template.json": templates "mongodb-persistent" already exi
sts
Error from server: error when creating "/usr/share/openshift/examples/db-templates/mysql-ephemeral-template.json": templates "mysql-ephemeral" already exists
Error from server: error when creating "/usr/share/openshift/examples/db-templates/mysql-persistent-template.json": templates "mysql-persistent" already exists
Error from server: error when creating "/usr/share/openshift/examples/db-templates/postgresql-ephemeral-template.json": templates "postgresql-ephemeral" already
 exists
Error from server: error when creating "/usr/share/openshift/examples/db-templates/postgresql-persistent-template.json": templates "postgresql-persistent" alrea
dy exists
[centos@172 ~]$ oc create -f /usr/share/openshift/examples/quickstart-templates/ -n openshift
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/cakephp-mysql.json": templates "cakephp-mysql-example" already exists

Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/cakephp.json": templates "cakephp-example" already exists
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/dancer-mysql.json": templates "dancer-mysql-example" already exists
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/dancer.json": templates "dancer-example" already exists
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/django-postgresql.json": templates "django-psql-example" already exis
ts
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/django.json": templates "django-example" already exists
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/jenkins-ephemeral-template.json": templates "jenkins-ephemeral" alrea
dy exists
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/jenkins-persistent-template.json": templates "jenkins-persistent" alr
eady exists
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/nodejs-mongodb.json": templates "nodejs-mongodb-example" already exis
ts
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/nodejs.json": templates "nodejs-example" already exists
Error from server: error when creating "/usr/share/openshift/examples/quickstart-templates/rails-postgresql.json": templates "rails-postgresql-example" already
exists
[centos@172 ~]$

Next
----

Weiter geht es mit dem Login in die Webconsole

- "New Project" anklicken
  id, name, Beschreibung muss angegeben werden
- Im Projekt nun "Get Started"
  - von einer GIT-URL
  - aus einem Template
    - Beispiel mit cakephp-mysql-example
      - es sind div. Parameter auszufüllen
        - SOURCE_REPOSITORY_URL
        usw., DB-Password, Security-Token usw.
        außerdem noch Labels ...
      - Create -> ohne weitere Änderung der Parameter steht nun innerhalb des Projektes zwei Services zur Verfügung (PHP und MySQL). MySQL ist sofort als Pod verfübar, 
        PHP versucht natürlich noch ein Build ...

Noch weitere Streams hinzufügen ...

[centos@172 ~]$ oc create -f /usr/share/openshift/examples/xpaas-streams
imagestream "jboss-webserver3-tomcat7-openshift" created
imagestream "jboss-webserver3-tomcat8-openshift" created
imagestream "jboss-eap6-openshift" created
imagestream "jboss-amq-6" created
[centos@172 ~]$

[centos@172 ~]$ oc create -f /usr/share/openshift/examples/xpaas-templates -n openshift
template "amq6-persistent" created
template "amq6" created
template "eap6-amq-persistent-sti" created
template "eap6-amq-sti" created
template "eap6-basic-sti" created
template "eap6-mongodb-persistent-sti" created
template "eap6-mongodb-sti" created
template "eap6-mysql-persistent-sti" created
template "eap6-mysql-sti" created
template "eap6-postgresql-persistent-sti" created
template "eap6-postgresql-sti" created
template "jws-tomcat7-basic-sti" created
template "jws-tomcat7-mongodb-persistent-sti" created
template "jws-tomcat7-mongodb-sti" created
template "jws-tomcat7-mysql-persistent-sti" created
template "jws-tomcat7-mysql-sti" created
template "jws-tomcat7-postgresql-persistent-sti" created
template "jws-tomcat7-postgresql-sti" created
template "jws-tomcat8-basic-sti" created
template "jws-tomcat8-mongodb-persistent-sti" created
template "jws-tomcat8-mongodb-sti" created
template "jws-tomcat8-mysql-persistent-sti" created
template "jws-tomcat8-mysql-sti" created
template "jws-tomcat8-postgresql-persistent-sti" created
template "jws-tomcat8-postgresql-sti" created
Error from server: error when creating "/usr/share/openshift/examples/xpaas-templates/eap6-https-sti.json": templates "eap6-basic-sti" already exists
Error from server: error when creating "/usr/share/openshift/examples/xpaas-templates/jws-tomcat7-https-sti.json": templates "jws-tomcat7-basic-sti" already exi
sts
Error from server: error when creating "/usr/share/openshift/examples/xpaas-templates/jws-tomcat8-https-sti.json": templates "jws-tomcat8-basic-sti" already exi
sts
[centos@172 ~]$

... damit stehen auch die Java-basierten Templates zur Verfügung.

REST-API
--------

Login:

[ec2-user@ip-172-31-11-251 openshift-ansible]$ curl -u john -kv -H "X-CSRF-TOKEN: xxx" 'https://ec2-54-152-198-165.compute-1.amazonaws.com:8443/oauth/authorize
?client_id=openshift-challenging-client&response_type=token'
Enter host password for user 'john':
*   Trying 172.31.6.162...
* Connected to ec2-54-152-198-165.compute-1.amazonaws.com (172.31.6.162) port 8443 (#0)
* Initializing NSS with certpath: sql:/etc/pki/nssdb
* skipping SSL peer certificate verification
* NSS: client certificate not found (nickname not specified)
* SSL connection using TLS_RSA_WITH_AES_128_CBC_SHA
* Server certificate:
*       subject: CN=172.31.6.162
*       start date: Sep 17 07:35:20 2015 GMT
*       expire date: Sep 16 07:35:21 2016 GMT
*       common name: 172.31.6.162
*       issuer: CN=openshift-signer@1442475317
* Server auth using Basic with user 'john'
> GET /oauth/authorize?client_id=openshift-challenging-client&response_type=token HTTP/1.1
> Authorization: Basic am9objpzbWl0aA==
> User-Agent: curl/7.40.0
> Host: ec2-54-152-198-165.compute-1.amazonaws.com:8443
> Accept: */*
> X-CSRF-TOKEN: xxx
>
< HTTP/1.1 302 Found
< Cache-Control: no-cache, no-store, max-age=0, must-revalidate
< Expires: Fri, 01 Jan 1990 00:00:00 GMT
< Location: https://54.152.198.165:8443/oauth/token/implicit#access_token=L9tUy1H-d57n1hb0Aw18HcTygXEojmq-Xis9Es-76VQ&expires_in=86400&token_type=Bearer
< Pragma: no-cache
< Set-Cookie: ssn=MTQ0MjQ3OTYyN3xWdG9Tczc1OHhjWGZHNGZjQ3gxcFRXR3N2aTRxdUpIT3pvbEZLSmY1dlpWSnd5UTdHNk5VZXltdWZhZ0ZwbHVJOHUxNGoyU3otWHN1cmRJR05aMEdfdWZXajJRQ0hDZV
l5R3FtSnNVYWtDdzRuSm9WQmw5aEo4NkVBb1V1LWF1RmtRPT18pwY17Y_1vKjmVmgxotFPwVuS0s2TuLeMDcISVPRw7gg=; Path=/; Expires=Thu, 17 Sep 2015 09:47:07 UTC; Max-Age=3600; Htt
pOnly; Secure
< Date: Thu, 17 Sep 2015 08:47:07 GMT
< Content-Length: 0