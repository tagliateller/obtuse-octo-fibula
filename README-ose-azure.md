# Install Azure CLI on Client

## Debian

$ curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
$ sudo apt-get install -y nodejs
$ sudo npm install azure-cli -g

## CentOS

$ curl --silent --location https://rpm.nodesource.com/setup_4.x | sudo bash -
$ sudo yum -y install nodejs
$ sudo npm install azure-cli -g

## CentOS

$ curl --silent --location https://rpm.nodesource.com/setup_4.x | sudo bash -
$ sudo yum -y install nodejs
$ sudo npm install azure-cli -g

$ azure login


# Add disk to master and nodes

$ azure vm disk attach-new osev3 osev3[master,node1,node2] 60
 
[azure@osev3master ~]$ sudo docker-storage-setup 
  Volume group "sda2" not found
  Cannot process volume group sda2
Überprüfe, dass niemand diese Festplatte zur Zeit benutzt …
OK

Festplatte /dev/sdc: 7832 Zylinder, 255 Köpfe, 63 Sektoren/Spur
sfdisk:  /dev/sdc: nicht erkannte Partitiontabellentyp

Alte Aufteilung:
sfdisk: Keine Partitionen gefunden

Neue Aufteilung:
Units: sectors of 512 bytes, counting from 0

   Gerät  boot.   Anfang      Ende  #Sektoren Id  System
/dev/sdc1          2048 125829119  125827072  8e  Linux LVM
/dev/sdc2             0         -          0   0  Leer
/dev/sdc3             0         -          0   0  Leer
/dev/sdc4             0         -          0   0  Leer
Warnung: Partition 1 endet nicht an einer Zylindergrenze
Warnung: Keine primäre Partition wurde als bootfähig (aktiv) markiert.
         Dies spielt bei LILO keine Rolle, aber der DOS-MBR wird auf
         dieser Festplatte nicht booten.
Die neue Partitionstabelle wurde erfolgreich geschrieben

Die Partitionstabelle wird erneut gelesen…

Wenn Sie eine DOS-Partition angelegt oder geändert haben, z. B. /dev/foo7,
dann nehmen Sie dd(1), um die ersten 512 Bytes auf 0 zu setzen:
„dd if=/dev/zero of=/dev/foo7 bs=512 count=1“ (siehe fdisk(8)).
  Physical volume "/dev/sdc1" successfully created
  Volume group "docker-vg" successfully created
  Rounding up size to full physical extent 64,00 MiB
  Logical volume "docker-poolmeta" created.
  Logical volume "docker-pool" created.
  WARNING: Converting logical volume docker-vg/docker-pool and docker-vg/docker-poolmeta to pool's data and metadata volumes.
  THIS WILL DESTROY CONTENT OF LOGICAL VOLUME (filesystem etc.)
  Converted docker-vg/docker-pool to thin pool.
  Logical volume "docker-pool" changed.
[azure@osev3master ~]$ 

[azure@osev3master ~]$ cat /etc/sysconfig/docker-storage-setup 
DEVS=/dev/sdc
VG=docker-vg
[azure@osev3master ~]$ 

[azure@osev3node2 ~]$ cat /etc/sysconfig/docker-storage
DOCKER_STORAGE_OPTIONS=--storage-driver devicemapper --storage-opt dm.fs=xfs --storage-opt dm.thinpooldev=/dev/mapper/docker--vg-docker--pool --storage-opt dm.use_deferred_removal=true
[azure@osev3node2 ~]$ 


A list of the facts gathered from the provided hosts follows. Because it is
often the case that the hostname for a system inside the cluster is different
from the hostname that is resolveable from command line or web clients
these settings cannot be validated automatically.

For some cloud providers the installer is able to gather metadata exposed in
the instance so reasonable defaults will be provided.

Plese confirm that they are correct before moving forward.


osev3master.westeurope.cloudapp.azure.com,10.1.0.4,10.1.0.4,osev3master.wlk1ugebnt3u3foabmpfhhmxgf.ax.internal.cloudapp.net,osev3master.wlk1ugebnt3u3foabmpfhhmxgf.ax.internal.cloudapp.net
osev3node1.westeurope.cloudapp.azure.com,10.1.0.5,10.1.0.5,osev3node1.wlk1ugebnt3u3foabmpfhhmxgf.ax.internal.cloudapp.net,osev3node1.wlk1ugebnt3u3foabmpfhhmxgf.ax.internal.cloudapp.net
osev3node2.westeurope.cloudapp.azure.com,10.1.0.6,10.1.0.6,osev3node2.wlk1ugebnt3u3foabmpfhhmxgf.ax.internal.cloudapp.net,osev3node2.wlk1ugebnt3u3foabmpfhhmxgf.ax.internal.cloudapp.net

Format:

connect_to,IP,public IP,hostname,public hostname

Notes:
 * The installation host is the hostname from the installer's perspective.
 * The IP of the host should be the internal IP of the instance.
 * The public IP should be the externally accessible IP associated with the instance
 * The hostname should resolve to the internal IP from the instances
   themselves.
 * The public hostname should resolve to the external ip from hosts outside of
   the cloud.

Do the above facts look correct? [y/N]: N

Edit /home/azure/.config/openshift/installer.cfg.yml with the desired values and run `atomic-openshift-installer --unattended install` to restart the install.

# Installation mit ansible

$ export ANSIBLE_HOST_KEY_CHECKING=False


