IMAGE_NAME = "ubuntu/xenial64"
NUM_LOAD_BALANCE = 0
NUM_MASTERS = 1
NUM_NODES = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false
    config.vagrant.plugins = "vagrant-disksize"

    ### Load Balancer provision
    (1..NUM_LOAD_BALANCE).each do |i|
        config.vm.define "k8s-loadb-#{i}" do |loadb|
            loadb.vm.box = IMAGE_NAME
            loadb.disksize.size = '20GB'
            loadb.vm.network "private_network", ip: "192.168.50.1#{i}"
            loadb.vm.hostname = "k8s-loadb-#{i}"

            # Virtualbox configuration
            loadb.vm.provider "virtualbox" do |v|
                v.memory = 2048
                v.cpus = 2
            end
        end
    end
      
    ### K8s master configuration
    (1..NUM_MASTERS).each do |i|
        config.vm.define "k8s-master-#{i}" do |master|
            master.vm.box = IMAGE_NAME
            master.disksize.size = '20GB'
            master.vm.network "private_network", ip: "192.168.50.#{i + 20}"
            master.vm.hostname = "k8s-master-#{i}"

            # Virtualbox configuration
            master.vm.provider "virtualbox" do |v|
                v.memory = 2048
                v.cpus = 2
            end
        end
    end

    ### K8s worker configuration
    (1..NUM_NODES).each do |i|
        config.vm.define "k8s-node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.disksize.size = '20GB'
            node.vm.network "private_network", ip: "192.168.50.#{i + 30}"
            node.vm.hostname = "k8s-node-#{i}"

            config.vm.provider "virtualbox" do |v|
                v.memory = 1024
                v.cpus = 1
            end
        end
    end

    ### Start ansible
    config.vm.provision "ansible_local" do |ansible|
        ansible.config_file = "ansible.cfg"
        ansible.playbook = "playbook.yml"
        ansible.groups = {
            "loadbalancers" => [
                "k8s-loadb-1"
                ],
            "masters" => [
                "k8s-master-1",
                "k8s-master-2"
                ],
            "nodes" => [
                "k8s-node-1",
                "k8s-node-2"
                ]
        }
    end

end