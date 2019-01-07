# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "jumperfly/centos-7"
  config.vm.box_version = "1809.01.01"
  config.vm.box_check_update = false
  config.ssh.insert_key = false

  config.vm.provider "virtualbox" do |v|
    v.memory = 256
    v.cpus = 1
  end

  config.vm.provision "shell", inline: <<-SHELL
    cp /vagrant/vagrant_insecure_key /home/vagrant/.ssh/id_rsa
    chmod 600 /home/vagrant/.ssh/id_rsa
    chown vagrant:vagrant /home/vagrant/.ssh/id_rsa
  SHELL

  config.vm.define "host1" do |config|
    config.vm.network "private_network", ip: "192.168.99.201"
  end

  config.vm.define "controller" do |config|
    config.vm.box = "jumperfly/centos-7-ansible"
    config.vm.box_version = "1804.02.01"
    config.vm.network "private_network", ip: "192.168.99.200"
    config.vm.provision "shell", inline: "mkdir -p /etc/ansible/roles && ln -sfn /vagrant /etc/ansible/roles/jumperfly.ansible_common"

    config.vm.provision "ansible_local" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "tests/test.yml"
      ansible.limit = "all"
      ansible.host_vars = {
        "host1": {
          "ansible_host": "192.168.99.201"
        }
      }
    end
  end
end
