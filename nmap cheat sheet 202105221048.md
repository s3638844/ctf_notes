tags: #htb #nmap #enumeration
links: [[index]], [[smbclient cheat sheet 202105221408|smbclient cheat sheet]]
source: [nmap cheat sheet](https://www.stationx.net/nmap-cheat-sheet/)

---

# nmap cheat sheet

## CLI examples

### port and service scan example
**run as root**
```
ports=$(nmap -p- --min-rate=1000 -T4 10.10.10.27 | grep ^\[0-9\] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//) 

nmap -sC -sV -p$ports 10.10.10.27
```

##### description
*step 1*
scan all ports (-p-), send packets no slower than 1000 per sec (--min-rate), aggressive (T4).  Grep for the numbers, and clean up the output.
*step 2*
scan ports provided from step 1 using default scripts (-sC), and attempt  to determine the version of the service running on the ports (-sV)
*NB.  This fails, something wrong with the parsing of the first command results in a failure of the second #todo fix this command*

## Cheat Sheet
### Target Specification
| Switch    | Example                          | Description           |
| --------- | -------------------------------- | --------------------- |
|           | nmap 192.168.1.1                 | scan a single IP      |
|           | nmap 192.168.1.1 192.168.2.1     | scan 2 IP             |
|           | nmap 192.168.1.1 - 192.168.1.200 | scan a range          |
|           | nmap scanme.nmap.org             | scan a domain         |
|           | nmap 192.168.1.0/24              | scan using CIDR       |
| -iL       | nmap -iL targets.txt             | scan file targets     |
| -iR       | nmap -iR 100                     | scan 100 random hosts |
| --exclude | nmap --exclude 192.168.1.1       | exclude listed        |
|           |                                  |                       |
 
 ### Scan Techniques
 | Switch | Example         | Descripion                                |
 | ------ | --------------- | ----------------------------------------- |
 | -sS    | nmap \<ip\> -sS | SCP SYN port scan (default)               |
 | -sT    | nmap \<ip\> -sT | TCP connect port scan (default with root) |
 | -sU    | nmap \<ip\> -sU | UDP port scan                             |
 | -sA    | nmap \<ip\> -sA | TCP ACK Scan                              |
 | -sW    | nmap \<ip\> -sW | TCP windows port scan                     |
 | -sM    | nmap \<ip\> -sM | SCP Maimon port scan                      |

### Host Discovery
| Switch | Example                         | Description                             |
| ------ | ------------------------------- | --------------------------------------- |
| -sL    | nmap 192.168.1.1-3 -sL          | No Scan List targets only               |
| -sn    | nmap 192.168.1.1/24 -sn         | disable port scan. host disco only      |
| -Pn    | nmap 192.168.1.1-5 -Pn          | Disable host disco, port scan only      |
| -PS    | nmap 192.168.1.1-5 -PS 22-25,80 | TCP SYN disco on port x. p80 by default |
| -PA    | nmap 192.168.1.1-5 -PA 22-25,80 | TCP ACK "    "                          |
| -PU    | nmap 192.168.1.1-5 -PU 53       | UDP dico on port x. p40125 by default   |
| -PR    | nmap 192.168.1.1-1/24 -PR       | ARP disco on local net                  |
| -n     | nmap 192.168.1.1 -n             | never do DNS res                                        |

### Port Specification
| Switch      | Example                             | Description                                                      |
| ----------- | ----------------------------------- | ---------------------------------------------------------------- |
| -p          | nmap 192.168.1.1 -p 21              | port scan for port x                                             |
| -p          | nmap 192.168.1.1 -p 20-100          | port scan range                                                  |
| -p          | nmap 192.168.1.1 -p U:53,T:21-25,80 | Scan TCP and UDP as spec                                         |
| -p-         | nmap 192.168.1.1 -p-                | Scan all ports ==takes forever==                                 |
| -p          | nmap 192.168.1.1 -p http.https      | port scan from service name                                      |
| -F          | nmap 192.168.1.1 -F                 | fast port scan (top 100)                                         |
| --top-ports | nmap 192.168.1.1 --top ports 2000   | port scan the top x ports                                        |
| -p- 65535   | nmap 192.168.1.1 -p-65535           | Leaving off initial port in range makes the scan start at port 1 |
| -p0-        | nmap 192.168.1.1 -p0-               | leavi off end port in range, scans through to 65535              |

### Timing and Performance
| Switch | Example              | Description                                                                                |
| ------ | -------------------- | ------------------------------------------------------------------------------------------ |
| -T0    | nmap 192.168.1.1 -T0 | Paranoid (0) Intrusion Detection System evasion                                            |
| -T1    | nmap 192.168.1.1 -T1 | Sneaky (1) Intrusion Detection System evasion                                              |
| -T2    | nmap 192.168.1.1 -T2 | Polite (2) slows down the scan to use less bandwidth and use less target machine resources |
| -T3    | nmap 192.168.1.1 -T3 | Normal (3) which is default speed                                                          |
| -T4    | nmap 192.168.1.1 -T4 | Aggressive (4) speeds scans; assumes you are on a reasonably fast and  reliable network    |
| -T5    | nmap 192.168.1.1 -T5 | Insane (5) speeds scan; assumes you are on an extraordinarily fast network                 |

## SMB related examples
### nmap - Enum Users
 nmap -p 445 --script smb-enum-users \<target\> --script-args smbuser=username,smbpass=password,smbdomain=domain
 nmap -p 445 --script smb-enum-users \<target\> --script-args smbuser=username,smbhash=LM:NTLM,smbdomain=domain
   
 nmap --script smb-enum-users.nse --script-args smbusername=User1,smbpass=Pass@1234,smbdomain=workstation -p445 192.168.1.10
   
 nmap --script smb-enum-users.nse --script-args smbusername=User1,smbhash=aad3b435b51404eeaad3b435b51404ee:C318D62C8B3CA508DD753DDA8CC74028,smbdomain=mydomain -p445 192.168.1.10<br>
 
 ### nmap - Enum Groups
 nmap -p 445 --script smb-enum-groups \<target\> --script-args smbuser=username,smbpass=password,smbdomain=domain
 nmap -p 445 --script smb-enum-groups \<target\> --script-args smbuser=username,smbhash=LM:NTLM,smbdomain=domain
 
 ### nmap - Enum Shares
 nmap -p 445 --script smb-enum-shares \<target\> --script-args smbuser=username,smbpass=password,smbdomain=domain
 nmap -p 445 --script smb-enum-shares \<target\> --script-args smbuser=username,smbpass=LM:NTLM,smbdomain=domain
 
 ### nmap - OS Discovery
 nmap -p 445 --script smb-os-discovery \<target\>
 
 ### nmap - SMB Vulnerabilities on Windows
 nmap -p 445 --script smb-vuln-ms06-025 target-IP <br>
 nmap -p 445 --script smb-vuln-ms07-029 target-IP <br>
 nmap -p 445 --script smb-vuln-ms08-067 target-IP <br>
 nmap -p 445 --script smb-vuln-ms10-054 target-IP <br>
 nmap -p 445 --script smb-vuln-ms10-061 target-IP <br>
 nmap -p 445 --script smb-vuln-ms17-010 target-IP <br>
 -- Always check for updated list on https://nmap.org/nsedoc/scripts/
 
 ### map - Brute Force Accounts (be aware of account lockout!)
 nmap ???p 445 --script smb-brute ???script-args userdb=user-list.txt,passdb=pass-list.txt target-IP