# Tryhackme - Pickle Rick (english version)

## Objective

In this CTF, we must find 3 Flags on the target machine, which in this case are 3 secret ingredients.

### Skills Used

- Port Scanning to detect active services
- Source code analysis
- directory scanning (Bruteforce)
- privilege escalation

### Tools Used

- NMAP for Port scanning
- Gobuster for directory scanning and bruteforce
  
## Steps

In this CTF, we must find 3 Flags on the target machine, which in this case are 3 secret ingredients.

First, let's do a brief reconnaissance of the machine using NMAP.

Command used: `nmap -T4 -sC -sV -A -p- 10.10.180.74`

What the command does:

`-T4` = Sets the scan speed

`-sC` = Performs a scan with the default script

`-sV` = Shows the version of services running on the target machine

`-A`= Performs an aggressive scan

`-p-` = Scans all ports

IF THE SCAN IS TAKING TOO LONG, THE SIMPLIFIED VERSION OF NMAP CAN ALSO BE USED:

Ref 1: Simple NMAP

![image](https://github.com/user-attachments/assets/8ccabdb1-c558-4708-b6b7-eec17686d5fa)

When scanning the target machine, we found two relevant open ports:

- Port 22 — This port is running the SSH service, which is relatively secure, and we cannot use it to gain access unless we obtain some credentials, so we'll set it aside for now.
- Port 80 — This port is running an HTTP server, which indicates that on this IP, we can find a running website. Let's check it out.

Upon entering the website, we found the webpage below:

Ref 2: Website found

![image](https://github.com/user-attachments/assets/1572eb9e-0bdf-4633-876b-e04fe3672587)

Since this is a relatively simple webpage, let's inspect the source code to try to find something relevant:

Ref 3: 
![image](https://github.com/user-attachments/assets/b1978a85-4270-4fe5-a838-d53e14007981)

When analyzing the source code, we found a note, "Username: R1ckRul3s." For now, we'll write down this username.  

Since we didn't find anything else relevant on the homepage, let's use the Gobuster tool to check if there are any hidden directories.  

Note: the target machine's IP changed during the writeup, but the approach remains the same.  


Command: `gobuster dir -u http://10.10.180.74 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt`

What the command does:  
- `dir -u` = specifies a URL as the target.  
- `-w` = specifies a wordlist to use (insert the full path).  
- `-x` = specifies the directory formats you want to find.









