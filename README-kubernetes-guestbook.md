# Installation kubernetes guestbook

```
[centos@XXX ~]$ sudo docker pull kubernetes/guestbook
Trying to pull repository docker.io/kubernetes/guestbook ...
4305190a3fb3: Download complete 
511136ea3c5a: Download complete 
388abcde152c: Download complete 
4767a4c6f365: Download complete 
bcf70befccdf: Download complete 
485267f0f212: Download complete 
a9081498d5ee: Download complete 
6bdeaff22aae: Download complete 
7a74244a951e: Download complete 
df134d7d0392: Download complete 
47f14b7ce740: Download complete 
Status: Downloaded newer image for docker.io/kubernetes/guestbook:latest
```

http://kubernetes.io/v1.0/examples/guestbook-go/README.html ??

```
[centos@XXX ~]$ sudo docker run -d -p 3000:3000 kubernetes/guestbook
8d75dc53643e4a95f2719bcf47628e9d67183cc93693aa96c4df69e0d1cf01ec
Usage of loopback devices is strongly discouraged for production use. Either use `--storage-opt dm.thinpooldev` or use `--storage-opt dm.no_warn_on_loop_devices=true` to suppress this warning.
```

```
[centos@ip-XXX ~]$ sudo docker ps
CONTAINER ID        IMAGE                  COMMAND                CREATED             STATUS              PORTS                    NAMES
8d75dc53643e        kubernetes/guestbook   "./guestbook"          7 seconds ago       Up 6 seconds        0.0.0.0:3000->3000/tcp   clever_banach       
dd9830033446        wordpress              "/entrypoint.sh apac   18 minutes ago      Up 18 minutes       0.0.0.0:80->80/tcp       wordpress           
e4670c162592        mysql                  "/entrypoint.sh mysq   20 minutes ago      Up 20 minutes       3306/tcp                 mysqlwp       
```

Guestbook ist nun via URL:3000 erreichbar -- gut !!

