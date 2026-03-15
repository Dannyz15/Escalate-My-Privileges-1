# Escalate-My-Privileges-1
Privilege Escalation lab task using Kali Linux for cybersecurity learning.

## Objective
- Discover target machine
- Enumerate services
- Exploit web vulnerabilities
- Gain user access
- Perform privilege escalation to root

---

# 1 Network Scanning

Tool used: netdiscover

Command:
netdiscover

Result:
Discovered target IP: 192.168.56.102

Screenshot:
screenshots/01-network-scan.png

---

# 2 Interface Configuration

Command:
ifconfig

Screenshot:
screenshots/02-ifconfig.png

---

# 3 Port Scanning

Command:
nmap -sP 192.168.56.0/24

Result:
Target host discovered.

Screenshot:
screenshots/03-nmap-scan.png

---

# 4 Service Enumeration

Command:
nmap -sV -p- 192.168.56.102

Open Ports:
80 - HTTP
111 - RPCBind
2049 - NFS

Screenshot:
screenshots/04-service-scan.png

---

# 5 Web Directory Enumeration

Tool:
DIRB

Command:
dirb http://192.168.56.102

Found directories:
- index.html
- robots.txt
- phpinfo.php

Screenshot:
screenshots/05-dirb-scan.png

---

# 6 Information Disclosure

Accessed:
http://192.168.56.102/phpinfo.php

Information gathered:
- PHP Version
- Server configuration

Screenshot:
screenshots/06-phpinfo.png

---

# 7 Reverse Shell

Command used to gain shell access.

Screenshot:
screenshots/07-reverse-shell.png

---

# 8 User Access

User discovered:
armour

Screenshot:
screenshots/08-user-access.png

---

# 9 Password Hash

Hash discovered:
b7bc8489abe360486b4b19dbc242e885

MD5 cracked password:
root1

Screenshot:
screenshots/09-password-hash.png

---

# 10 Privilege Escalation

Command:
sudo bash

Result:
Root access obtained.

Screenshot:
screenshots/10-root-access.png

---

# Final Proof

Proof file:
cat /root/proof.txt

Result:
Best of Luck
628433556e49f976bab2cc04948a22fe4

---
