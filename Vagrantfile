# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/kinetic64"
  config.vm.network "public_network"
  config.vm.synced_folder ".", "/vagrant"
  config.vm.provision "shell", inline: <<-SHELL
    # update the system
    apt update && apt upgrade -y
    # install the shadowsocks application
    apt install shadowsocks-libev -y
    # install tun2socks
    wget https://github.com/jadolg/go-tun2socks/releases/download/v1.0.2/tun2socks -O /usr/bin/tun2socks
    chmod +x /usr/bin/tun2socks
    # copy the proxy configuration 
    cp /vagrant/proxy.json /etc/
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
  SHELL
end
