Install openvpn:
wget https://raw.githubusercontent.com/0dataexpert0/sh/master/openvpn/openvpn.sh && bash openvpn.sh
---
fixed private ip's for openvpn clients
add "client-config-dir ccd" to /etc/openvpn/server/server.conf
sudo mkdir /etc/openvpn/ccd
sudo touch /etc/openvpn/ccd/client1
echo "ifconfig-push 10.10.10.10 255.255.255.255" > /etc/openvpn/ccd/client1 
---
Allow only connection to openvpn server and block other internet
# create chains for iptables
sudo iptables -N ALLOWVPN
sudo iptables -N BLOCKALL
# allow access for the interfaces loopback, tun, and tap
sudo iptables -A OUTPUT -o tun+ -j ACCEPT;
sudo iptables -A OUTPUT -o tap+ -j ACCEPT;
sudo iptables -A OUTPUT -o lo+ -j ACCEPT;
# route outgoing data via our created chains
sudo iptables -A OUTPUT -j ALLOWVPN;
sudo iptables -A OUTPUT -j BLOCKALL;
# allow connections to certain IP addresses with no active VPN
sudo iptables -A ALLOWVPN -d 1.2.3.4 -j ACCEPT
sudo iptables -A ALLOWVPN -d 5.6.7.8 -j ACCEPT
# block all disallowed connections
sudo iptables -A BLOCKALL -j DROP
----
