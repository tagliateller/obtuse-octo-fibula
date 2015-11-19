
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


