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

#ETC_HOSTS_ENTRIES = (1..N_MACHINES).map { |i| "#{ip_address(i)} #{hostname(i)}\n"}.join('')

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
            host.vm.box_version = "30"
            host.vm.network "private_network", ip: ip_addr(i)
            host.vm.network "private_network", ip: ip_addr_sec(i)
            host.vm.synced_folder ".", "/vagrant", disabled: true
            host.vm.synced_folder "/home/brb/sandbox/gopath/src/github.com/cilium/cilium", "/cilium", type: "nfs"
        end
    end
end
