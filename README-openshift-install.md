# Installation OpenShift - Manuell

* TASK: [openshift_repos | Ensure libselinux-python is installed] *************** 
 sudo yum list | grep libselinux-phython

* TASK: [openshift_repos | Create any additional repos that are defined] ******** 
* TASK: [openshift_repos | Remove the additional repos if no longer defined] **** 

 /etc/yum.repos.d/openshift_additional.repo
 /etc/yum.repos.d/enterprise-v3.repo
 /etc/yum.repos.d/rhel-7-libra-candidate.repo
 /etc/yum.repos.d/epel7-openshift.repo
 /etc/yum.repos.d/oso-rhui-rhel-7-server.repo
 ...
 
* TASK: [openshift_repos | Configure gpg keys if needed] ************************ 

* TASK: [openshift_repos | Configure yum repositories] ************************** 

Quelle = /home/ec2-user/openshift-ansible/playbooks/common/openshift-cluster/roles/openshift_repos/files/origin/repos/maxamillion-origin-next-epel-7.repo

Ziel   = /etc/yum.repos.d/maxamillion-origin-next-epel-7.repo

sudo vi /etc/yum.repos.d/maxamillion-origin-next-epel-7.repo
```
[maxamillion-origin-next]
name=Copr repo for origin-next owned by maxamillion
baseurl=https://copr-be.cloud.fedoraproject.org/results/maxamillion/origin-next/epel-7-$basearch/
skip_if_unavailable=True
gpgcheck=1
gpgkey=https://copr-be.cloud.fedoraproject.org/results/maxamillion/origin-next/pubkey.gpg
enabled=1
```

* TASK: [os_update_latest | Update all packages] ******************************** 
 
sudo yum -y update

* TASK: [openshift_master_ca | Install the base package for admin tooling] ****** 

sudo yum -y origin

* TASK: [openshift_master_ca | Create openshift_master_config_dir if it doesn't exist] ***

sudo mkdir -p /etc/origin/master
sudo chmod 755 /etc/origin/master

* TASK: [openshift_master_ca | Create the master certificates if they do not already exist] ***

40.114.4.137 ist die public ip
10.0.0.11 ist die private ip

[azureuser@demov3-master ~]$ sudo oadm ca create-master-certs --hostnames=40.114.4.137,10.0.0.11 --master=https://10.0.0.11:8443 --public-master=https://40.114
.4.137:8443 --cert-dir=/etc/origin/master --overwrite=false
Generated new key pair as /etc/origin/master/serviceaccounts.public.key and /etc/origin/master/serviceaccounts.private.key
[azureuser@demov3-master ~]$




 




