
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


