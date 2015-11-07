# Installation einer Single VM mit OpenShift (Executable)

$ azure group create -n demov3group -l "East US"

single-linux-machine]$ azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g demov3group -n demov3deployment

ssh -i ~/azure-key-pair azureuser@demov3.eastus.cloudapp.azure.com

```
sudo yum -y update
sudo yum -y install docker
sudo yum -y install git
sudo systemctl start docker
```

Download

```
[azureuser@demov3 ~]$ curl -L -O https://github.com/openshift/origin/releases/download/v1.0.7/openshift-origin-v1.0.7-67bb208-linux-amd64.tar.gz
```

Entpacken

```
[azureuser@demov3 ~]$ mkdir origin-1.0.7
[azureuser@demov3 ~]$ cd origin-1.0.7/
$ tar zxvf ../openshift-origin-v1.0.7-67bb208-linux-amd64.tar.gz

./
./kube-proxy
./oadm
./kubectl
./kube-controller-manager
./kube-scheduler
./oc
./openshift
./kube-apiserver
./kubelet
```

Export in Path und Start OpenShift Origin

```
[azureuser@demov3 origin-1.0.7]$ export PATH=$(pwd):$PATH
[azureuser@demov3 origin-1.0.7]$ sudo ./openshift start
```

Einrichtung Registry

```
sudo ./oadm registry --config=openshift.local.config/master/admin.kubeconfig --credentials=openshift.local.config/master/openshift-registry.kubeconfig 
```

Laden der Examples

```
sudo yum -y install unzip
unzip examples.zip (git openshift-ansible)
```

geht nicht, da oauth auf 10.0.0.11 umgeleitet wird. auch die nachträgliche korrektur der url hilft nicht.
https://demov3.eastus.cloudapp.azure.com:8443/console/

[azureuser@demov3 ~]$ origin-1.0.7/oc login

[azureuser@demov3 ~]$ origin-1.0.7/oc new-project smoke-project

origin-1.0.7/oc new-app https://github.com/gshipley/smoke.git
--> geht, es ist aber keine registry vorhanden

## Anleitung nach https://docs.openshift.org/latest/getting_started/administrators.html#try-it-out

[azureuser@demov3 ~]$ origin-1.0.7/oc new-app openshift/deployment-example --> geht

--> aber Fehler in Deployment

ok, 2. Versuch: Deployment ok (wie Tutorial), weiter ...
#

[azureuser@demov3 origin]$ curl http://172.30.84.60:8080
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Deployment Demonstration</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    HTML{height:100%;}
    BODY{font-family:Helvetica,Arial;display:flex;display:-webkit-flex;align-items:center;justify-content:center;-webkit-align-items:center;-webkit-box-align:center;-webkit-justify-content:center;height:100%;}
    .box{background:#006e9c;color:white;text-align:center;border-radius:10px;display:inline-block;}
    H1{font-size:10em;line-height:1.5em;margin:0 0.5em;}
    H2{margin-top:0;}
  </style>
</head>
<body>
<div class="box"><h1>v1</h1><h2></h2></div>
</body>
</html>[azureuser@demov3 origin]$ 


 
--> es fehlen die Templates ... oder ?


