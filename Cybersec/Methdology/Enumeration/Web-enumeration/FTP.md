`TCP PORT 20( Could be otheer port)- File transfer`
`**TCP PORT 21 - Control Channel(Commands and responses)**`

FTP Server return code - https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes
FTP Command Cheat sheet - https://web.archive.org/web/20230326204635/https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/


config folder = `/etc/vsftpd.conf`
deny users = `/etc/ftpusers`

Standard solution to restrict logins - https://en.wikipedia.org/wiki/Fail2ban

```
sudo nmap --script-updatedb
```

**FTP Related scripts**
```
find / -type f -name ftp* 2>/dev/null | grep scripts
```
scripts
- ftp-anon
- ftp-syst
--script-trace

FTP can be interacted with `netcat or telnet`


```
openssl s_client -connect 10.129.14.136:21 -starttls ftp
```