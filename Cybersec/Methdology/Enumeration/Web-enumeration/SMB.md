`Server Message Block` 

Port - `445`
alternative ports - `137`, `138`, and `139`

Default config - `/etc/samba/smb.conf` 

``` listing_users
smbclient -N -L //$ip
```

#### RPCclient
```
rpcclient -U "" $ip
```

| **Query**                 | **Description**                                                    |
| ------------------------- | ------------------------------------------------------------------ |
| `srvinfo`                 | Server information.                                                |
| `enumdomains`             | Enumerate all domains that are deployed in the network.            |
| `querydominfo`            | Provides domain, server, and user information of deployed domains. |
| `netshareenumall`         | Enumerates all available shares.                                   |
| `netsharegetinfo <share>` | Provides information about a specific share.                       |
| `enumdomusers`            | Enumerates all domain users.                                       |
| `queryuser <RID>`         | Provides information about a specific user.                        |

**Brute forcing RID**
```
for i in $(seq 500 1100);do rpcclient -N -U "" $ip -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done
```
https://github.com/SecureAuthCorp/impacket/blob/master/examples/samrdump.py


##### Alternative to RPCclient
https://github.com/ShawnDEvans/smbmap
```
smbmap -H 10.129.14.128
```
https://github.com/byt3bl33d3r/CrackMapExec
```
crackmapexec smb 10.129.14.128 --shares -u '' -p ''
```


https://github.com/cddmp/enum4linux-ng

```
git clone https://github.com/cddmp/enum4linux-ng.git
cd enum4linux-ng
pip3 install -r requirements.txt
```
Usage
```
./enum4linux-ng.py $ip -A
```
