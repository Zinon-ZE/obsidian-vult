**Script Related**
```
locate /usr/share/nmap/scripts
```
```
--script <category>
```

| **Category** | **Description**                                                                                                                         |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| `auth`       | Determination of authentication credentials.                                                                                            |
| `broadcast`  | Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans. |
| `brute`      | Executes scripts that try to log in to the respective service by brute-forcing with credentials.                                        |
| `default`    | Default scripts executed by using the `-sC` option.                                                                                     |
| `discovery`  | Evaluation of accessible services.                                                                                                      |
| `dos`        | These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services.              |
| `exploit`    | This category of scripts tries to exploit known vulnerabilities for the scanned port.                                                   |
| `external`   | Scripts that use external services for further processing.                                                                              |
| `fuzzer`     | This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.     |
| `intrusive`  | Intrusive scripts that could negatively affect the target system.                                                                       |
| `malware`    | Checks if some malware infects the target system.                                                                                       |
| `safe`       | Defensive scripts that do not perform intrusive and destructive access.                                                                 |
| `version`    | Extension for service detection.                                                                                                        |
| `vuln`       | Identification of specific vulnerabilities.                                                                                             |
| `banner`     | Banner grabbing similar to netcat                                                                                                       |


***Scan Network Range***

```
rustscan -a 192.168.1.6 -r 1-65535
```
```
sudo nmap 10.129.2.0/24 -sn 
```
***Packet tracing to determine OS ***
```
sudo nmap $ip -sn  -PE --packet-trace --disable-arp-ping 


```
Can determine OS by check the TTL values
**64**: Unix
**128**: Windows
**255**: Network equipment
####Web Application Directories enum unknonwn
**Using scripts**
```
nmap -sV --script=http-enum $ip
```

| Useful Flags         |                                                      |
| -------------------- | ---------------------------------------------------- |
| `-sU`                | Performs a UDP scan.                                 |
| `-Pn`                | Disables ICMP Echo requests.                         |
| `-n`                 | Disables DNS resolution.                             |
| `--disable-arp-ping` | Disables ARP ping.                                   |
| `--packet-trace`     | Shows all packets sent and received.                 |
| `-p 137`             | Scans only the specified port.                       |
| `--reason`           | Displays the reason a port is in a particular state. |
| ``` -sS```           | Performs TCP scan                                    |
| ```-sV```            | Performs a service scan.                             |
# NMAP Evasion 

**Decoy IP to avoid detection** *RND random address 5* 
```
-D RND:5
```

**Specifying IP address**

| `-S IP` | Specify target         |
| ------- | ---------------------- |
| `-e`    | **Specific interface** |

**DNS PROXY**

| `--dns-server <ns>,<ns>` | Tells nmap to use custom dns |
| ------------------------ | ---------------------------- |
| `--source-port 52 `      | Specifying port 52(dns)      |

# Result-Report

While we run various scans, we should always save the results. We can use these later to examine the differences between the different scanning methods we have used. `Nmap` can save the results in 3 different formats.

- Normal output (`-oN`) with the `.nmap` file extension
- Grepable output (`-oG`) with the `.gnmap` file extension
- XML output (`-oX`) with the `.xml` file extension
## Style sheets
```
&& xsltproc target.xml -o target.html
```



# Useful phrasing 

**Listing Ports only seprated by comma**
```
| grep "/tcp" | cut -d '/' -f 1 | paste -sd, -
```

