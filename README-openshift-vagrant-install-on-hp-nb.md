* Installation OpenShift Origin 
** Vagrant auf lokalem NB

./openshift-ansible

vagrant up --no-provision
vagrant provision

PLAY RECAP ******************************************************************** 
localhost                  : ok=21   changed=0    unreachable=0    failed=0   
master                     : ok=335  changed=64   unreachable=0    failed=0   
node1                      : ok=101  changed=29   unreachable=0    failed=0   
node2                      : ok=101  changed=29   unreachable=0    failed=0   

** TODO
*** Hochfahren - geht das ?

ja - z.B. mit virtualbox console master starten -> login geht, oc get nodes auf master zeigt nodes == notready an. Node starten mit vb console -> node == ready

*** Tutorials abarbeiten siehe paas/tutorials
*** ManageIQ -> Monitoring klären

## Weitere Schritte
### Authenification

sudo yum install httpd-tools

[vagrant@ose3-master master]$ sudo htpasswd -c /etc/origin/master/users.htpasswd john
New password: 
Re-type new password: 
Adding password for user john
[vagrant@ose3-master master]$ 

sudo vi /etc/origin/master/master-config.yml

[vagrant@ose3-master master]$ sudo htpasswd -c /etc/origin/master/users.htpasswd john
New password: 
Re-type new password: 
Adding password for user john
[vagrant@ose3-master master]$ 

[vagrant@ose3-master master]$ sudo service origin-master status
Redirecting to /bin/systemctl status  origin-master.service
● origin-master.service - Origin Master Service
   Loaded: loaded (/usr/lib/systemd/system/origin-master.service; enabled; vendor preset: disabled)
   Active: active (running) since Di 2016-04-12 13:08:14 EDT; 19min ago
     Docs: https://github.com/openshift/origin
 Main PID: 1107 (openshift)
   CGroup: /system.slice/origin-master.service
           └─1107 /usr/bin/openshift start master --config=/etc/origin/master/master-config.yaml --loglevel=2

Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [549.217µs] [501.541µs] Conversion done
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [562.35µs] [13.133µs] About to store object in database
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [882.014347ms] [881.451997ms] END
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: I0412 13:08:21.641212    1107 trace.go:57] Trace "Create /api/v1/namespaces/default/events" (star...00 EDT):
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [46.849µs] [46.849µs] About to convert to expected version
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [276.208µs] [229.359µs] Conversion done
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [310.21µs] [34.002µs] About to store object in database
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [882.544226ms] [882.234016ms] Object stored in database
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [882.573978ms] [29.752µs] Self-link added
Apr 12 13:08:21 ose3-master.example.com origin-master[1107]: [882.938246ms] [364.268µs] END
Hint: Some lines were ellipsized, use -l to show in full.
[vagrant@ose3-master master]$ 

