# -*- mode: ruby -*-
# vi: set ft=ruby :
vagrant_default_provider = "virtualbox"
vagrant_name = "nfs-server"

Vagrant.configure("2") do |config|
  ENV['VAGRANT_DEFAULT_PROVIDER'] = vagrant_default_provider
  config.vm.box = "elegoev/ubuntu-18.04-nfs"
  config.vm.define vagrant_name
  config.vm.hostname = vagrant_name

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--name", vagrant_name]
    v.customize ["modifyvm", :id, "--memory", "512"]
    v.customize ["modifyvm", :id, "--cpus", "1"]
  end

  config.vm.network "forwarded_port", guest: 2049, host: 2099, host_ip: ENV['LAB_IP'], id: "nfs-server"

  config.vm.provision "shell", inline: <<-SHELL
    # Prepare directories
    echo "# Settings managed by Vagrantfile" > /etc/exports
    #############################################
    # NFS volumes for wordpress/mysql demo
    #############################################
    i=0
    usage=wp-mysql
    while [ $i -le 2 ]; do
      mkdir -p /mnt/volumes/${usage}-$i
      echo "/mnt/volumes/${usage}-$i      (rw,async,no_subtree_check,no_auth_nlm,insecure,no_root_squash)" >> /etc/exports
      ((i++))
    done

    #############################################
    # NFS volumes for elasticsearch demo
    #############################################
    i=0
    usage=elasticsearch
    while [ $i -le 2 ]; do
      mkdir -p /mnt/volumes/${usage}-$i
      echo "/mnt/volumes/${usage}-$i      (rw,async,no_subtree_check,no_auth_nlm,insecure,no_root_squash)" >> /etc/exports
      ((i++))
    done

    #############################################
    # NFS volumes for openshift wp demo
    #############################################
    usage=oc-wp-wordpress-data
    mkdir -p /mnt/volumes/${usage}
    echo "/mnt/volumes/${usage}      (rw,async,no_subtree_check,no_auth_nlm,insecure,no_root_squash)" >> /etc/exports
    usage=oc-wp-mysql-data
    mkdir -p /mnt/volumes/${usage}
    echo "/mnt/volumes/${usage}      (rw,async,no_subtree_check,no_auth_nlm,insecure,no_root_squash)" >> /etc/exports

    chmod 777 -R /mnt/volumes

    # Restart NFS server
    systemctl restart nfs-kernel-server
  SHELL
end
