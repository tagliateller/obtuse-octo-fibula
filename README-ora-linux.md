# Übersicht

Manueller Start/Stop der Oracle-Datenbank

mit folgenden Befehlen werden:

    die Datenbank
    der Listener
    die isql-Weboberfläche
    und der Oracle-Enterprise-Manager gestartet

$ sqlplus / as sysdba
SQL> startup
SQL> exit
$ lsnrctl start
$ isqlplusctl start
$ emctl start dbconsole

stoppen mit diesen Befehlen:

$ emctl stop dbconsole
$ isqlplusctl stop
$ lsnrctl stop
$ sqlplus / as sysdba
SQL> shutdown immediate
SQL> exit

Datenbank Schritt für Schritt starten:

1. $ sqlplus / as sysdba
2. SQL> startup nomount          NOMOUNT-Phase
3. SQL> alter database mount     MOUNT-Phase
4. SQL> alter database open      OPEN-Phase

Kurzüberblick:

DB starten:                  SQL> startup;
DB stoppen:                  SQL> shutdown immediate;
DB in nomount-Phase starten: SQL> startup nomount;
DB mounten:                  SQL> alter database mount;
DB öffnen:                   SQL> alter database open;
DB restricted öffnen:        SQL> startup open restrict;
DB wieder normal öffnen:     SQL> alter system disable restricted session;
Controlfiles zeigen:         SQL> show parameter control_files;

Einen User trotz restricted Mode die Nutzung der Datenbank erlauben:
SQL> grant restricted session to <user>;



# Oracle-Linux 7 - Maschine

```console
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
```

# Oracle-DB 12.1.0.1 - Maschine

```console
[azureuser@oradb ~]$ df -h
Dateisystem           Size  Used Avail Use% Eingehängt auf
/dev/sda3             7,7G  1,3G  6,1G  18% /
tmpfs                  14G     0   14G   0% /dev/shm
/dev/sda1             485M   52M  408M  12% /boot
/dev/sda5             2,0G   35M  1,9G   2% /tmp
/dev/sda2              30G  5,7G   23G  21% /u01
/dev/sdb1             197G  188M  187G   1% /mnt/resource
[azureuser@oradb ~]$ 
```

```console
[oracle@oradb azureuser]$ uname -a
Linux oradb 2.6.32-358.18.1.el6.x86_64 #1 SMP Tue Aug 27 13:25:08 PDT 2013 x86_64 x86_64 x86_64 GNU/Linux
[oracle@oradb azureuser]$ 
```

```console
ls /etc/*-release
less /etc/redhat-release
```

Red Hat Enterprise Linux Server release 6.4 (Santiago)
/etc/redhat-release (END) 

less /etc/oracle-release

Oracle Linux Server release 6.4
/etc/oracle-release (END) 

```console
ssh -i azure-key-pair -X azureuser@oradb-h4hhk4kx.cloudapp.net
```

[azureuser@oradb ~]$ su oracle
[oracle@oradb azureuser]$ /u01/app/oracle/product/12.1.0/dbhome_1/bin/dbca

User-Pwd: berlin1234#

Hinweis: dbca ist extrem langsam, aber es funktioniert !!

# Benchmarking

http://www.hammerdb.com/hammerdb_quickstart_oracle.pdf
http://www.hammerdb.com/index.html

siehe Bilder

# Silent-DBCA

https://docs.oracle.com/cd/B28359_01/server.111/b28310/create002.htm

```console
[azureuser@oradb ~]$ su oracle
[oracle@oradb azureuser]$ cd ~
[oracle@oradb ~]$ /u01/app/oracle/product/12.1.0/dbhome_1/bin/dbca -silent -createDatabase -templateName General_Purpose.dbc -gdbname ora12c -sid ora12c -respo
nseFile NO_VALUE -characterSet AL32UTF8 -memoryPercentage 30 -emConfiguration LOCAL
Enter SYS user password:

Enter SYSTEM user password:

```