[vagrant@ose3-master master]$ sudo service origin-master restart
Redirecting to /bin/systemctl restart  origin-master.service
[vagrant@ose3-master master]$ sudo service origin-master status
Redirecting to /bin/systemctl status  origin-master.service
● origin-master.service - Origin Master Service
   Loaded: loaded (/usr/lib/systemd/system/origin-master.service; enabled; vendor preset: disabled)
   Active: active (running) since Di 2016-04-12 13:27:46 EDT; 6s ago
     Docs: https://github.com/openshift/origin
 Main PID: 3418 (openshift)
   CGroup: /system.slice/origin-master.service
           └─3418 /usr/bin/openshift start master --config=/etc/origin/master/master-config.yaml --loglevel=2

Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: [685.288479ms] [685.286378ms] Decoding dir /openshift.io/imagestreams/openshift END
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: [685.290894ms] [2.415µs] Decoded 1 nodes
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: [685.291785ms] [891ns] END
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: I0412 13:27:51.028791    3418 trace.go:57] Trace "List /oapi/v1/imagestreams" (started 2016-04-12...00 EDT):
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: [80.688µs] [80.688µs] About to List from storage
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: [707.515278ms] [707.43459ms] Listing from storage done
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: [707.60621ms] [90.932µs] Self-linking done
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: [710.522821ms] [2.916611ms] Writing http response done (10 items)
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: [710.525011ms] [2.19µs] END
Apr 12 13:27:51 ose3-master.example.com origin-master[3418]: I0412 13:27:51.404077    3418 start_master.go:635] Started Origin Controllers
Hint: Some lines were ellipsized, use -l to show in full.
[vagrant@ose3-master master]$ 
```
## Integrated Docker Registry

[vagrant@ose3-master master]$ sudo oadm registry --config=admin.kubeconfig --credentials=openshift-registry.kubeconfig 
Flag --credentials has been deprecated, use --service-account to specify the service account the registry will use to make API calls
deploymentconfig "docker-registry" created
service "docker-registry" created
[vagrant@ose3-master master]$ 

TODO: Nach dem wiederanfahren war der Master nicht schedulable: 

oadm manage-node ose3-master.example.com --schedulable=true

OK, ist bereinigt

[vagrant@ose3-master master]$ oc get nodes
NAME                      STATUS                     AGE
ose3-master.example.com   Ready,SchedulingDisabled   5d
ose3-node1.example.com    Ready                      5d
ose3-node2.example.com    Ready                      5d
[vagrant@ose3-master master]$ oadm manage-node ose3-master.example.com --schedulable=true
NAME                      STATUS    AGE
ose3-master.example.com   Ready     5d
[vagrant@ose3-master master]$ oc get nodes
NAME                      STATUS    AGE
ose3-master.example.com   Ready     5d
ose3-node1.example.com    Ready     5d
ose3-node2.example.com    Ready     5d
```

OK, ggf. hier schauen: 

https://github.com/openshift/training/blob/master/02-Installation-and-Scheduler.md#node-labels

## Labeling

ausführen (auf dem master):

```
oc label node/ose3-master.example.com region=infra zone=default
oc label node/ose3-node1.example.com region=primary zone=east
oc label node/ose3-node2.example.com region=primary zone=west
```

oc get nodes bringt danach immer noch keine Labels:

[vagrant@ose3-master ~]$ oc get nodes
NAME                      STATUS    AGE
ose3-master.example.com   Ready     6d
ose3-node1.example.com    Ready     6d
ose3-node2.example.com    Ready     6d
[vagrant@ose3-master ~]$ 

erneutes ausführen zeigt aber:

[vagrant@ose3-master ~]$ oc label ose3-master.example.com region=infra zone=default
error: you must provide one or more resources by argument or filename (.json|.yaml|.yml|stdin)
[vagrant@ose3-master ~]$ oc label node/ose3-master.example.com region=infra zone=default
'region' already has a value (infra), and --overwrite is false
'zone' already has a value (default), and --overwrite is false

Annahme: Label sind gesetzt.

## weiter mit Trainig, registry ignorieren

```
[vagrant@ose3-master ~]$ oadm new-project demo --display-name="OpenShift 3 Demo" --description="das erste Projekt" --admin=john
Created project demo
```

Nutzer anlegen

[vagrant@ose3-master ~]$ sudo useradd john
sudo passwd john 
john / smith

Git clonen für das Training

[vagrant@ose3-master ~]$ git clone https://github.com/openshift/training.git
Klone nach 'training'...
remote: Counting objects: 3255, done.
remote: Total 3255 (delta 0), reused 0 (delta 0), pack-reused 3255
Empfange Objekte: 100% (3255/3255), 1.64 MiB | 618.00 KiB/s, done.
Löse Unterschiede auf: 100% (2059/2059), done.

Quota anlegen

[vagrant@ose3-master ~]$ oc create -f ~/training/content/quota.json -n demo
resourcequota "test-quota" created
[vagrant@ose3-master ~]$ oc get -n demo quota
NAME         AGE
test-quota   8s
[vagrant@ose3-master ~]$ 

[vagrant@ose3-master ~]$ oc describe quota test-quota -n demo
Name:		test-quota
Namespace:	demo
Resource	Used	Hard
--------	----	----
cpu		0	500m
memory		0	512Mi
pods		0	3
resourcequotas	1	1


[vagrant@ose3-master ~]$ 

Limits

