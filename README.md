# Caronte

This virtual machine acts as a router that forwards everything through a shadowsocks connection.

# Dependencies
 - Vagrant (https://www.vagrantup.com/)

# How to use
1. Fill `proxy.json` with your shadowsocks configuration.
    - `local_port` needs to be 1080.
    - `server` needs to be an IP address.
2. Execute `vagrant up`
3. Use the IP address of the virtual machine as default gateway  