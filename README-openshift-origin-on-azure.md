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

## Versuche mit centos

TASK: [docker | enable and start the docker service] ************************** 
failed: [demov3-node2.eastus.cloudapp.azure.com] => {"failed": true}
msg: Job for docker.service failed. See 'systemctl status docker.service' and 'journalctl -xn' for details.

failed: [demov3-node1.eastus.cloudapp.azure.com] => {"failed": true}
msg: Job for docker.service failed. See 'systemctl status docker.service' and 'journalctl -xn' for details.


FATAL: all hosts have already failed -- aborting

PLAY RECAP ******************************************************************** 
           to retry, use: --limit @/home/ec2-user/config.retry

demov3-master.cloudapp.azure.com : ok=0    changed=0    unreachable=1    failed=0   
demov3-master.eastus.cloudapp.azure.com : ok=155  changed=43   unreachable=0    failed=0   
demov3-node1.eastus.cloudapp.azure.com : ok=40   changed=11   unreachable=0    failed=1   
demov3-node2.eastus.cloudapp.azure.com : ok=40   changed=11   unreachable=0    failed=1   
localhost                  : ok=12   changed=0    unreachable=0    failed=0   

ausgabe systemctl

[ec2-user@ip-172-31-53-132 openshift-ansible]$ ssh -i ~/azure-key-pair azureuser@demov3-node1.eastus.cloudapp.azure.com
Last login: Sun Dec  6 11:38:07 2015 from ec2-52-90-176-9.compute-1.amazonaws.com
[azureuser@demov3-node1 ~]$ sudo systemctl status docker.service
docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled)
   Active: failed (Result: timeout) since So 2015-12-06 11:39:16 UTC; 12min ago
     Docs: http://docs.docker.com
 Main PID: 3939

Dez 06 11:38:08 demov3-node1 systemd[1]: Starting Docker Application Container Engine...
Dez 06 11:38:09 demov3-node1 docker[3939]: time="2015-12-06T11:38:09.059898659Z" level=info msg="Listening for HTTP on unix (/var/run/docker.sock)"
Dez 06 11:38:10 demov3-node1 docker[3939]: time="2015-12-06T11:38:10.895615940Z" level=error msg="WARNING: No --storage-opt dm.thinpooldev specifi...tion use"
Dez 06 11:39:08 demov3-node1 systemd[1]: docker.service operation timed out. Terminating.
Dez 06 11:39:16 demov3-node1 systemd[1]: Failed to start Docker Application Container Engine.
Dez 06 11:39:16 demov3-node1 systemd[1]: Unit docker.service entered failed state.
Hint: Some lines were ellipsized, use -l to show in full.
[azureuser@demov3-node1 ~]$ 

Versuche docker.service zu starten

Resultat: Timout, ggf. größere Maschine nutzen ?

