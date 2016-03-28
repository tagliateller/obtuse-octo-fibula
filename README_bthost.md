# VM einrichten und btsync installieren

vagrant ssh

vagrant@vagrant-ubuntu-trusty-64:~$ mkdir btsync-app
vagrant@vagrant-ubuntu-trusty-64:~$ mkdir BTSync
vagrant@vagrant-ubuntu-trusty-64:~$ cd btsync-app/
vagrant@vagrant-ubuntu-trusty-64:~/btsync-app$

frei nach: https://blog.getsync.com/2016/02/18/official-linux-packages-for-sync-now-available/

vagrant@vagrant-ubuntu-trusty-64:~/btsync-app$ sudo sh -c 'echo "deb http://linux-packages.getsync.com/btsync/deb btsyn
c non-free" > /etc/apt/sources.list.d/btsync.list'
vagrant@vagrant-ubuntu-trusty-64:~/btsync-app$ wget -qO - http://linux-packages.getsync.com/btsync/key.asc | sudo apt-k
ey add -
OK
vagrant@vagrant-ubuntu-trusty-64:~/btsync-app$

sudo apt-get update
sudo apt-get install btsync

# Anpassen der Config-Datei

/etc/btsync/config.json

{
    "listening_port" : 0,
    "storage_path" : "/var/lib/btsync/",
    "pid_file" : "/var/run/btsync/btsync.pid",
    "agree_to_EULA": "yes",

    "webui" :
    {
        "listen" : "0.0.0.0:8888"
    }
}

--> hier muss die webui-IP geändert werden, damit anfragen auch aus dem Netz entgegengenommen werden können.
Hinweis: Weitere Einstellung sind mit eigener  config möglich, dazu "btsync --dump-sample-config aufrufen", "/etc/init.d/btsync" anpassen

Ergebnis: localhost:8888 ist erreichbar (Vagrantfile anpassen, Port 8888 weiterleiten!)

# Test der Syncronisation 


