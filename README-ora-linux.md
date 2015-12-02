# Oracle-Linux 7 - Maschine

[azureuser@orapingu ~]$ df -h
Dateisystem    Größe Benutzt Verf. Verw% Eingehängt auf
/dev/sda2        27G    2,1G   24G    9% /
devtmpfs        1,7G       0  1,7G    0% /dev
tmpfs           1,7G       0  1,7G    0% /dev/shm
tmpfs           1,7G     17M  1,7G    1% /run
tmpfs           1,7G       0  1,7G    0% /sys/fs/cgroup
/dev/sda1       477M    174M  274M   39% /boot
/dev/sda3       2,0G    6,1M  1,8G    1% /tmp
/dev/sdb1       6,8G     32M  6,4G    1% /mnt/resource
[azureuser@orapingu ~]$ 

# Oracle-DB 12.1.0.1 - Maschine

[azureuser@oradb ~]$ df -h
Dateisystem           Size  Used Avail Use% Eingehängt auf
/dev/sda3             7,7G  1,3G  6,1G  18% /
tmpfs                  14G     0   14G   0% /dev/shm
/dev/sda1             485M   52M  408M  12% /boot
/dev/sda5             2,0G   35M  1,9G   2% /tmp
/dev/sda2              30G  5,7G   23G  21% /u01
/dev/sdb1             197G  188M  187G   1% /mnt/resource
[azureuser@oradb ~]$ 

[oracle@oradb azureuser]$ uname -a
Linux oradb 2.6.32-358.18.1.el6.x86_64 #1 SMP Tue Aug 27 13:25:08 PDT 2013 x86_64 x86_64 x86_64 GNU/Linux
[oracle@oradb azureuser]$ 

ls /etc/*-release
less /etc/redhat-release

Red Hat Enterprise Linux Server release 6.4 (Santiago)
/etc/redhat-release (END) 

less /etc/oracle-release

Oracle Linux Server release 6.4
/etc/oracle-release (END) 

ssh -i azure-key-pair -X azureuser@oradb-h4hhk4kx.cloudapp.net

[azureuser@oradb ~]$ su oracle
[oracle@oradb azureuser]$ /u01/app/oracle/product/12.1.0/dbhome_1/bin/dbca

User-Pwd: berlin1234#

Hinweis: dbca ist extrem langsam, aber es funktioniert !!

# Benchmarking

http://www.hammerdb.com/hammerdb_quickstart_oracle.pdf
http://www.hammerdb.com/index.html

siehe Bilder

# Silent-DBCA

```console
[azureuser@oradb ~]$ su oracle
[oracle@oradb azureuser]$ cd ~
[oracle@oradb ~]$ /u01/app/oracle/product/12.1.0/dbhome_1/bin/dbca -silent -createDatabase -templateName General_Purpose.dbc -gdbname ora12c -sid ora12c -respo
nseFile NO_VALUE -characterSet AL32UTF8 -memoryPercentage 30 -emConfiguration LOCAL
Enter SYS user password:

Enter SYSTEM user password:

```



