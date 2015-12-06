azure-create.inventory:

```txt
localhost
ansible_connection=local
```

```console
ansible-playbook -i azure-create.inventory playbooks/azure/openshift-cluster-create.yml
```

```txt
# Create an OSEv3 group that contains the masters and nodes groups
[OSEv3:children]
masters
nodes

# Set variables common for all OSEv3 hosts
[OSEv3:vars]
# SSH user, this user should allow ssh based auth without requiring a password
ansible_ssh_user=azureuser

# If ansible_ssh_user is not root, ansible_sudo must be set to true
ansible_sudo=true

product_type=openshift
deployment_type=origin

# uncomment the following to enable htpasswd authentication; defaults to DenyAllPasswordIdentityProvider
openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider', 'filename': '/etc/origin/origin-passwd'}]


# host group for masters
[masters]
demov3-master.eastus.cloudapp.azure.com

# host group for nodes, includes region info
[nodes]
demov3-master.cloudapp.azure.com openshift_node_labels="{'region': 'infra', 'zone': 'default'}"
demov3-node1.eastus.cloudapp.azure.com openshift_node_labels="{'region': 'primary', 'zone': 'east'}"
demov3-node2.eastus.cloudapp.azure.com openshift_node_labels="{'region': 'primary', 'zone': 'west'}"
```

```console
export ANSIBLE_HOST_KEY_CHECKING=False
```

```console
[ec2-user@ip-172-31-53-132 openshift-ansible]$ eval `ssh-agent -s`
Agent pid 2515
[ec2-user@ip-172-31-53-132 openshift-ansible]$ ssh-add ~/azure-key-pair
Identity added: /home/ec2-user/azure-key-pair (/home/ec2-user/azure-key-pair)
[ec2-user@ip-172-31-53-132 openshift-ansible]$ 
```

Löschen der known-hosts Datei bei mehrmaliger Ausführung

```console
rm ~/.ssh/known_hosts
```

```console
ansible-playbook -i azure.inventory playbooks/byo/openshift-cluster/config.yml
```

Probleme bei CoreOS: kein python, 
hostname auch zu lang:

demov3-master.vi12zqpum53ulklztb1oznh1th.bx.internal.cloudapp.net

