MongoDB with Serf
=================

Vagrant build for Ubuntu with MongoDB and Serf

Requires Vagrant v2 - http://www.vagrantup.com/

Also requires Vagrant plugins:
  - vagrant-omnibus
  - vagrant-hosts
  - vagrant-cachier

Installs:
  - Vagrant
  - Serf

The following environment variables are required for every vagrant command:

  - VM_HOSTNAME - the hostname to configure the VM with
  - VM_MEM - VM ram (in mb)
  - SERF_JOIN - a Serf node to connect to

Example:

    VM_HOSTNAME="mongodb1" VM_MEM="1024" SERF_JOIN="zk1" vagrant up
