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

sudo oadm ca create-master-certs --hostnames=40.114.4.137,10.0.0.11 --master=https://10.0.0.11:8443 --public-master=https://40.114.4.137:8443 --cert-dir=/etc/origin/master --overwrite=false 

Ausgabe:

Generated new key pair as /etc/origin/master/serviceaccounts.public.key and /etc/origin/master/serviceaccounts.private.key

TASK: [os_firewall | Install iptables packages] *******************************

sudo yum -y install iptables 
--> ist bereits installiert !!
sudo yum -y install iptables-services
--> wird installiert
rpm -q firewalld 
--> ist bereits installiert

TASK: [os_firewall | Ensure firewalld service is not enabled] ***************** 

sudo systemctl stop firewalld 
sudo systemctl status firewalld  
--> loaded/disabled

TASK: [os_firewall | Start and enable iptables service] *********************** 

sudo systemctl start iptables

TASK: [os_firewall | Mask firewalld service] **********************************
sudo systemctl mask firewalld

TASK: [os_firewall | Add iptables allow rules] ******************************** 

- name: Add iptables allow rules
  os_firewall_manage_iptables:
    name: "{{ item.service }}"
    action: add
    protocol: "{{ item.port.split('/')[1] }}"
    port: "{{ item.port.split('/')[0] }}"
  with_items: os_firewall_allow
  when: os_firewall_allow is defined

# TODO: update setting these values based on the facts
os_firewall_allow:
- service: etcd embedded
  port: 4001/tcp
- service: api server https
  port: 8443/tcp
- service: dns tcp
  port: 53/tcp
- service: dns udp
  port: 53/udp
- service: Fluentd td-agent tcp
  port: 24224/tcp
- service: Fluentd td-agent udp
  port: 24224/udp
- service: pcsd
  port: 2224/tcp
- service: Corosync UDP
  port: 5404/udp
- service: Corosync UDP
  port: 5405/udp

changed: [demov3-master-9986c] => (item={'port': '4001/tcp', 'service': 'etcd embedded'}) => {"changed": true, "item": {"port": "4001/tcp", "service": "etcd embedded"}, "output": ["", "Successfully created chain OS_FIREWALL_ALLOW", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n", "", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n", "", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}

changed: [demov3-master-9986c] => (item={'port': '8443/tcp', 'service': 'api server https'}) => {"changed": true, "item": {"port": "8443/tcp", "service": "api server https"}, "output": ["", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}

changed: [demov3-master-9986c] => (item={'port': '53/tcp', 'service': 'dns tcp'}) => {"changed": true, "item": {"port": "53/tcp", "service": "dns tcp"}, "output": ["", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}

changed: [demov3-master-9986c] => (item={'port': '53/udp', 'service': 'dns udp'}) => {"changed": true, "item": {"port": "53/udp", "service": "dns udp"}, "output": ["", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}

changed: [demov3-master-9986c] => (item={'port': '24224/tcp', 'service': 'Fluentd td-agent tcp'}) => {"changed": true, "item": {"port": "24224/tcp", "service": "Fluentd td-agent tcp"}, "output": ["", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}

changed: [demov3-master-9986c] => (item={'port': '24224/udp', 'service': 'Fluentd td-agent udp'}) => {"changed": true, "item": {"port": "24224/udp", "service": "Fluentd td-agent udp"}, "output": ["", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}

changed: [demov3-master-9986c] => (item={'port': '2224/tcp', 'service': 'pcsd'}) => {"changed": true, "item": {"port": "2224/tcp", "service": "pcsd"}, "output": ["", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}

changed: [demov3-master-9986c] => (item={'port': '5404/udp', 'service': 'Corosync UDP'}) => {"changed": true, "item": {"port": "5404/udp", "service": "Corosync UDP"}, "output": ["", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}

changed: [demov3-master-9986c] => (item={'port': '5405/udp', 'service': 'Corosync UDP'}) => {"changed": true, "item": {"port": "5405/udp", "service": "Corosync UDP"}, "output": ["", "iptables: Saving firewall rules to /etc/sysconfig/iptables: [  OK  ]\r\n"]}


[azureuser@demov3-master ~]$ sudo iptables -N OS_FIREWALL_ALLOW
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 4001 -j ACCEPT
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 8443 -j ACCEPT
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 53 -j ACCEPT
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 53 -j ACCEPT
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 24224 -j ACCEPT
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 24224 -j ACCEPT
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 2224 -j ACCEPT
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 5404 -j ACCEPT
[azureuser@demov3-master ~]$ sudo iptables -A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 5405 -j ACCEPT



- name: Remove iptables rules
  os_firewall_manage_iptables:
    name: "{{ item.service }}"
    action: remove
    protocol: "{{ item.port.split('/')[1] }}"
    port: "{{ item.port.split('/')[0] }}"
  with_items: os_firewall_deny
  when: os_firewall_deny is defined

os_firewall_deny:
- service: api server http
  port: 8080/tcp
- service: former web console port
  port: 8444/tcp
- service: former etcd peer port
  port: 7001/tcp

TASK: [os_firewall | Remove iptables rules] *********************************** 
ok: [demov3-master-9986c] => (item={'port': '8080/tcp', 'service': 'api server http'}) => {"changed": false, "item": {"port": "8080/tcp", "service": "api server http"}, "output": []}
ok: [demov3-master-9986c] => (item={'port': '8444/tcp', 'service': 'former web console port'}) => {"changed": false, "item": {"port": "8444/tcp", "service": "former web console port"}, "output": []}
ok: [demov3-master-9986c] => (item={'port': '7001/tcp', 'service': 'former etcd peer port'}) => {"changed": false, "item": {"port": "7001/tcp", "service": "former etcd peer port"}, "output": []}

[azureuser@demov3-master ~]$ sudo iptables -D OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT
iptables: Bad rule (does a matching rule exist in that chain?).
[azureuser@demov3-master ~]$ sudo iptables -D OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 8444 -j ACCEPT
iptables: Bad rule (does a matching rule exist in that chain?).
[azureuser@demov3-master ~]$ sudo iptables -D OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 7001 -j ACCEPT
iptables: Bad rule (does a matching rule exist in that chain?).
[azureuser@demov3-master ~]$







 




