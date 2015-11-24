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

```
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
```


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
```
[azureuser@demov3-master ~]$ sudo iptables -D OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT
iptables: Bad rule (does a matching rule exist in that chain?).
[azureuser@demov3-master ~]$ sudo iptables -D OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 8444 -j ACCEPT
iptables: Bad rule (does a matching rule exist in that chain?).
[azureuser@demov3-master ~]$ sudo iptables -D OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 7001 -j ACCEPT
iptables: Bad rule (does a matching rule exist in that chain?).
[azureuser@demov3-master ~]$
```
TASK: [openshift_master | Create the scheduler config] ************************ 

./roles/openshift_master/templates/scheduler.json.j2
```
{
  "kind": "Policy",
  "apiVersion": "v1",
  "predicates": [
    {"name": "MatchNodeSelector"},
    {"name": "PodFitsResources"},
    {"name": "PodFitsPorts"},
    {"name": "NoDiskConflict"},
    {"name": "Region", "argument": {"serviceAffinity" : {"labels" : ["region"]}}}
  ],"priorities": [
    {"name": "LeastRequestedPriority", "weight": 1},
    {"name": "ServiceSpreadingPriority", "weight": 1},
    {"name": "Zone", "weight" : 2, "argument": {"serviceAntiAffinity" : {"label": "zone"}}}
  ]
}
```
sudo vi /etc/origin/master/scheduler.json
sudo chmod 664 /etc/origin/master/scheduler.json

TASK: [openshift_master | Install httpd-tools if needed] ********************** 

sudo yum -y install httpd-tools

TASK: [openshift_master | Ensure htpasswd directory exists] ******************* 

TASK: [openshift_master | Create the htpasswd file if needed] ***************** 

/etc/origin/origin-passwd

sudo htpasswd -c /etc/origin/origin-passwd john # pw: smith

TASK: [openshift_master | Create master config] ******************************* 

TODO: Variablen ermitteln, KonfigDatei so darstellen, dass es auch bei anderen Deployments passt - ODER schneller: 
Beispiel von Single-Installation holen ??