```console
[azureuser@oradb ~]$ su oracle
[oracle@oradb azureuser]$ cd ~
[oracle@oradb ~]$ /u01/app/oracle/product/12.1.0/dbhome_1/bin/dbca -silent -createDatabase -templateName General_Purpose.dbc -gdbname ora12c -sid ora12c -respo
nseFile NO_VALUE -characterSet AL32UTF8 -memoryPercentage 30 -emConfiguration LOCAL
Enter SYS user password:

Enter SYSTEM user password:

Copying database files
1% complete
3% complete
11% complete
18% complete
26% complete
33% complete
37% complete
Creating and starting Oracle instance
40% complete
45% complete
50% complete
55% complete
56% complete
60% complete
62% complete
Completing Database Creation
66% complete
70% complete
73% complete
85% complete
96% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/ora12c/ora12c.log" for further details.
[oracle@oradb ~]$
```

Fehler:
[oracle@oradb HammerDB-2.18]$ ./hammerdb.tcl 
wish8.6: error while loading shared libraries: libXss.so.1: cannot open shared object file: No such file or directory

Lösung
sudo yum install libXScrnSaver

```console
# .bashrc

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin

export PATH

# User specific environment and startup programs
# /u01/app/oracle/product/12.1.0/dbhome_1/
umask 022
export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=$ORACLE_BASE/product/12.1.0/dbhome_1
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
export ORACLE_LIBRARY=$ORACLE_HOME/lib/libclntsh.so
export ORACLE_SID=ora12c
export PATH=$ORACLE_HOME/bin:$PATH
```                                      

```console
[oracle@oradb ~]$ sqlplus / as sysdba

SQL*Plus: Release 12.1.0.1.0 Production on Thu Dec 3 16:37:27 2015

Copyright (c) 1982, 2013, Oracle.  All rights reserved.

Connected to an idle instance.

SQL> startup
ORACLE instance started.

Total System Global Area 6630809600 bytes
Fixed Size		    2303360 bytes
Variable Size		 1241516672 bytes
Database Buffers	 5368709120 bytes
Redo Buffers		   18280448 bytes
Database mounted.
Database opened.
SQL> exit
Disconnected from Oracle Database 12c Enterprise Edition Release 12.1.0.1.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
[oracle@oradb ~]$ lsnrctl start

LSNRCTL for Linux: Version 12.1.0.1.0 - Production on 03-DEC-2015 16:40:22

Copyright (c) 1991, 2013, Oracle.  All rights reserved.

Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

TNSLSNR for Linux: Version 12.1.0.1.0 - Production
Log messages written to /u01/app/oracle/diag/tnslsnr/oradb/listener/alert/log.xml
Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=oradb.oradb-26i3q3z1.g5.internal.cloudapp.net)(PORT=1521)))

Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 12.1.0.1.0 - Production
Start Date                03-DEC-2015 16:40:28
Uptime                    0 days 0 hr. 0 min. 5 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Log File         /u01/app/oracle/diag/tnslsnr/oradb/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=oradb.oradb-26i3q3z1.g5.internal.cloudapp.net)(PORT=1521)))
The listener supports no services
The command completed successfully
[oracle@oradb ~]$ 
```

vi /u01/app/oracle/product/12.1.0/dbhome_1/network/admin/tnsnames.ora

ORA12C =
 (DESCRIPTION =
   (ADDRESS_LIST =
     (ADDRESS = (PROTOCOL = TCP)(HOST = oradb.oradb-26i3q3z1.g5.internal.cloudapp.net)(PORT = 1521))
   )
 (CONNECT_DATA =
   (SERVICE_NAME = ORA12C)
 )
)
~                                                                                                                                                             
~     

[oracle@oradb HammerDB-2.18]$ tnsping ora12c

TNS Ping Utility for Linux: Version 12.1.0.1.0 - Production on 03-DEC-2015 17:05:08

Copyright (c) 1997, 2013, Oracle.  All rights reserved.

Used parameter files:


Used TNSNAMES adapter to resolve the alias
Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = oradb.oradb-26i3q3z1.g5.internal.cloudapp.net)(PORT = 1521))) (CONNECT_DATA = (SERVICE_NAME = ORA12C)))
OK (0 msec)

Achtung - Tablespace muss USERS sein !!!


