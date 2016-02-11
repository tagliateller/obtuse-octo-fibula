Nach https://blog.openshift.com/running-any-docker-image-on-openshift-origin/

= Installation single vm
Vorauss.: Vagrant und Virtualbox sind installiert

Download Vagrantfile von https://www.openshift.org/vm/Vagrantfile

Im Vagrantfile 8443 freischalten

VM starten

'''
robert@c3p0:~/workspace/boxes/openshift-all-in-one$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'thesteve0/openshift-origin'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: origin-1.1.1
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: 
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default: 
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: No guest additions were detected on the base box for this VM! Guest
    default: additions are required for forwarded ports, shared folders, host only
    default: networking, and more. If SSH fails on this machine, please install
    default: the guest additions and repackage the box to continue.
    default: 
    default: This is not an error message; everything may continue to work properly,
    default: in which case you may ignore this message.
==> default: Setting hostname...
==> default: Configuring and enabling network interfaces...
robert@c3p0:~/workspace/boxes/openshift-all-in-one$ 
'''

Browser mit https://localhost:8443 öffnen, admin/admin anmelden

= weiter auf der cmdline

robert@c3p0:~$ oc login https://localhost:8443
The server uses a certificate signed by an unknown authority.
You can bypass the certificate check, but any data you send to the server could be intercepted by others.
Use insecure connections? (y/n): y

Authentication required for https://localhost:8443 (openshift)
Username: admin
Password: 
Login successful.

You have access to the following projects and can switch between them with 'oc project <projectname>':

  * default (current)
  * openshift
  * openshift-infra
  * sample

Using project "default".
Welcome! See 'oc help' to get started.

= neues Projekt anlegen

robert@c3p0:~$ oc new-project myfear --description="Testing random docker images on OpenShift" --display-name "Myfears test project"
Now using project "myfear" on server "https://localhost:8443".

You can add applications to this project with the 'new-app' command. For example, try:

    $ oc new-app centos/ruby-22-centos7~https://github.com/openshift/ruby-hello-world.git

to build a new hello-world application in Ruby.
robert@c3p0:~$ 

= dem nutzer myfear die rolle admin geben

robert@c3p0:~$ oc policy add-role-to-user myfear admin -n myfear
Error from server: Role "myfear" not found
robert@c3p0:~$ 

--> das geht schon mal nicht, weiter im text
--> klären, woher der Nutzer kommt
--> auch die weiteren Punkte klappen so nicht, die json-Files sind leer
 
