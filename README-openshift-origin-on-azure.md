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

## Lösung zu langer Hostname

sudo hostname -f demov3-master.internal.net

sudo vi /etc/hosts

10.0.0.4 demov3-master.internal.net
10.0.0.5 demov3-node1.internal.net
10.0.0.6 demov3-node2.internal.net

sudo rm /etc/resolv.conf

sudo systemctl stop NetworkManager
sudo systemctl restart network

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

## mit der Standard_A2 - Maschine gleiches Problem:

```console
-- The result is failed.
Dec 07 13:39:54 demov3-node1 systemd[1]: Unit docker-storage-setup.service entered failed state.
Dec 07 13:39:54 demov3-node1 systemd[1]: Starting Docker Application Container Engine...
-- Subject: Unit docker.service has begun with start-up
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit docker.service has begun starting up.
Dec 07 13:39:54 demov3-node1 docker[13328]: time="2015-12-07T13:39:54.839351600Z" level=info msg="Listening for HTTP on unix (/var/run/docker.sock)"
Dec 07 13:39:54 demov3-node1 docker[13328]: time="2015-12-07T13:39:54.840236835Z" level=error msg="WARNING: No --storage-opt dm.thinpooldev specified, using loo
Dec 07 13:40:03 demov3-node1 systemd-udevd[13336]: inotify_add_watch(7, /dev/dm-1, 10) failed: No such file or directory
Dec 07 13:40:54 demov3-node1 systemd[1]: docker.service operation timed out. Terminating.
Dec 07 13:40:58 demov3-node1 systemd[1]: Failed to start Docker Application Container Engine.
-- Subject: Unit docker.service has failed
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit docker.service has failed.
--
-- The result is failed.
Dec 07 13:40:58 demov3-node1 systemd[1]: Unit docker.service entered failed state.
Dec 07 13:43:25 demov3-node1 sudo[13354]: azureuser : TTY=pts/0 ; PWD=/home/azureuser ; USER=root ; COMMAND=/bin/journalctl -xn
lines 1-30/30 (END)
```

vi ./roles/docker/tasks/main.yml

---
# tasks file for docker
# hack wg. centos 7.1, docker-version 1.8.x startet dort nicht
- name: Install docker
  action: "{{ ansible_pkg_mgr }} name=docker-1.7.1 state=present"

- name: enable and start the docker service
  service: name=docker enabled=yes state=started

- include: udev_workaround.yml
  when: docker_udev_workaround | default(False)
~                                                                                                                                                             
~                                        

```
GATHERING FACTS *************************************************************** 
ok: [demov3-master.eastus.cloudapp.azure.com]

TASK: [openshift_manage_node | Wait for Node Registration] ******************** 
failed: [demov3-master.eastus.cloudapp.azure.com] => (item=demov3-node1.yjsnnmbktbxehpv2pm2xnqhtyb.bx.internal.cloudapp.net) => {"attempts": 20, "changed": true, "cmd": ["oc", "get", "node", "demov3-node1.yjsnnmbktbxehpv2pm2xnqhtyb.bx.internal.cloudapp.net"], "delta": "0:00:00.482568", "end": "2015-12-10 20:27:04.107312", "failed": true, "item": "demov3-node1.yjsnnmbktbxehpv2pm2xnqhtyb.bx.internal.cloudapp.net", "rc": 1, "start": "2015-12-10 20:27:03.624744", "warnings": []}
stderr: Error from server: node "demov3-node1.yjsnnmbktbxehpv2pm2xnqhtyb.bx.internal.cloudapp.net" not found
msg: Task failed as maximum retries was encountered
failed: [demov3-master.eastus.cloudapp.azure.com] => (item=demov3-node2.yjsnnmbktbxehpv2pm2xnqhtyb.bx.internal.cloudapp.net) => {"attempts": 20, "changed": true, "cmd": ["oc", "get", "node", "demov3-node2.yjsnnmbktbxehpv2pm2xnqhtyb.bx.internal.cloudapp.net"], "delta": "0:00:00.497794", "end": "2015-12-10 20:29:32.031386", "failed": true, "item": "demov3-node2.yjsnnmbktbxehpv2pm2xnqhtyb.bx.internal.cloudapp.net", "rc": 1, "start": "2015-12-10 20:29:31.533592", "warnings": []}
stderr: Error from server: node "demov3-node2.yjsnnmbktbxehpv2pm2xnqhtyb.bx.internal.cloudapp.net" not found
msg: Task failed as maximum retries was encountered

FATAL: all hosts have already failed -- aborting

PLAY RECAP ******************************************************************** 
           to retry, use: --limit @/home/ec2-user/config.retry

demov3-master.cloudapp.azure.com : ok=0    changed=0    unreachable=1    failed=0   
demov3-master.eastus.cloudapp.azure.com : ok=160  changed=42   unreachable=0    failed=1   
demov3-node1.eastus.cloudapp.azure.com : ok=63   changed=25   unreachable=0    failed=0   
demov3-node2.eastus.cloudapp.azure.com : ok=63   changed=25   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   
```