[vagrant@ose3-master ~]$ oc create -f ~/training/content/limits.json -n demo
limitrange "limits" created
[vagrant@ose3-master ~]$ oc describe limitranges limits -n demo
Name:		limits
Namespace:	demo
Type		Resource	Min	Max	Default Request	Default Limit	Max Limit/Request Ratio
----		--------	---	---	---------------	-------------	-----------------------
Pod		cpu		10m	500m	-		-		-
Pod		memory		5Mi	750Mi	-		-		-
Container	memory		5Mi	750Mi	100Mi		100Mi		-
Container	cpu		10m	500m	100m		100m		-


[vagrant@ose3-master ~]$ 

## Login

[john@ose3-master ~]$ oc login -u john --certificate-authority=/etc/origin/master/ca.crt --server=https://ose3-master.example.com:8443
Authentication required for https://ose3-master.example.com:8443 (openshift)
Username: john
Password: 
Error from server: Internal error occurred: unexpected response: 500
[john@ose3-master ~]$ 

ohhhhh ...

Log:

[vagrant@ose3-master ~]$ sudo journalctl -f -u origin-master.service

Apr 14 01:55:38 ose3-master.example.com origin-master[1114]: E0414 01:55:38.950296    1114 basicauth.go:41] Error authenticating login "john" with provider "my_htpasswd_provider": user "john" cannot be claimed by identity "my_htpasswd_provider:john" because it is already mapped to [allow_all:john]
Apr 14 01:55:38 ose3-master.example.com origin-master[1114]: E0414 01:55:38.950508    1114 errorpage.go:30] AuthenticationError: user "john" cannot be claimed by identity "my_htpasswd_provider:john" because it is already mapped to [allow_all:john]
Apr 14 01:57:19 ose3-master.example.com origin-master[1114]: E0414 01:57:19.514018    1114 login.go:162] Error authenticating "john" with provider "my_htpasswd_provider": user "john" cannot be claimed by identity "my_htpasswd_provider:john" because it is already mapped to [allow_all:john]
Apr 14 01:57:27 ose3-master.example.com origin-master[1114]: E0414 01:57:27.302636    1114 login.go:162] Error authenticating "john" with provider "my_htpasswd_provider": user "john" cannot be claimed by identity "my_htpasswd_provider:john" because it is already mapped to [allow_all:john]

