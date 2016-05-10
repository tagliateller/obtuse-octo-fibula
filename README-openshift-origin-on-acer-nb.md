* Ausgangslage: vagrant + virtualbox unter windows installiert, ansible ist auf einer centos vm installiert
* Vagrantfiles in workspace/boxes/origin/[master,node1,node2] anlegen. komlettes vagrantfile geht nicht wg. rsync-Fehler
* IP .100, .201, .202. SSH-Port wird durch vagrant selbst bestimmt
* VM einzeln starten

* Inventory anpassen

```
# Create an OSEv3 group that contains the masters and nodes groups
[OSEv3:children]
masters
nodes

# Set variables common for all OSEv3 hosts
[OSEv3:vars]
# SSH user, this user should allow ssh based auth without requiring a password
ansible_ssh_user=vagrant

# If ansible_ssh_user is not root, ansible_sudo must be set to true
ansible_sudo=true

product_type=openshift
deployment_type=origin

# uncomment the following to enable htpasswd authentication; defaults to DenyAllPasswordIdentityProvider
openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider', 'filename': '/etc/origin/origin-passwd'}]

# host group for masters
[masters]
master ansible_ssh_host=192.168.100.100 ansible_ssh_port=2200

# host group for nodes, includes region info
[nodes]
node1 ansible_ssh_host=192.168.100.201 ansible_ssh_port=2201 openshift_node_labels="{'region': 'primary', 'zone': 'east'}"
node2 ansible_ssh_host=192.168.100.202 ansible_ssh_port=2202 openshift_node_labels="{'region': 'primary', 'zone': 'west'}"

~

```

http://jessesnet.com/development-notes/2014/vagrant-virtual-machine-cluster/
http://superuser.com/questions/671191/how-to-ssh-between-a-cluster-of-vagrant-guest-vms


# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.define "master" do |master|
     master.vm.box = "chef/centos-6.5"
     master.vm.network "private_network", ip: "10.2.2.2"
  end

  config.vm.define "slave" do |slave|
     slave.vm.box = "chef/centos-6.5"
     slave.vm.network "private_network", ip: "10.2.2.4"
  end

end

vagrant up
vagrant ssh master
vagrant ssh slave

So alternatively VMs can be accessed (default password is ‘vagrant’):

ssh vagrant@localhost -p2200
ssh vagrant@localhost -p2201
SSH onto both VMs and verify the IP addresses and that the other server is ping’able:

vagrant ssh master
ifconfig
ping 10.2.2.2
To make SSH’ing between the various VMs and host easier, add SSH key signatures (default password is ‘vagrant’) from host to VM and in-between the VMs.:

ssh-keygen
ssh-copy-id -i ~/.ssh/id_rsa.pub vagrant@10.2.2.2
ssh vagrant@10.2.2.2
Since these VMs use VirtualBox, provider specific configs can be used to specify how much RAM and CPUs each VM can use by adding the following to the VagrantFile:

config.vm.provider "virtualbox" do |v|
  v.memory = 1024
  v.cpus = 1
end
Conclusion
```

```
# -*- mode: ruby -*-
# vi: set ft=ruby :
VAGRANTFILE_API_VERSION = "2"

unless Vagrant.has_plugin?("vagrant-hostmanager")
  raise 'vagrant-hostmanager plugin is required'
end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  deployment_type = ENV['OPENSHIFT_DEPLOYMENT_TYPE'] || 'origin'
  num_nodes = (ENV['OPENSHIFT_NUM_NODES'] || 2).to_i

  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true
  config.ssh.insert_key = false

  config.vm.provider "virtualbox" do |vbox, override|
    override.vm.box = "centos/7"
    vbox.memory = 1024
    vbox.cpus = 2

    # Enable multiple guest CPUs if available
    vbox.customize ["modifyvm", :id, "--ioapic", "on"]
  end

  config.vm.provider "libvirt" do |libvirt, override|
    libvirt.cpus = 2
    libvirt.memory = 1024
    libvirt.driver = 'kvm'
    case deployment_type
    when "openshift-enterprise"
      override.vm.box = "rhel-7"
    when "atomic-enterprise"
      override.vm.box = "rhel-7"
    when "origin"
      override.vm.box = "centos/7"
      override.vm.box_download_checksum = "b2a9f7421e04e73a5acad6fbaf4e9aba78b5aeabf4230eebacc9942e577c1e05"
      override.vm.box_download_checksum_type = "sha256"
    end
  end

  num_nodes.times do |n|
    node_index = n+1
    config.vm.define "node#{node_index}" do |node|
      node.vm.hostname = "ose3-node#{node_index}.example.com"
      node.vm.network :private_network, ip: "192.168.50.#{200 + n}"
    end
  end

  config.vm.define "master" do |master|
    master.vm.hostname = "ose3-master.example.com"
    master.vm.network :private_network, ip: "192.168.50.100"
    master.vm.network :forwarded_port, guest: 8443, host: 8443
  end
end
```

* störend ist hier immer noch rsync, aber mit dreimaligen Aufruf sind alle Maschinen da
* ssh von controller aus geht mit insecure private key (aus c:\user)
* TODO: Installation geht nun auch ?