```
apiLevels:
{% if openshift.common.deployment_type == "enterprise" %}
- v1beta3
{% endif %}
- v1
apiVersion: v1
assetConfig:
  logoutURL: ""
  masterPublicURL: {{ openshift.master.public_api_url }}
  publicURL: {{ openshift.master.public_console_url }}/
  servingInfo:
    bindAddress: {{ openshift.master.bind_addr }}:{{ openshift.master.console_port }}
    certFile: master.server.crt
    clientCA: ""
    keyFile: master.server.key
    maxRequestsInFlight: 0
    requestTimeoutSeconds: 0
corsAllowedOrigins:
{% for origin in ['127.0.0.1', 'localhost', openshift.common.hostname, openshift.common.ip, openshift.common.public_hostname, openshift.common.public_ip] %}
  - {{ origin }}
{% endfor %}
{% for custom_origin in openshift.master.custom_cors_origins | default("") %}
  - {{ custom_origin }}
{% endfor %}
{% if openshift.master.embedded_dns | bool %}
dnsConfig:
  bindAddress: {{ openshift.master.bind_addr }}:{{ openshift.master.dns_port }}
{% endif %}
etcdClientInfo:
  ca: {{ "ca.crt" if (openshift.master.embedded_etcd | bool) else "master.etcd-ca.crt" }}
  certFile: master.etcd-client.crt
  keyFile: master.etcd-client.key
  urls:
{% for etcd_url in openshift.master.etcd_urls %}
    - {{ etcd_url }}
{% endfor %}
{% if openshift.master.embedded_etcd | bool %}
etcdConfig:
  address: {{ openshift.common.hostname }}:{{ openshift.master.etcd_port }}
  peerAddress: {{ openshift.common.hostname }}:7001
  peerServingInfo:
    bindAddress: {{ openshift.master.bind_addr }}:7001
    certFile: etcd.server.crt
    clientCA: ca.crt
    keyFile: etcd.server.key
  servingInfo:
    bindAddress: {{ openshift.master.bind_addr }}:{{ openshift.master.etcd_port }}
    certFile: etcd.server.crt
    clientCA: ca.crt
    keyFile: etcd.server.key
  storageDirectory: {{ openshift.common.data_dir }}/openshift.local.etcd
{% endif %}
etcdStorageConfig:
  kubernetesStoragePrefix: kubernetes.io
  kubernetesStorageVersion: v1
  openShiftStoragePrefix: openshift.io
  openShiftStorageVersion: v1
imageConfig:
  format: {{ openshift.master.registry_url }}
  latest: false
kind: MasterConfig
kubeletClientInfo:
{# TODO: allow user specified kubelet port #}
  ca: ca.crt
  certFile: master.kubelet-client.crt
  keyFile: master.kubelet-client.key
  port: 10250
{% if openshift.master.embedded_kube | bool %}
kubernetesMasterConfig:
  apiLevels:
{% if openshift.common.deployment_type == "enterprise" %}
  - v1beta3
{% endif %}
  - v1
  apiServerArguments: {{ api_server_args if api_server_args is defined else 'null' }}
  controllerArguments: {{ controller_args if controller_args is defined else 'null' }}
{# TODO: support overriding masterCount #}
  masterCount: 1
  masterIP: ""
  podEvictionTimeout: ""
  proxyClientInfo:
    certFile: master.proxy-client.crt
    keyFile: master.proxy-client.key
  schedulerConfigFile: {{ openshift_master_scheduler_conf }}
  servicesNodePortRange: ""
  servicesSubnet: {{ openshift.master.portal_net }}
  staticNodeNames: {{ openshift_node_ips | default([], true) }}
{% endif %}
masterClients:
{# TODO: allow user to set externalKubernetesKubeConfig #}
  externalKubernetesKubeConfig: ""
  openshiftLoopbackKubeConfig: openshift-master.kubeconfig
masterPublicURL: {{ openshift.master.public_api_url }}
networkConfig:
  clusterNetworkCIDR: {{ openshift.master.sdn_cluster_network_cidr }}
  hostSubnetLength: {{ openshift.master.sdn_host_subnet_length }}
{% if openshift.common.use_openshift_sdn %}
  networkPluginName: {{ openshift.common.sdn_network_plugin_name }}
{% endif %}
# serviceNetworkCIDR must match kubernetesMasterConfig.servicesSubnet
  serviceNetworkCIDR: {{ openshift.master.portal_net }}
{% include 'v1_partials/oauthConfig.j2' %}
policyConfig:
  bootstrapPolicyFile: {{ openshift_master_policy }}
  openshiftInfrastructureNamespace: openshift-infra
  openshiftSharedResourcesNamespace: openshift
projectConfig:
  defaultNodeSelector: "{{ openshift.master.default_node_selector }}"
  projectRequestMessage: "{{ openshift.master.project_request_message }}"
  projectRequestTemplate: "{{ openshift.master.project_request_template }}"
  securityAllocator:
    mcsAllocatorRange: "{{ openshift.master.mcs_allocator_range }}"
    mcsLabelsPerProject: {{ openshift.master.mcs_labels_per_project }}
    uidAllocatorRange: "{{ openshift.master.uid_allocator_range  }}"
routingConfig:
  subdomain:  "{{ openshift.master.default_subdomain | default("") }}"
serviceAccountConfig:
  managedNames:
  - default
  - builder
  - deployer
  masterCA: ca.crt
  privateKeyFile: serviceaccounts.private.key
  publicKeyFiles:
  - serviceaccounts.public.key
servingInfo:
  bindAddress: {{ openshift.master.bind_addr }}:{{ openshift.master.api_port }}
  certFile: master.server.crt
  clientCA: ca.crt
  keyFile: master.server.key
  maxRequestsInFlight: 500
  requestTimeoutSeconds: 3600
```









 


# demov3-master

## iptables

[centos@ip-172-31-28-12 ~]$ sudo iptables --list-rules
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-N DOCKER
-N OS_FIREWALL_ALLOW
-A INPUT -i tun0 -m comment --comment "traffic from docker for internet" -j ACCEPT
-A INPUT -p udp -m multiport --dports 4789 -m comment --comment "001 vxlan incoming" -j ACCEPT
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -j OS_FIREWALL_ALLOW
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -o lbr0 -j DOCKER
-A FORWARD -o lbr0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i lbr0 ! -o lbr0 -j ACCEPT
-A FORWARD -i lbr0 -o lbr0 -j ACCEPT
-A FORWARD -s 10.1.0.0/16 -j ACCEPT
-A FORWARD -d 10.1.0.0/16 -j ACCEPT
-A FORWARD -o docker0 -j DOCKER
-A FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i docker0 ! -o docker0 -j ACCEPT
-A FORWARD -i docker0 -o docker0 -j ACCEPT
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 4001 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 8443 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 53 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 53 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 24224 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 24224 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 2224 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 5404 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 5405 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 10250 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 10255 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 10255 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 4789 -j ACCEPT
[centos@ip-172-31-28-12 ~]$ 


