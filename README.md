# obtuse-octo-fibula
## Docker-Installation t2.medium

* t2.medium initialisieren, centos 7 (marketplace)
```
sudo yum -y update
sudo yum -y install docker
sudo systemctl start docker
```
```
[centos@XXX ~]$ sudo docker version
Client version: 1.7.1
Client API version: 1.19
Package Version (client): docker-1.7.1-115.el7.x86_64
Go version (client): go1.4.2
Git commit (client): 446ad9b/1.7.1
OS/Arch (client): linux/amd64
Server version: 1.7.1
Server API version: 1.19
Package Version (server): docker-1.7.1-115.el7.x86_64
Go version (server): go1.4.2
Git commit (server): 446ad9b/1.7.1
OS/Arch (server): linux/amd64
```
```
[centos@XXX ~]$ sudo docker pull wordpress
latest: Pulling from docker.io/wordpress
575489a51992: Pull complete 
6845b83c79fb: Pull complete 
3df3f2590da5: Pull complete 
3aa3c78f21f1: Pull complete 
57860eb0fb6f: Pull complete 
8999bce4bf9d: Pull complete 
8ea1b0aa1793: Pull complete 
ee36c6ecf5e7: Pull complete 
e30a0bf7a009: Pull complete 
708514bdb1d4: Pull complete 
6dfa3a5d3712: Pull complete 
85d85d7c2f39: Pull complete 
5eab8c1996b1: Pull complete 
246246b80022: Pull complete 
dd6018d8c313: Pull complete 
f7ae395a24a1: Pull complete 
2993bb26faab: Pull complete 
1ab5f38f876a: Pull complete 
c0fb6d60e35c: Pull complete 
1f3e3a5f6919: Pull complete 
3e5259a3b243: Pull complete 
5c6e28b378a2: Pull complete 
fe6652d28f37: Pull complete 
164519f10bec: Pull complete 
f48da55e54a7: Pull complete 
1d5a3b3fa7eb: Pull complete 
0ff075fdfe63: Pull complete 
6e1721e5acae: Pull complete 
e9e968179066: Pull complete 
3c342f5e2eed: Pull complete 
0f3bfe695a17: Pull complete 
05a4abb90627: Pull complete 
docker.io/wordpress:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
Digest: sha256:9ff223641a1ae9d21a4c9e0e98684ef1212cebaf4e0944e7fa8851f33e7c0c2a
Status: Downloaded newer image for docker.io/wordpress:latest
```
```
[centos@XXX ~]$ sudo docker pull mysql:latest
latest: Pulling from docker.io/mysql
d00c34d2a9a8: Pull complete 
446d4e47e83e: Pull complete 
1f8ef4d817ed: Pull complete 
b1f3ce0d7f9c: Pull complete 
8d582eda87b8: Pull complete 
711c15ab8323: Pull complete 
b6813d20e7fc: Pull complete 
c09ae1d3480c: Pull complete 
db3e505e0abe: Pull complete 
afdfd5e9a89b: Pull complete 
f794540c6bc7: Pull complete 
b8fe26b9c5c9: Pull complete 
67666c93c68c: Pull complete 
196db1908492: Pull complete 
575489a51992: Already exists 
6845b83c79fb: Already exists 
docker.io/mysql:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
Digest: sha256:43c3231883d1a3a69da1faf594a4c9b7455a9659e946fd677858e71c10d011bd
Status: Downloaded newer image for docker.io/mysql:latest
```

```
[centos@XXX ~]$ sudo docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
docker.io/wordpress   latest              05a4abb90627        9 hours ago         511.9 MB
docker.io/mysql       latest              196db1908492        2 days ago          359.8 MB
```

```
[centos@XXX ~]$ sudo docker run --name mysqlwp -e MYSQL_ROOT_PASSWORD=wordpressdocker -d mysql
e4670c162592a97263f4da7a459da2ab2485f65de5f08a63dbb868b1569235cd
Usage of loopback devices is strongly discouraged for production use. Either use `--storage-opt dm.thinpooldev` or use `--storage-opt dm.no_warn_on_loop_devices=true` to suppress this warning.
```

```
[centos@XXX ~]$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED              STATUS              PORTS               NAMES
e4670c162592        mysql               "/entrypoint.sh mysq   About a minute ago   Up About a minute   3306/tcp            mysqlwp             
```

```
[centos@XXX ~]$ sudo docker run --name wordpress --link mysqlwp:mysql -p 80:80 -d wordpress
dd983003344625e6d786ce986012f27231ba4ca2a792207ada86375325c6d9c3
Usage of loopback devices is strongly discouraged for production use. Either use `--storage-opt dm.thinpooldev` or use `--storage-opt dm.no_warn_on_loop_devices=true` to suppress this warning.
```
```
[centos@XXX ~]$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                NAMES
dd9830033446        wordpress           "/entrypoint.sh apac   29 seconds ago      Up 28 seconds       0.0.0.0:80->80/tcp   wordpress           
e4670c162592        mysql               "/entrypoint.sh mysq   2 minutes ago       Up 2 minutes        3306/tcp             mysqlwp             
```

Damit kann Wordpress aufgerufen und weiter (über die Oberfläche) installiert werden.


#Tipps

docker exec -t -i 589f2ad30138 /bin/bash# Select Azure subscription

 
 subscription setzen
 

azure account set "$azuresubname"

 

# Confirm that subscription is now default

 

azuresubdefault=$(azure account show --json "$azuresubname" | jsawk 'return this.isDefault')

if [ "$azuresubdefault" != "true" ]; then

    echo "Error: Azure subscription ${azuresubdefault} not found as default subscription"

    exit 1

fi# Select Linux VM image 

 

azurevmimage=$(azure vm image list --json | jsawk -n 'out(this.name)' -q "[?name=\"*Ubuntu-14_04_1-LTS-amd64-server*\"]" | sort | tail -n 1)

 

# Confirm that valid Linux VM Image is selected

 

if [ "$azurevmimage" = "" ]; then

    echo "Error: Azure VM image not found"

    exit 1

fi