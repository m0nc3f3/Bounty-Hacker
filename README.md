
# Bounty Hacker CTF: Write-up

_Author: moncef fennan — Date: 2025-11-03_
tags:

- ctf
    
- tryhackme
    
- nmap
    
- enumeration
    
- ssh
    
- ftp
    
- brute-force
    
- privesc
    

## Table of Contents

- [🚀 Deploy & Scan](#-deploy--scan)
    
- [🕵️ Service Enumeration & Flag Hunting](#service-enumeration-flag-hunting)
    
    - [Finding the task list](#finding-the-task-list)
        
    - [Brute-force SSH](#brute-force-ssh)
        
    - [User login & flag](#user-login--flag)
        
    - [Privilege escalation & root flag](#privilege-escalation--root-flag)
        
- [🏁 Conclusion](#-conclusion)
    

---

## 🚀 Deploy & Scan

`Deployed the machine and ran a port scan to see what’s open.`

<img src="/images/nmap-scan.png" alt="nmap-scan">

(No answers needed here.)


---
<a id="service-enumeration-flag-hunting"></a>
## 🕵️ Service Enumeration & Flag Hunting

### Finding the task list

`We tried gobuster against port 80 but hit dead ends. So we checked FTP as anonymous and found two files.`

<img src="/images/gobuster rabbit hole.png" alt="gobuster scan">

<img src="/images/ftp access.png" alt="ftp">

> [!NOTE]  
> **Question:** Who wrote the task list?  
> **Answer:** `lin`

`Steps: login to FTP anonymously, download the files, one contains the task list which shows `lin` as the author.`



---

### Brute-force SSH

`The second FTP file (`locks.txt`) contains some passwords. From our nmap scan, we know SSH is open.`

<img src="/images/cat locks.txt.png" alt="locks.txt">

> [!NOTE]  
> **Question:** What service can you brute-force with the text file found?  
> **Answer:** `SSH`

`We used `hydra` with username `lin` and the password list to brute-force the SSH login.`

<img src="/images/password found.png" alt="hydra">

> [!NOTE]  
> **Question:** What is the user's password?  
> **Answer:** `RedDr4gonSynd1cat3`

---

### User login & flag

`SSH into the machine using `lin:RedDr4gonSynd1cat3` and check the Desktop.`

<img src="/images/user.txt.png" alt="first flag">

> [!NOTE]  
> **Question:** What is the user flag?  
> **Answer:** `THM{CR1M3_SyNd1C4T3}`

---

### Privilege escalation & root flag

`Check allowed sudo commands:`

```bash
sudo -l
```

`We saw we can execute `/bin/tar` without a password. Googling `sudo tar privesc` shows the command to escalate.`

```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

<img src="/images/root.txt.png" alt="root flag">

> [!NOTE]  
> **Question:** What is the root flag?  
> **Answer:** `THM{80UN7Y_h4cK3r}`

---

## 🏁 Conclusion

`This box was fun and straightforward:`

- Enumerate FTP anonymously to find credentials and task info.
    
- Brute-force SSH using the password list found on FTP.
    
- Grab the user flag.
    
- Check `sudo -l` and escalate using a known tar exploit.
    
- Read the root flag.
    

A solid mix of enumeration, brute-force, and privesc.
