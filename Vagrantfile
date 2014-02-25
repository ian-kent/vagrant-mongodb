# -*- mode: ruby -*-
# vi: set ft=ruby :

## vagrant plugins required:
# vagrant-omnibus, vagrant-hosts, vagrant-cachier

# https://github.com/everpeace/cookbook-mesos/blob/master/example/mesosphere/Vagrantfile

# Set VM_HOSTNAME, VM_MEM and SERF_JOIN
# Required even to run ssh/destroy
# E.g.
# VM_HOSTNAME="mongodb1" VM_MEM="1024" SERF_JOIN="zk1" vagrant up

Vagrant.configure("2") do |config|

  # enable plugins
  config.omnibus.chef_version = :latest
  config.cache.auto_detect = true

  config.vm.provider :virtualbox do |vb, override|
    #override.vm.box = "Official Ubuntu 13.04 daily Cloud Image amd64 (No Guest Additions)"
    #override.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/raring/current/raring-server-cloudimg-amd64-vagrant-disk1.box"

    override.vm.box = "Official Ubuntu Precise64 Cloud Image current"
    override.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box"

    override.vm.hostname = ENV['VM_HOSTNAME']
    override.vm.network :public_network

    vb.name = 'vagrant-mesos-' + ENV['VM_HOSTNAME']
    vb.customize ["modifyvm", :id, "--memory", ENV['VM_MEM'] ]
  end

$script = <<SCRIPT
  # Configure apt for mongodb
  # http://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/
  apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10
  echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' | tee /etc/apt/sources.list.d/mongodb.list
  apt-get update
  apt-get install -y mongodb-10gen
SCRIPT
  config.vm.provision "shell", inline: $script

$script = <<SCRIPT
  wget https://dl.bintray.com/mitchellh/serf/0.4.1_linux_amd64.zip
  apt-get install -y unzip
  unzip 0.4.1_linux_amd64.zip
  mv serf /usr/local/bin
  rm 0.4.1_linux_amd64.zip
  wget https://raw.github.com/hashicorp/serf/c15b5f15dec3d735dae1a6d1f3455d4d7b5685e7/ops-misc/upstart.conf
  mv upstart.conf /etc/init/serf-agent.conf
  mkdir /etc/serf
  ip=`ip addr list eth1 | grep "inet " | cut -d ' ' -f6 | cut -d/ -f1`
  echo { \\"start_join\\": [\\"$1\\"], \\"protocol\\": 3, \\"tags\\": { \\"role\\": \\"mongodb\\" }, \\"advertise\\": \\"$ip\\" } | tee /etc/serf/config.json
  exec start serf-agent
SCRIPT
  config.vm.provision "shell", inline: $script, args: "#{ENV['SERF_JOIN']}"
end
