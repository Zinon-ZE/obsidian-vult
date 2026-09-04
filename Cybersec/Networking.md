```
ip a                             # interfaces and addresses
ip link
nmcli device status
nmcli connection show

ss -tulnp                        # what's listening
ss -tan

ping -c4 host
curl -v host:port
traceroute host

cat /etc/hosts                   # local static resolution
cat /etc/resolv.conf             # DNS servers
nmcli con mod <conn> ipv4.dns "8.8.8.8"
nmcli con up <conn>

hostnamectl
```