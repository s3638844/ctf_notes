tags: #htb #enumeration #smb
links: [[index]], [[nmap cheat sheet 202105221048 | nmap cheat sheet]] 
 
---

# smbclient cheat sheet
This is a list of useful commands/tricks using smbclient, enum4linux and nmap smb scripts - very useful on a pentesting
https://sharingsec.blogspot.com

 ## List shares on a machine using NULL Session
 
` smbclient -L <target-IP>`
 
 ## List shares on a machine using a valid username + password
` smbclient -L <target-IP> -U username%password`
 
 ## Connect to a valid share with username + password
` smbclient //targetshare$ -U username%password`
  
 ## List files on a specific share
` smbclient //targetshare$ -c 'ls' password -U username`
 
 ## List files on a specific share folder inside the share
 `smbclient //targetshare$ -c 'cd folder; ls' password -U username`
 
 ## Download a file from a specific share folder
 `smbclient //targetshare$ -c 'cd folder;get desired_file_name' password -U username`
  
 ## Copy a file to a specific share folder
` smbclient //targetshare$ -c 'put /var/www/my\_local\_file.txt .\target\_folder\target\_file.txt' password -U username`
 
 ## Create a folder in a specific share folder
` smbclient //targetshare$ -c 'mkdir .\target\_folder\new\_folder' password -U username`
 
 ## Rename a file in a specific share folder
` smbclient //targetshare$ -c 'rename current\_file.txt new\_file.txt' password -U username`
 
 ## enum4linux - General enumeration - anonymous session 
` enum4linux -a target`
 ## enum4linux - General enumeration - authenticated session
` enum4linux -a target -u user -p pass`
 
 ## enum4linux - Users enumeration
` enum4linux -u user -p pass -U target`
 
 ## enum4linux - Group and members enumeration 
` enum4linux -u user -p pass -G target`
 
 ## enum4linux - Password policy
` enum4linux -u user -p pass -P target`
 
 # NMAP 4 SMB
 ![[nmap cheat sheet 202105221048#SMB related examples]]