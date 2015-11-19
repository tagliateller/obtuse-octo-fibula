
https://github.com/kubernetes/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md


```console
git clone https://github.com/kubernetes/kubernetes
cd kubernetes/docs/getting-started-guides/coreos/azure/
```

```console
npm install
```

Bedingt, möglicherweise ist das nicht das Problem:

Azure-cli sollte 0.9.12 sein, sonst:

```console
sudo npm update -g azure-cli
```

Aber:

```console
azure config mode asm
```

Es gibt nicht alle Locations im ASM-Mode

```console
export AZ_LOCATION="Central US"
```

Start der Clusterzeugung

```console
./create-kubernetes-cluster.js
```

Ergebnis OK:

```console
azure_wrapper/info: Saved SSH config, you can use it like so: `ssh -F  ./output/kube_5a347d13b42268_ssh_conf <hostname>`
azure_wrapper/info: The hosts in this deployment are:
 [ 'etcd-00', 'etcd-01', 'etcd-02', 'kube-00', 'kube-01', 'kube-02' ]
azure_wrapper/info: Saved state into `./output/kube_5a347d13b42268_deployment.yml`
[ec2-user@ip-172-31-11-251 azure]$
```

[ec2-user@ip-172-31-11-251 azure]$ ssh -F output/kube_5a347d13b42268_ssh_conf kube-00
CoreOS stable (766.4.0)
Update Strategy: No Reboots
core@kube-00 ~ $

core@kube-00 ~ $ kubectl get nodes
NAME      LABELS                           STATUS
kube-01   kubernetes.io/hostname=kube-01   Ready
kube-02   kubernetes.io/hostname=kube-02   Ready
core@kube-00 ~ $

core@kube-00 ~ $ kubectl create -f ~/guestbook-example
replicationcontrollers/frontend
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:32760) to serve traffic.

See http://releases.k8s.io/HEAD/docs/user-guide/services-firewalls.md for more details.
services/frontend
replicationcontrollers/redis-master
services/redis-master
replicationcontrollers/redis-slave
services/redis-slave
core@kube-00 ~ $

core@kube-00 ~ $ kubectl get pods --watch
NAME                 READY     STATUS    RESTARTS   AGE
frontend-7x59m       0/1       Pending   0          49s
frontend-e42wf       0/1       Pending   0          49s
frontend-preko       0/1       Pending   0          49s
redis-master-rlxk4   0/1       Pending   0          42s
redis-slave-3xvw4    0/1       Pending   0          41s
redis-slave-f37o6    0/1       Pending   0          41s

core@kube-00 ~ $ kubectl get pods
NAME                 READY     STATUS    RESTARTS   AGE
frontend-7x59m       1/1       Running   0          9m
frontend-e42wf       1/1       Running   0          9m
frontend-preko       1/1       Running   0          9m
redis-master-rlxk4   1/1       Running   0          9m
redis-slave-3xvw4    1/1       Running   0          9m
redis-slave-f37o6    1/1       Running   0          9m
core@kube-00 ~ $

Skalierung des Clusters - in einem 2. Terminal, wechseln in das kubernetes verzeichnis

export AZ_VM_SIZE=Large

azure login

 ./scale-kubernetes-cluster.js ./output/kube_5a347d13b42268_deployment.yml 2
 
 
azure_wrapper/info: The hosts in this deployment are:
 [ 'etcd-00',-cert=./output/kube_5a347d13b42268_ssh.pem',
  'etcd-01',-name=kube-04',
  'etcd-02',h=2208',
  'kube-00',stom-data=./output/kubernetes-cluster-main-nodes_688ef1e11c763b_generated.yml',
  'kube-01',1e93f07c4903bcad35bda10acf22__CoreOS-Stable-766.4.0',
  'kube-02',' ] ],
  'kube-03',
  'kube-04' ]/task: { todo:
azure_wrapper/info: Saved state into `./output/kube_688ef1e11c763b_deployment.yml`
[ec2-user@ip-172-31-11-251 azure]$ #

Zurück zum Master

core@kube-00 ~ $ kubectl get nodes
NAME      LABELS                           STATUS
kube-01   kubernetes.io/hostname=kube-01   Ready
kube-02   kubernetes.io/hostname=kube-02   Ready
kube-03   kubernetes.io/hostname=kube-03   Ready
kube-04   kubernetes.io/hostname=kube-04   Ready
core@kube-00 ~ $

Prüfen der Skalierung der Applikation


core@kube-00 ~ $ kubectl get rc
CONTROLLER     CONTAINER(S)   IMAGE(S)                                    SELECTOR            REPLICAS
frontend       php-redis      kubernetes/example-guestbook-php-redis:v2   name=frontend       3
redis-master   master         redis                                       name=redis-master   1
redis-slave    worker         kubernetes/redis-slave:v2                   name=redis-slave    2
core@kube-00 ~ $

core@kube-00 ~ $ kubectl scale --replicas=4 rc redis-slave
scaled
core@kube-00 ~ $ kubectl get rc
CONTROLLER     CONTAINER(S)   IMAGE(S)                                    SELECTOR            REPLICAS
frontend       php-redis      kubernetes/example-guestbook-php-redis:v2   name=frontend       3
redis-master   master         redis                                       name=redis-master   1
redis-slave    worker         kubernetes/redis-slave:v2                   name=redis-slave    4
core@kube-00 ~ $

Noch skaliert er nicht auf alle Nodes

core@kube-00 ~ $ kubectl get pods -l name=frontend
NAME             READY     STATUS    RESTARTS   AGE
frontend-7x59m   1/1       Running   0          1h
frontend-e42wf   1/1       Running   0          1h
frontend-preko   1/1       Running   0          1h
core@kube-00 ~ $

...

ok ... bleibt dabei. Dann lassen wir es mal dabei und sehen es als erfolgreich an

Expose Endpoint

[ec2-user@ip-172-31-11-251 azure]$ ./expose_guestbook_app_port.sh output/kube_5a347d13b42268_ssh_conf
Guestbook app is on port 32760, will map it to port 80 on kube-00
info:    Executing command vm endpoint create
+ Getting virtual machines
+ Reading network configuration
+ Updating network configuration
info:    vm endpoint create command OK
info:    Executing command vm endpoint show
+ Getting virtual machines
data:      Name                          : tcp-80-32760
data:      Local port                    : 32760
data:      Protcol                       : tcp
data:      Virtual IP Address            : 104.43.196.27
data:      Direct server return          : Disabled
info:    vm endpoint show command OK
[ec2-user@ip-172-31-11-251 azure]$

Tatsache: http://kube-5a347d13b42268.cloudapp.net/ zeigt das guestbook

Abriss des Clusters

[ec2-user@ip-172-31-11-251 azure]$ ./destroy-cluster.js output/kube_5a347d13b42268_deployment.yml

# Tests mit Apps
https://github.com/kubernetes/kubernetes/tree/master/examples

# Tests mit Wildfly + DB lt. Arun Gupta
http://blog.arungupta.me/mysql-kubernetes-service-access-wildfly-pod-techtip72/