Der Hack geht jetzt auch nicht mehr, ggf. weil die repo die Docker-Version nicht mehr erhalten ? TODO: Docker vorab installieren, dann ansible laufen lassen.

## Test AWS (CentOS 7.0)

[centos@ip-172-31-30-37 ~]$ sudo yum -y install docker
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.symnds.com
 * extras: mirror.trouble-free.net
 * updates: mirror.vcu.edu
Abhängigkeiten werden aufgelöst
--> Transaktionsprüfung wird ausgeführt
---> Paket docker.x86_64 0:1.8.2-10.el7.centos markiert, um installiert zu werden
--> Abhängigkeit lvm2 >= 2.02.112 wird für Paket docker-1.8.2-10.el7.centos.x86_64 verarbeitet
--> Abhängigkeit docker-selinux >= 1.8.2-10.el7.centos wird für Paket docker-1.8.2-10.el7.centos.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
---> Paket docker-selinux.x86_64 0:1.8.2-10.el7.centos markiert, um installiert zu werden
---> Paket lvm2.x86_64 7:2.02.130-5.el7 markiert, um installiert zu werden
--> Abhängigkeit lvm2-libs = 7:2.02.130-5.el7 wird für Paket 7:lvm2-2.02.130-5.el7.x86_64 verarbeitet
--> Abhängigkeit device-mapper-persistent-data >= 0.5.5-1 wird für Paket 7:lvm2-2.02.130-5.el7.x86_64 verarbeitet
--> Abhängigkeit liblvm2app.so.2.2(Base)(64bit) wird für Paket 7:lvm2-2.02.130-5.el7.x86_64 verarbeitet
--> Abhängigkeit libdevmapper-event.so.1.02(Base)(64bit) wird für Paket 7:lvm2-2.02.130-5.el7.x86_64 verarbeitet
--> Abhängigkeit liblvm2app.so.2.2()(64bit) wird für Paket 7:lvm2-2.02.130-5.el7.x86_64 verarbeitet
--> Abhängigkeit libdevmapper-event.so.1.02()(64bit) wird für Paket 7:lvm2-2.02.130-5.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
---> Paket device-mapper-event-libs.x86_64 7:1.02.107-5.el7 markiert, um installiert zu werden
---> Paket device-mapper-persistent-data.x86_64 0:0.5.5-1.el7 markiert, um installiert zu werden
--> Abhängigkeit libaio.so.1(LIBAIO_0.4)(64bit) wird für Paket device-mapper-persistent-data-0.5.5-1.el7.x86_64 verarbeitet
--> Abhängigkeit libaio.so.1(LIBAIO_0.1)(64bit) wird für Paket device-mapper-persistent-data-0.5.5-1.el7.x86_64 verarbeitet
--> Abhängigkeit libaio.so.1()(64bit) wird für Paket device-mapper-persistent-data-0.5.5-1.el7.x86_64 verarbeitet
---> Paket lvm2-libs.x86_64 7:2.02.130-5.el7 markiert, um installiert zu werden
--> Abhängigkeit device-mapper-event = 7:1.02.107-5.el7 wird für Paket 7:lvm2-libs-2.02.130-5.el7.x86_64 verarbeitet
--> Transaktionsprüfung wird ausgeführt
---> Paket device-mapper-event.x86_64 7:1.02.107-5.el7 markiert, um installiert zu werden
---> Paket libaio.x86_64 0:0.3.109-13.el7 markiert, um installiert zu werden
--> Abhängigkeitsauflösung beendet

Abhängigkeiten aufgelöst

==============================================================================================================================================================
 Package                                            Arch                        Version                                     Paketquelle                 Größe
==============================================================================================================================================================
Installieren:
 docker                                             x86_64                      1.8.2-10.el7.centos                         extras                       10 M
Als Abhängigkeiten installiert:
 device-mapper-event                                x86_64                      7:1.02.107-5.el7                            base                        167 k
 device-mapper-event-libs                           x86_64                      7:1.02.107-5.el7                            base                        169 k
 device-mapper-persistent-data                      x86_64                      0.5.5-1.el7                                 base                        350 k
 docker-selinux                                     x86_64                      1.8.2-10.el7.centos                         extras                       63 k
 libaio                                             x86_64                      0.3.109-13.el7                              base                         24 k
 lvm2                                               x86_64                      7:2.02.130-5.el7                            base                        1.0 M
 lvm2-libs                                          x86_64                      7:2.02.130-5.el7                            base                        872 k

