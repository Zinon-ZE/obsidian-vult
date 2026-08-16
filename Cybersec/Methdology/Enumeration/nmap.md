```
locate scripts/citrix
```

#### Scan Network Range
```
sudo nmap 10.129.2.0/24 -sn 
```
### Packet tracing to determine OS 
```
sudo nmap $ip -sn  -PE --packet-trace --disable-arp-ping 
```
