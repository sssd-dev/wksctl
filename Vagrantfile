# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    (1..3).each do |i|
         config.vm.define vm_name = "%s-%02d" % ["kube", i] do |config|
            config.vm.box = "centos/7"
            config.vm.hostname = vm_name

            config.ssh.insert_key = false
            config.vm.provision "file", source: "~/.vagrant.d/insecure_private_key", destination: "/home/vagrant/.ssh/id_rsa"
            config.vm.provision "shell", inline: "chmod 400 /home/vagrant/.ssh/id_rsa"

            config.vm.provider "virtualbox" do |vb|
                vb.memory = "2048"
                vb.cpus = 2
            end

            config.vm.network :private_network, ip: "172.17.8.#{i+100}"
            # Allows ssh access to the boxes
            config.vm.network :forwarded_port, guest: 22, host: "22#{i}2", id: "ssh"
            # We need this to establish kubectl connectivity with kubernetes master api
            config.vm.network :forwarded_port, guest: 6443, host: "644#{i}3"
            # The below line is required for Weave Net to reach the API server
            # in order to get the list of peers. Without it, worker nodes never
            # become "Ready".
            config.vm.provision "shell", inline: "ip route add 10.96.0.0/16 dev eth1"
        end
    end
end
