#### Directory/File Enumeration
```
gobuster dir -u http://$ip/ -w /usr/share/seclists/Discovery/Web-Content/common.txt
```
#### DNS Subdomain Enumeration
```
gobuster dns -d $ip -w /usr/share/SecLists/Discovery/DNS/namelist.txt
```
#### Banner Grabbing / Web Server Headers
```
curl -IL $ip
```
```
whatweb $ip
```
