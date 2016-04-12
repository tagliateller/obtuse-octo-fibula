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

OK, ggf. hier schauen: 

https://github.com/openshift/training/blob/master/02-Installation-and-Scheduler.md#node-labels


