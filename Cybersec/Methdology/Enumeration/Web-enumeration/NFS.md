Config file - `/etc/exports`
port - `111,2049`

```
--script nfs* 
```

Showing avaliable NFS shares
```
showmount -e $ip
```

Mounting NFS Share
```
make new directory
```

```
sudo mount -t nfs $ip:/ ./new_directory/ -o nolock
```
