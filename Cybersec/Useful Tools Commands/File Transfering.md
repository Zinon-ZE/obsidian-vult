## Hosting Files on local
```
cd /tmp
python3 -m http.server 8000
```

## Downloading files on remote

##### Using Wget
```
wget http://$ip:port/filename
```
##### Using curl
```
curl http://$ip:port/filename -o filename
```
##### Using SCP
- Assuming we have SSH credentials to the remote

```
scp filename user@remotehost:/tmp/filename
```

Using Base64
- Encrypt the file on host
```
base64 filename -w 0
```
- Decrypt the file on remote
```
echo base64_string | base64 -d > filename
```

## Validating File Transfers

```
file filename
```

```
md5sum filename
```