## node-compute-77f18

[centos@ip-172-31-31-118 ~]$ sudo iptables --list-rules
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-N DOCKER
-N OS_FIREWALL_ALLOW
-A INPUT -i tun0 -m comment --comment "traffic from docker for internet" -j ACCEPT
-A INPUT -p udp -m multiport --dports 4789 -m comment --comment "001 vxlan incoming" -j ACCEPT
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -j OS_FIREWALL_ALLOW
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -s 10.1.0.0/16 -j ACCEPT
-A FORWARD -d 10.1.0.0/16 -j ACCEPT
-A FORWARD -o lbr0 -j DOCKER
-A FORWARD -o lbr0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i lbr0 ! -o lbr0 -j ACCEPT
-A FORWARD -i lbr0 -o lbr0 -j ACCEPT
-A FORWARD -o docker0 -j DOCKER
-A FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i docker0 ! -o docker0 -j ACCEPT
-A FORWARD -i docker0 -o docker0 -j ACCEPT
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 10250 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 10255 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 10255 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 4789 -j ACCEPT
[centos@ip-172-31-31-118 ~]$ 


## demov3-node-infra-fb30f

[centos@ip-172-31-25-64 ~]$ sudo iptables --list-rules
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-N DOCKER
-N OS_FIREWALL_ALLOW
-A INPUT -i tun0 -m comment --comment "traffic from docker for internet" -j ACCEPT
-A INPUT -p udp -m multiport --dports 4789 -m comment --comment "001 vxlan incoming" -j ACCEPT
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -j OS_FIREWALL_ALLOW
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -o lbr0 -j DOCKER
-A FORWARD -o lbr0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i lbr0 ! -o lbr0 -j ACCEPT
-A FORWARD -i lbr0 -o lbr0 -j ACCEPT
-A FORWARD -s 10.1.0.0/16 -j ACCEPT
-A FORWARD -d 10.1.0.0/16 -j ACCEPT
-A FORWARD -o docker0 -j DOCKER
-A FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i docker0 ! -o docker0 -j ACCEPT
-A FORWARD -i docker0 -o docker0 -j ACCEPT
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 10250 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 10255 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 10255 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 4789 -j ACCEPT
[centos@ip-172-31-25-64 ~]$ 


## node-compute-1c585

[centos@ip-172-31-31-119 ~]$ sudo iptables --list-rules
-P INPUT ACCEPT
-P FORWARD ACCEPT
-P OUTPUT ACCEPT
-N DOCKER
-N OS_FIREWALL_ALLOW
-A INPUT -i tun0 -m comment --comment "traffic from docker for internet" -j ACCEPT
-A INPUT -p udp -m multiport --dports 4789 -m comment --comment "001 vxlan incoming" -j ACCEPT
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -j OS_FIREWALL_ALLOW
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -s 10.1.0.0/16 -j ACCEPT
-A FORWARD -d 10.1.0.0/16 -j ACCEPT
-A FORWARD -o lbr0 -j DOCKER
-A FORWARD -o lbr0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i lbr0 ! -o lbr0 -j ACCEPT
-A FORWARD -i lbr0 -o lbr0 -j ACCEPT
-A FORWARD -o docker0 -j DOCKER
-A FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i docker0 ! -o docker0 -j ACCEPT
-A FORWARD -i docker0 -o docker0 -j ACCEPT
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 10250 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
-A OS_FIREWALL_ALLOW -p tcp -m state --state NEW -m tcp --dport 10255 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 10255 -j ACCEPT
-A OS_FIREWALL_ALLOW -p udp -m state --state NEW -m udp --dport 4789 -j ACCEPT
[centos@ip-172-31-31-119 ~]$ 