Transaktionsübersicht
==============================================================================================================================================================
Installieren  1 Paket (+7 Abhängige Pakete)

Gesamte Downloadgröße: 13 M
Installationsgröße: 52 M
Downloading packages:
(1/8): device-mapper-event-1.02.107-5.el7.x86_64.rpm                                                                                   | 167 kB  00:00:00     
(2/8): device-mapper-event-libs-1.02.107-5.el7.x86_64.rpm                                                                              | 169 kB  00:00:00     
(3/8): libaio-0.3.109-13.el7.x86_64.rpm                                                                                                |  24 kB  00:00:00     
(4/8): docker-selinux-1.8.2-10.el7.centos.x86_64.rpm                                                                                   |  63 kB  00:00:00     
(5/8): device-mapper-persistent-data-0.5.5-1.el7.x86_64.rpm                                                                            | 350 kB  00:00:00     
(6/8): lvm2-libs-2.02.130-5.el7.x86_64.rpm                                                                                             | 872 kB  00:00:00     
(7/8): lvm2-2.02.130-5.el7.x86_64.rpm                                                                                                  | 1.0 MB  00:00:00     
(8/8): docker-1.8.2-10.el7.centos.x86_64.rpm                                                                                           |  10 MB  00:00:01     
--------------------------------------------------------------------------------------------------------------------------------------------------------------
Gesamt                                                                                                                        5.1 MB/s |  13 MB  00:00:02     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installieren     : 7:device-mapper-event-libs-1.02.107-5.el7.x86_64                                                                                     1/8 
  Installieren     : 7:device-mapper-event-1.02.107-5.el7.x86_64                                                                                          2/8 
  Installieren     : 7:lvm2-libs-2.02.130-5.el7.x86_64                                                                                                    3/8 
  Installieren     : libaio-0.3.109-13.el7.x86_64                                                                                                         4/8 
  Installieren     : device-mapper-persistent-data-0.5.5-1.el7.x86_64                                                                                     5/8 
  Installieren     : 7:lvm2-2.02.130-5.el7.x86_64                                                                                                         6/8 
Created symlink from /etc/systemd/system/sysinit.target.wants/lvm2-lvmpolld.socket to /usr/lib/systemd/system/lvm2-lvmpolld.socket.
  Installieren     : docker-selinux-1.8.2-10.el7.centos.x86_64                                                                                            7/8 
  Installieren     : docker-1.8.2-10.el7.centos.x86_64                                                                                                    8/8 
  Überprüfung läuft: 7:lvm2-2.02.130-5.el7.x86_64                                                                                                         1/8 
  Überprüfung läuft: 7:lvm2-libs-2.02.130-5.el7.x86_64                                                                                                    2/8 
  Überprüfung läuft: 7:device-mapper-event-1.02.107-5.el7.x86_64                                                                                          3/8 
  Überprüfung läuft: docker-selinux-1.8.2-10.el7.centos.x86_64                                                                                            4/8 
  Überprüfung läuft: device-mapper-persistent-data-0.5.5-1.el7.x86_64                                                                                     5/8 
  Überprüfung läuft: docker-1.8.2-10.el7.centos.x86_64                                                                                                    6/8 
  Überprüfung läuft: libaio-0.3.109-13.el7.x86_64                                                                                                         7/8 
  Überprüfung läuft: 7:device-mapper-event-libs-1.02.107-5.el7.x86_64                                                                                     8/8 

Installiert:
  docker.x86_64 0:1.8.2-10.el7.centos                                                                                                                         

Abhängigkeit installiert:
  device-mapper-event.x86_64 7:1.02.107-5.el7     device-mapper-event-libs.x86_64 7:1.02.107-5.el7     device-mapper-persistent-data.x86_64 0:0.5.5-1.el7    
  docker-selinux.x86_64 0:1.8.2-10.el7.centos     libaio.x86_64 0:0.3.109-13.el7                       lvm2.x86_64 7:2.02.130-5.el7                          
  lvm2-libs.x86_64 7:2.02.130-5.el7              

Komplett!
[centos@ip-172-31-30-37 ~]$ 

Gleicher Fehler auch in dieser Version !!

## eig. Image Fedora / CentOS

VirtualBox unterstützt nur 32bit-Linuxe ? Server 2012 !
Wie kommt man zu einer vhd Datei ? Konvertierer ? Native Windows mit Windows Server 2012 ?

## RHEL 7.2

extras - repo muss enabled sein
ergebnis ist aber identisch zu centos :-(

## Folge genau http://blog.ranjandas.me/install-docker-1-8-x-on-centos-7/

TODO