## nächster Versuch: wir folgen der Install-Routine, fahren master und nodes nicht herunter
... :-(
## vagrant boxen hochfahren, rest der installation mit byo

```
# Create an OSEv3 group that contains the masters and nodes groups
[OSEv3:children]
masters
nodes

# Set variables common for all OSEv3 hosts
[OSEv3:vars]
# SSH user, this user should allow ssh based auth without requiring a password
ansible_ssh_user=vagrant

# If ansible_ssh_user is not root, ansible_sudo must be set to true
ansible_sudo=true

product_type=openshift
deployment_type=origin

# uncomment the following to enable htpasswd authentication; defaults to DenyAllPasswordIdentityProvider
openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider', 'filename': '/etc/origin/origin-passwd'}]

# host group for masters
[masters]
master ansible_ssh_host=ose3-master.example.com

# host group for nodes, includes region info
[nodes]
master ansible_ssh_host=ose3-master.example.com openshift_node_labels="{'region': 'infra', 'zone': 'default'}"
node1 ansible_ssh_host=ose3-node1.example.com openshift_node_labels="{'region': 'primary', 'zone': 'east'}"
node2 ansible_ssh_host=ose3-node2.example.com openshift_node_labels="{'region': 'primary', 'zone': 'west'}"

```

robert@c3p0:~/workspace/openshift-ansible$ ansible-playbook -i origin-vagrant-hp-nb.inventory --private-key=~/.vagrant.d/insecure_private_key playbooks/byo/config.yml -vvvv

... geht auch nicht ...

--> siehe obere config - die ssh-ports müssen weggelassen werden

```
export ANSIBLE_HOST_KEY_CHECKING=False
```

git checkout 984645e9c6106d08608d88ee5f0fe9d4388dcf0f scheint den aktuellen Docker-Fehler zu beheben.

mit diesem Stand ist die Ausführung erfolgreich:

PLAY RECAP ******************************************************************** 
localhost                  : ok=21   changed=0    unreachable=0    failed=0   
master                     : ok=322  changed=68   unreachable=0    failed=0   
node1                      : ok=87   changed=29   unreachable=0    failed=0   
node2                      : ok=87   changed=29   unreachable=0    failed=0   

es fehlen wieder die Labels:

[vagrant@ose3-master ~]$ oc get nodes
NAME                      STATUS                     AGE
ose3-master.example.com   Ready,SchedulingDisabled   23m
ose3-node1.example.com    Ready                      23m
ose3-node2.example.com    Ready                      23m
[vagrant@ose3-master ~]$ 

scheinen doch da zu sein, werden nur nicht angezeigt:

[vagrant@ose3-master ~]$ oc get nodes --selector='region=infra'
NAME                      STATUS                     AGE
ose3-master.example.com   Ready,SchedulingDisabled   24m
[vagrant@ose3-master ~]$ oc get nodes --selector='region=primary'
NAME                     STATUS    AGE
ose3-node1.example.com   Ready     24m
ose3-node2.example.com   Ready     24m
[vagrant@ose3-master ~]$ 

Umstellung auf htpasswd während der Installation scheint geklappt zu haben ...

[vagrant@ose3-master ~]$ sudo htpasswd /etc/origin/origin-passwd john
New password: 
Re-type new password: 
Adding password for user john
[vagrant@ose3-master ~]$ 

Login in webconsole ist danach auch sofort möglich

[vagrant@ose3-master ~]$ cd /etc/origin/master/
[vagrant@ose3-master master]$ sudo oadm registry --config=admin.kubeconfig --credentials=openshift-registry.kubeconfig
Flag --credentials has been deprecated, use --service-account to specify the service account the registry will use to make API calls
deploymentconfig "docker-registry" created
service "docker-registry" created
[vagrant@ose3-master master]$ 

es scheint so, dass bereits ein router deployt versucht wurde:

[vagrant@ose3-master master]$ oc get pods
NAME                       READY     STATUS              RESTARTS   AGE
docker-registry-1-deploy   0/1       ContainerCreating   0          31s
router-1-deploy            0/1       Pending             0          40m
[vagrant@ose3-master master]$ oc get pods
NAME                       READY     STATUS              RESTARTS   AGE
docker-registry-1-deploy   0/1       ContainerCreating   0          40s
router-1-deploy            0/1       Pending             0          40m
[vagrant@ose3-master master]$ oc get pods
NAME                       READY     STATUS              RESTARTS   AGE
docker-registry-1-deploy   0/1       ContainerCreating   0          56s
router-1-deploy            0/1       Pending             0          41m
[vagrant@ose3-master master]$ oc get pods
NAME                       READY     STATUS              RESTARTS   AGE
docker-registry-1-deploy   0/1       ContainerCreating   0          1m
router-1-deploy            0/1       Pending             0          41m

## docker status node 2

[vagrant@ose3-node2 ~]$ sudo systemctl status docker.service
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
  Drop-In: /usr/lib/systemd/system/docker.service.d
           └─docker-sdn-ovs.conf
   Active: active (running) since Do 2016-04-21 14:34:47 GMT-2; 4min 47s ago
     Docs: http://docs.docker.com
 Main PID: 16173 (sh)
   Memory: 99.7M
   CGroup: /system.slice/docker.service
           ├─16173 /bin/sh -c /usr/bin/docker daemon $OPTIONS            $DOCKER_STORAGE_OPTIONS            $DOCKER_NETWORK_OPTIONS            $ADD_REGISTRY          ...
           ├─16174 /usr/bin/docker daemon --selinux-enabled --insecure-registry=172.30.0.0/16 -b=lbr0 --mtu=1450 --add-registry registry.access.redhat.com
           └─16175 /usr/bin/forward-journald -tag docker

Apr 21 14:38:18 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:18.052308634+02:00" level=error msg="HTTP Error" err="No such image: ope...Code=404
Apr 21 14:38:18 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:18.054970868+02:00" level=info msg="{Action=create, LoginUID=4294967295,...=15997}"
Apr 21 14:38:19 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:19.099162022+02:00" level=warning msg="Error getting v2 registry: endpoi... v2 API"
Apr 21 14:38:28 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:28.512784307+02:00" level=info msg="{Action=create, LoginUID=4294967295,...=15997}"
Apr 21 14:38:28 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:28.856759818+02:00" level=info msg="{Action=start, ID=85125e8cd1d8057e23...e, OpenS
Apr 21 14:38:29 ose3-node2.example.com forward-journal[16175]: 2016/04/21 14:38:29 http: multiple response.WriteHeader calls
Apr 21 14:38:29 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:29.600584446+02:00" level=error msg="Handler for GET /images/openshift/o...:v1.1.6"
Apr 21 14:38:29 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:29.600653921+02:00" level=error msg="HTTP Error" err="No such image: ope...Code=404
Apr 21 14:38:29 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:29.603576501+02:00" level=info msg="{Action=create, LoginUID=4294967295,...=15997}"
Apr 21 14:38:30 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:30.557015134+02:00" level=warning msg="Error getting v2 registry: endpoi... v2 API"
Hint: Some lines were ellipsized, use -l to show in full.

[vagrant@ose3-node2 ~]$ sudo systemctl status docker.service -l
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
  Drop-In: /usr/lib/systemd/system/docker.service.d
           └─docker-sdn-ovs.conf
   Active: active (running) since Do 2016-04-21 14:34:47 GMT-2; 6min ago
     Docs: http://docs.docker.com
 Main PID: 16173 (sh)
   Memory: 27.9M
   CGroup: /system.slice/docker.service
           ├─16173 /bin/sh -c /usr/bin/docker daemon $OPTIONS            $DOCKER_STORAGE_OPTIONS            $DOCKER_NETWORK_OPTIONS            $ADD_REGISTRY            $BLOCK_REGISTRY            $INSECURE_REGISTRY            2>&1 | /usr/bin/forward-journald -tag docker
           ├─16174 /usr/bin/docker daemon --selinux-enabled --insecure-registry=172.30.0.0/16 -b=lbr0 --mtu=1450 --add-registry registry.access.redhat.com
           └─16175 /usr/bin/forward-journald -tag docker

Apr 21 14:38:28 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:28.856759818+02:00" level=info msg="{Action=start, ID=85125e8cd1d8057e23eecb3794212d621d2775f7839396070264510220f6034b, LoginUID=4294967295, PID=15997, Config={Hostname=docker-registry-1-deploy, AttachStdin=false, AttachStdout=false, AttachStderr=false, Tty=false, OpenStdin=false, StdinOnce=false, Env=[ROUTER_SERVICE_HOST=172.30.231.206 ROUTER_PORT_1936_TCP_ADDR=172.30.231.206 DOCKER_REGISTRY_SERVICE_PORT=5000 DOCKER_REGISTRY_SERVICE_PORT_5000_TCP=5000 DOCKER_REGISTRY_PORT=tcp://172.30.31.49:5000 KUBERNETES_PORT=tcp://172.30.0.1:443 KUBERNETES_PORT_443_TCP_PORT=443 ROUTER_SERVICE_PORT=80 ROUTER_PORT_80_TCP=tcp://172.30.231.206:80 ROUTER_PORT_80_TCP_PROTO=tcp DOCKER_REGISTRY_PORT_5000_TCP_PROTO=tcp KUBERNETES_SERVICE_PORT=443 KUBERNETES_PORT_443_TCP_ADDR=172.30.0.1 KUBERNETES_PORT_53_UDP_ADDR=172.30.0.1 KUBERNETES_PORT_53_TCP_ADDR=172.30.0.1 ROUTER_SERVICE_PORT_443_TCP=443 ROUTER_SERVICE_PORT_1936_TCP=1936 ROUTER_PORT_80_TCP_PORT=80 ROUTER_PORT_443_TCP=tcp://172.30.231.206:443 ROUTER_PORT_443_TCP_PROTO=tcp ROUTER_PORT_1936_TCP=tcp://172.30.231.206:1936 DOCKER_REGISTRY_SERVICE_HOST=172.30.31.49 DOCKER_REGISTRY_PORT_5000_TCP=tcp://172.30.31.49:5000 KUBERNETES_PORT_443_TCP_PROTO=tcp KUBERNETES_PORT_53_UDP=udp://172.30.0.1:53 ROUTER_PORT_80_TCP_ADDR=172.30.231.206 ROUTER_PORT_443_TCP_ADDR=172.30.231.206 KUBERNETES_SERVICE_PORT_DNS=53 KUBERNETES_PORT_443_TCP=tcp://172.30.0.1:443 ROUTER_PORT_1936_TCP_PROTO=tcp ROUTER_PORT_1936_TCP_PORT=1936 KUBERNETES_PORT_53_UDP_PROTO=udp KUBERNETES_PORT_53_TCP=tcp://172.30.0.1:53 KUBERNETES_PORT_53_TCP_PROTO=tcp ROUTER_SERVICE_PORT_80_TCP=80 KUBERNETES_SERVICE_PORT_DNS_TCP=53 KUBERNETES_PORT_53_UDP_PORT=53 KUBERNETES_PORT_53_TCP_PORT=53 ROUTER_PORT=tcp://172.30.231.206:80 ROUTER_PORT_443_TCP_PORT=443 DOCKER_REGISTRY_PORT_5000_TCP_PORT=5000 DOCKER_REGISTRY_PORT_5000_TCP_ADDR=172.30.31.49 KUBERNETES_SERVICE_HOST=172.30.0.1 KUBERNETES_SERVICE_PORT_HTTPS=443], Image=openshift/origin-pod:v1.1.6, Entrypoint={parts:[/pod]}, NetworkDisabled=false, Labels=map[io.kubernetes.container.restartCount:0 io.kubernetes.container.terminationMessagePath: io.kubernetes.pod.name:docker-registry-1-deploy io.kubernetes.pod.namespace:default io.kubernetes.pod.terminationGracePeriod:30 io.kubernetes.pod.uid:ecc49787-07bd-11e6-bcd3-525400b263eb io.kubernetes.container.hash:52e6dca3 io.kubernetes.container.name:POD]}, HostConfig={MemorySwap=-1, CPUShares=2, OomKillDisable=false, Privileged=false, PublishAllPorts=false, DNS=[172.30.0.1 10.0.2.2], DNSSearch=[default.svc.cluster.local svc.cluster.local cluster.local example.com], NetworkMode=default, SecurityOpt=[label:level:s0:c3,c2], ReadonlyRootfs=false, LogConfig={Type:json-file Config:map[]}, ShmSize=67108864}}"
Apr 21 14:38:29 ose3-node2.example.com forward-journal[16175]: 2016/04/21 14:38:29 http: multiple response.WriteHeader calls
Apr 21 14:38:29 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:29.600584446+02:00" level=error msg="Handler for GET /images/openshift/origin-deployer:v1.1.6/json returned error: No such image: openshift/origin-deployer:v1.1.6"
Apr 21 14:38:29 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:29.600653921+02:00" level=error msg="HTTP Error" err="No such image: openshift/origin-deployer:v1.1.6" statusCode=404
Apr 21 14:38:29 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:29.603576501+02:00" level=info msg="{Action=create, LoginUID=4294967295, PID=15997}"
Apr 21 14:38:30 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:38:30.557015134+02:00" level=warning msg="Error getting v2 registry: endpoint does not support v2 API"
Apr 21 14:40:35 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:40:35.111630159+02:00" level=info msg="{Action=create, LoginUID=4294967295, PID=15997}"
Apr 21 14:40:36 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:40:36.317373060+02:00" level=info msg="{Action=start, ID=017b446fe98cef6f0ba4a3418d4ba21624850a69db7e4c223079501e03b63a1e, LoginUID=4294967295, PID=15997, Config={Hostname=017b446fe98c, User=1000010000, AttachStdin=false, AttachStdout=false, AttachStderr=false, ExposedPorts=map[53/tcp:{} 8443/tcp:{}], Tty=false, OpenStdin=false, StdinOnce=false, Env=[KUBERNETES_MASTER=https://ose3-master.example.com:8443 OPENSHIFT_MASTER=https://ose3-master.example.com:8443 BEARER_TOKEN_FILE=/var/run/secrets/kubernetes.io/serviceaccount/token OPENSHIFT_CA_DATA=-----BEGIN CERTIFICATE-----\nMIIC5jCCAdCgAwIBAgIBATALBgkqhkiG9w0BAQswJjEkMCIGA1UEAwwbb3BlbnNo\naWZ0LXNpZ25lckAxNDYxMjQxNTYyMB4XDTE2MDQyMTEyMjYwMloXDTIxMDQyMDEy\nMjYwM1owJjEkMCIGA1UEAwwbb3BlbnNoaWZ0LXNpZ25lckAxNDYxMjQxNTYyMIIB\nIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAt2Yv4UZVD1WEJK7Uqa1XMR7F\njqWO0tKGD443IeFohDD/hx194xv10hWrd/LVwaDCl6ZuNY4nGzY5ZuFpW53Cd2MK\nCn4gKpjTqwbuPtiWzs6d30eKIawxTQMtVgZ/qTeC87hyMFgkshrJ/P153ziRIpMJ\nxcuNR/9661aT38+4Y0aMTa9VM4ZCaGnyWWLUntMUsnkysmxrE1vyOKpAfJKwPu2I\nEqi0UciUFEy9GEYeJuVQ7TvbWkAS7aJlKuVYLxlH5W2VaRQ4w4soAW66WlEKT29+\nkNSp9FPPlNB4QgY50cksui6spWhHkGX32yMcFNYUVs39jWoI8tkHYmrXJLkutwID\nAQABoyMwITAOBgNVHQ8BAf8EBAMCAKQwDwYDVR0TAQH/BAUwAwEB/zALBgkqhkiG\n9w0BAQsDggEBAEbLFljfDnMTUOFy1dmh60D1/2AWeLqsbybf1q9kuXXVJARL//KF\nfZWdcNuq9juS/rIq0R5t6vaeXL27Dg736e1GukPmdCI1Bsr/L5xMbEkQ2+HkdAFe\nC0ibh/Lr4yHKMz9q0AZAdaAu2lRZxEdcd0+/hoI7Kew0Ej9iKSeEDBn8bUXNA1xh\n1OPJN0g99sI2vBI+cp8TAQn2t7LhO9tEoFwPp8oVZMmkFVsLWtXVtdQuLQIJ/n8q\nyx0ZmmtF7h4XMmzDbrXLR5W7sx3cbqfGNqFCawaueV3reRqavnYx1uwSXWkKafsZ\nrE5s2HcwJSOyR51ei9wbHzDYB/fWu/AmypE=\n-----END CERTIFICATE-----\n OPENSHIFT_DEPLOYMENT_NAME=docker-registry-1 OPENSHIFT_DEPLOYMENT_NAMESPACE=default ROUTER_PORT_1936_TCP_PORT=1936 DOCKER_REGISTRY_PORT_5000_TCP_PROTO=tcp DOCKER_REGISTRY_PORT_5000_TCP_ADDR=172.30.31.49 KUBERNETES_PORT_443_TCP_PROTO=tcp KUBERNETES_PORT_443_TCP_PORT=443 KUBERNETES_PORT_53_TCP=tcp://172.30.0.1:53 ROUTER_SERVICE_PORT=80 ROUTER_PORT_80_TCP_ADDR=172.30.231.206 KUBERNETES_SERVICE_PORT_HTTPS=443 KUBERNETES_SERVICE_PORT_DNS=53 ROUTER_PORT_1936_TCP_ADDR=172.30.231.206 DOCKER_REGISTRY_PORT_5000_TCP_PORT=5000 KUBERNETES_SERVICE_HOST=172.30.0.1 KUBERNETES_PORT_53_TCP_PROTO=tcp KUBERNETES_PORT_53_TCP_ADDR=172.30.0.1 ROUTER_PORT_80_TCP_PORT=80 DOCKER_REGISTRY_SERVICE_PORT=5000 ROUTER_PORT_443_TCP_ADDR=172.30.231.206 ROUTER_PORT_1936_TCP=tcp://172.30.231.206:1936 ROUTER_PORT_1936_TCP_PROTO=tcp KUBERNETES_SERVICE_PORT=443 KUBERNETES_PORT_53_TCP_PORT=53 ROUTER_SERVICE_PORT_1936_TCP=1936 ROUTER_PORT_80_TCP_PROTO=tcp ROUTER_PORT_443_TCP=tcp://172.30.231.206:443 ROUTER_PORT_443_TCP_PROTO=tcp KUBERNETES_PORT=tcp://172.30.0.1:443 KUBERNETES_PORT_53_UDP_PROTO=udp KUBERNETES_PORT_53_UDP_PORT=53 ROUTER_SERVICE_PORT_443_TCP=443 ROUTER_PORT_80_TCP=tcp://172.30.231.206:80 ROUTER_PORT=tcp://172.30.231.206:80 DOCKER_REGISTRY_SERVICE_HOST=172.30.31.49 DOCKER_REGISTRY_SERVICE_PORT_5000_TCP=5000 DOCKER_REGISTRY_PORT=tcp://172.30.31.49:5000 KUBERNETES_SERVICE_PORT_DNS_TCP=53 ROUTER_SERVICE_HOST=172.30.231.206 ROUTER_SERVICE_PORT_80_TCP=80 KUBERNETES_PORT_443_TCP=tcp://172.30.0.1:443 KUBERNETES_PORT_443_TCP_ADDR=172.30.0.1 KUBERNETES_PORT_53_UDP=udp://172.30.0.1:53 KUBERNETES_PORT_53_UDP_ADDR=172.30.0.1 ROUTER_PORT_443_TCP_PORT=443 DOCKER_REGISTRY_PORT_5000_TCP=tcp://172.30.31.49:5000 PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin HOME=/root OPENSHIFT_CONTAINERIZED=true KUBECONFIG=/var/lib/origin/openshift.local.config/master/admin.kubeconfig], Image=openshift/origin-deployer:v1.1.6, WorkingDir=/var/lib/origin, Entrypoint={parts:[/usr/bin/openshift-deploy]}, NetworkDisabled=false, Labels=map[io.kubernetes.pod.name:docker-registry-1-deploy io.kubernetes.pod.namespace:default io.kubernetes.pod.terminationGracePeriod:30 io.kubernetes.pod.uid:ecc49787-07bd-11e6-bcd3-525400b263eb io.kubernetes.container.hash:d472c43f license:GPLv2 name:CentOS Base Image io.kubernetes.container.name:deployment io.kubernetes.container.restartCount:0 io.kubernetes.container.terminationMessagePath:/dev/termination-log build-date:2016-03-31 vendor:CentOS]}, HostConfig={Binds=[/var/lib/origin/openshift.local.volumes/pods/ecc49787-07bd-11e6-bcd3-525400b263eb/volumes/kubernetes.io~secret/deployer-token-a72ry:/var/run/secrets/kubernetes.io/serviceaccount:ro,Z /var/lib/origin/openshift.local.volumes/pods/ecc49787-07bd-11e6-bcd3-525400b263eb/etc-hosts:/etc/hosts /var/lib/origin/openshift.local.volumes/pods/ecc49787-07bd-11e6-bcd3-525400b263eb/containers/deployment/1fd9f8ad:/dev/termination-log], MemorySwap=-1, CPUShares=2, OomKillDisable=false, Privileged=false, PublishAllPorts=false, NetworkMode=container:85125e8cd1d8057e23eecb3794212d621d2775f7839396070264510220f6034b, IpcMode=container:85125e8cd1d8057e23eecb3794212d621d2775f7839396070264510220f6034b, CapDrop={parts:[KILL MKNOD SETGID SETUID SYS_CHROOT]}, GroupAdd=[1000010000], SecurityOpt=[label:level:s0:c3,c2], ReadonlyRootfs=false, LogConfig={Type:json-file Config:map[]}, ShmSize=67108864}}"
Apr 21 14:40:36 ose3-node2.example.com forward-journal[16175]: 2016/04/21 14:40:36 http: multiple response.WriteHeader calls
Apr 21 14:40:38 ose3-node2.example.com forward-journal[16175]: time="2016-04-21T14:40:38.924199087+02:00" level=info msg="{Action=stop, ID=85125e8cd1d8057e23eecb3794212d621d2775f7839396070264510220f6034b, LoginUID=4294967295, PID=15997}"
[vagrant@ose3-node2 ~]$ 

## TeardownNetworkError

https://bugzilla.redhat.com/show_bug.cgi?id=1322077











