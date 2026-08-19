# Diagnosis

Tools for diagnosis 
```
systemctl status <name>
```
```
journalctl -u <name>
```
```
nginx -t #testing nginx config
```

useful commands 

```
chmod 
```
```
chown user:group file
```

### Error code on j

| Error code | Explanation               |
| ---------- | ------------------------- |
| 203        | Could not run file        |
| 127        | Command not found         |
| 126        | Found, but not executable |
| 1          | general failure, read log |
|            |                           |
|            |                           |
### System Services path
```
/etc/systemd/system
```



### Disk Diagnosis

```
df -h  #Shows usage
```

```
du -sh folder/files # Find out which fkers is causing shit
```


### User Shell


| Shell State       | Description               |
| ----------------- | ------------------------- |
| /bin/bash         | interactive login allowed |
| /usr/bin/bash     | interactive login allowed |
| /usr/sbin/nologin | interactive login refused |
| /bin/false        | login immediately fails   |
