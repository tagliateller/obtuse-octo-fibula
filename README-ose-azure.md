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

Anmeldung und Ressourcenmanager einschalten

```
azure login
azure config mode arm
```

Host Key Checking ausschalten

```shell
$ export ANSIBLE_HOST_KEY_CHECKING=False
```

Erzeugung der VMs

```shell
$ ansible-playbook -i azure-create.inventory playbooks/azure/openshift-enterprise-cluster-create.yml
```

## Installation NFS-Server

$ sudo yum -y install nfs-utils nfs-utils-lib
$ sudo systemctl status nfs-config.service

Weitere Platte anhängen

$ azure vm disk attach-new osev3-group osev3master 60

[azureuser@osev3master ~]$ sudo dmesg | grep SCSI
[    0.224074] SCSI subsystem initialized
[    0.624374] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 251)
[    1.176175] sd 3:0:1:0: [sdb] Attached SCSI disk
[    1.200199] sd 2:0:0:0: [sda] Attached SCSI disk
[   69.797614] sd 5:0:0:0: [sdc] Attached SCSI disk
[ 1702.720407] sd 5:0:0:1: [sdd] Attached SCSI disk
[azureuser@osev3master ~]$


[azureuser@osev3master ~]$ sudo fdisk /dev/sdd
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x4b35633b.

The device presents a logical sector size that is smaller than
the physical sector size. Aligning to a physical sector (or optimal
I/O) size boundary is recommended, or performance may be impacted.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-125829119, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-125829119, default 125829119):
Using default value 125829119
Partition 1 of type Linux and of size 60 GiB is set

Command (m for help): p

Disk /dev/sdd: 64.4 GB, 64424509440 bytes, 125829120 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk label type: dos
Disk identifier: 0x4b35633b

   Device Boot      Start         End      Blocks   Id  System
/dev/sdd1            2048   125829119    62913536   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[azureuser@osev3master ~]$


[azureuser@osev3master ~]$ sudo mkfs -t ext4 /dev/sdd1
mke2fs 1.42.9 (28-Dec-2013)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
3932160 inodes, 15728384 blocks
786419 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2164260864
480 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

[azureuser@osev3master ~]$

[azureuser@osev3master ~]$ sudo mkdir /nfsstorage
[azureuser@osev3master ~]$ sudo mount /dev/sdd1 /nfsstorage

[azureuser@osev3master ~]$ sudo -i blkid
/dev/sdb1: UUID="e5496f22-a796-440c-9bb4-ef09fa472650" TYPE="ext4"
/dev/sda1: UUID="f8c55fc4-8bd7-4333-9e7f-2ddb778fb0c0" TYPE="xfs"
/dev/sda2: UUID="3a095553-37df-41d7-991b-7f99d19ee37b" TYPE="xfs"
/dev/sr0: LABEL="rd_rdfe_stable.160202-1748" TYPE="udf"
/dev/sdd1: UUID="264d077f-e5a4-4908-81d7-2eb6bc215e54" TYPE="ext4"
[azureuser@osev3master ~]$

sudo vi /etc/fstab

an das Ende anfügen

UUID=264d077f-e5a4-4908-81d7-2eb6bc215e54   /datadrive   ext4   defaults   1   2

sudo chmod go+w /datadrive

## NFS-Server konfigurieren


Installation OpenShift Enterprise

```shell

```
