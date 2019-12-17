N_MACHINES=2
IP_PREFIX = '192.168.35'
IP_PREFIX_SEC = '192.168.34'
IP_SUFFIX_BASE = 10

def ip_addr(i)
  "#{IP_PREFIX}.#{IP_SUFFIX_BASE + i}"
end

def ip_addr_sec(i)
  "#{IP_PREFIX_SEC}.#{IP_SUFFIX_BASE + i}"
end

def hostname(i)
  "cilium#{i}"
end

Vagrant.configure(2) do |config|
    (1..N_MACHINES).each do |i|
        hostname = hostname(i)
        config.vm.define hostname do |host|
            host.vm.provider :virtualbox do |vb|
                vb.name = hostname
                vb.memory = 4096
                vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
                vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
            end

            host.vm.box = "cilium/ubuntu-next"
            host.vm.box_version = "32"
            host.vm.network "private_network", ip: ip_addr(i)
            host.vm.network "private_network", ip: ip_addr_sec(i)
            host.vm.synced_folder ".", "/vagrant", disabled: true
            host.vm.synced_folder "/home/brb/sandbox/gopath/src/github.com/cilium/cilium", "/cilium", type: "nfs"
        end
    end
    config.vm.provision "install-k8s", type: "shell", inline:<<-SHELL
      apt-get update && apt-get install -y git apt-transport-https ca-certificates curl software-properties-common
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
      add-apt-repository \
        "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) \
        stable"
      apt-get update
      apt-get install -y docker-ce || true
      sudo apt-get update && sudo apt-get install -y apt-transport-https curl
      curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
      cat <<FOOBAR | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
FOOBAR
      sudo apt-get update
      sudo apt-get install -y kubelet kubeadm kubectl
      sudo apt-mark hold kubelet kubeadm kubectl
    SHELL
end
