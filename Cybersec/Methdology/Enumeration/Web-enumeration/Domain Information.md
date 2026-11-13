subdomain gathering - https://crt.sh
**Useful commands to sue with crt**

*List Subdomains*
``` 
curl -s https://crt.sh/\?q\=EXAMPLE.COM\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u
```
*Grab Ip of domains*
```
for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done
```
*Shodan -IP list*
```
for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
ZinonIsHere@htb[/htb]$ for i in $(cat ip-addresses.txt);do shodan host $i;done

```
*DNS Records*
```
dig any EXAMPLE.COM
```

*DNS AXFR ZONE TRANSFER*
```
dig axfr domain_name @ip
```



DNS enum
https://github.com/fwaeytens/dnsenum


Third-party infrastructure gathering 
domain glass - https://domain.glass (cloudflare - Gateway info)

*Info gathering for **Cloud Providers*** - https://buckets.grayhatwarfare.com/


#### Subdomain Brute Forcing
```
for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.domain.name @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```
https://github.com/fwaeytens/dnsenum