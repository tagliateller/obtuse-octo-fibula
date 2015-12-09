```console
sudo yum -y update

sudo yum -y install docker

sudo systemctl start docker
```

[azureuser@demov3-master ~]$ sudo systemctl status docker.service
docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled)
   Active: failed (Result: timeout) since Mi 2015-12-09 23:03:01 UTC; 33s ago
     Docs: http://docs.docker.com
 Main PID: 11111

Dez 09 23:01:41 demov3-master docker[11111]: time="2015-12-09T23:01:41.405148900Z" level=info msg="Listening for HTTP on unix (/var/run/docker.sock)"
Dez 09 23:01:42 demov3-master docker[11111]: time="2015-12-09T23:01:42.268125500Z" level=error msg="WARNING: No --storage-opt dm.thinpooldev speci...tion use"
Dez 09 23:02:41 demov3-master systemd[1]: docker.service operation timed out. Terminating.
Dez 09 23:03:01 demov3-master systemd[1]: Failed to start Docker Application Container Engine.
Dez 09 23:03:01 demov3-master systemd[1]: Unit docker.service entered failed state.
Hint: Some lines were ellipsized, use -l to show in full.
[azureuser@demov3-master ~]$ 

[azureuser@demov3-master ~]$ sudo yum -y install device-mapper device-mapper-event device-mapper-libs device-mapper-event-libs
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
Paket 7:device-mapper-1.02.93-3.el7_1.1.x86_64 ist bereits in der neuesten Version installiert.
Paket 7:device-mapper-event-1.02.93-3.el7_1.1.x86_64 ist bereits in der neuesten Version installiert.
Paket 7:device-mapper-libs-1.02.93-3.el7_1.1.x86_64 ist bereits in der neuesten Version installiert.
Paket 7:device-mapper-event-libs-1.02.93-3.el7_1.1.x86_64 ist bereits in der neuesten Version installiert.
Nichts zu tun
[azureuser@demov3-master ~]$ 

Der Tipp bringt also auch nichts:

http://www.itzgeek.com/how-tos/linux/centos-how-tos/installing-docker-on-centos-7-rhel-7-fedora-21.html


