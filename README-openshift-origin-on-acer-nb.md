* Ausgangslage: vagrant + virtualbox unter windows installiert, ansible ist auf einer centos vm installiert
* Vagrantfiles in workspace/boxes/origin/[master,node1,node2] anlegen. komlettes vagrantfile geht nicht wg. rsync-Fehler
* IP .100, .201, .202. SSH-Port wird durch vagrant selbst bestimmt
* VM einzeln starten

* Inventory anpassen

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
master ansible_ssh_host=192.168.100.100 ansible_ssh_port=2200

# host group for nodes, includes region info
[nodes]
node1 ansible_ssh_host=192.168.100.201 ansible_ssh_port=2201 openshift_node_labels="{'region': 'primary', 'zone': 'east'}"
node2 ansible_ssh_host=192.168.100.202 ansible_ssh_port=2202 openshift_node_labels="{'region': 'primary', 'zone': 'west'}"

~

```


