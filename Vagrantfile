# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/kinetic64"
  config.vm.hostname = "caronte"
  config.vm.network "public_network"
  config.vm.synced_folder ".", "/vagrant"
  config.vm.provision "shell", inline: <<-SHELL
    # install the shadowsocks application
    snap install shadowsocks-rust
    # install tun2socks
    wget https://github.com/jadolg/go-tun2socks/releases/download/v1.0.2/tun2socks -O /usr/bin/tun2socks
    chmod +x /usr/bin/tun2socks
    # copy the proxy configuration 
    cp /vagrant/proxy.json /home/vagrant/
    # setup the proxy service
    cp /vagrant/shadowsocks.service /etc/systemd/system/shadowsocks.service
    systemctl enable shadowsocks
    systemctl restart shadowsocks
    # setup tun2socks
    SERVER=$(cat /etc/proxy.json | jq -r .server) envsubst < /vagrant/tun2socks.service > /etc/systemd/system/tun2socks.service
    systemctl enable tun2socks
    systemctl restart tun2socks
    # setup forwarding
    sysctl -w net.ipv4.ip_forward=1
    echo "y" | ufw reset
    ufw allow 22
    for interface in `ls /sys/class/net`; do if [ $interface != "lo" ] && [ $interface != tun1 ]; then ufw route allow in on $interface out on tun1; fi done
    echo "y" | ufw enable
    # Disable ipv6
    grep net.ipv6.conf.all.disable_ipv6 /etc/sysctl.conf || (echo "net.ipv6.conf.all.disable_ipv6 = 1" | sudo tee -a /etc/sysctl.conf)
    grep net.ipv6.conf.default.disable_ipv6  /etc/sysctl.conf || (echo "net.ipv6.conf.default.disable_ipv6  = 1" | sudo tee -a /etc/sysctl.conf)
    grep net.ipv6.conf.lo.disable_ipv6 /etc/sysctl.conf || (echo "net.ipv6.conf.lo.disable_ipv6 = 1" | sudo tee -a /etc/sysctl.conf)
    # Increase ulimit
    grep fs.file-max /etc/sysctl.conf || (echo "fs.file-max = 65535" | sudo tee -a /etc/sysctl.conf)
    # Update sysctl configuration
    sysctl -p
  SHELL
end
