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

- Port 22 — This port is running the `SSH` service, which is relatively secure, and we cannot use it to gain access unless we obtain some credentials, so we'll set it aside for now.
- Port 80 — This port is running an `HTTP server`, which indicates that on this IP, we can find a running website. Let's check it out.

Upon entering the website, we found the webpage below:

Ref 2: Website found

![image](https://github.com/user-attachments/assets/1572eb9e-0bdf-4633-876b-e04fe3672587)

Since this is a relatively simple webpage, let's inspect the source code to try to find something relevant:

Ref 3: Source Code

![image](https://github.com/user-attachments/assets/b1978a85-4270-4fe5-a838-d53e14007981)

When analyzing the source code, we found a note, "Username: R1ckRul3s." For now, we'll write down this username.  

Since we didn't find anything else relevant on the homepage, let's use the Gobuster tool to check if there are any hidden directories.  

Note: the target machine's IP changed during the writeup, but the approach remains the same.  


Command: `gobuster dir -u http://10.10.180.74 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt`

What the command does:  
- `dir -u` = specifies a URL as the target.  
- `-w` = specifies a wordlist to use (insert the full path).  
- `-x` = specifies the directory formats you want to find.

Ref 4: Gobuster results

![image](https://github.com/user-attachments/assets/4146b894-94a0-48e5-a599-e06357637fb1)

We found some interesting directories, but before checking them, let's check if this site has a `/robots.txt` directory.
This directory is common on many websites and is used to inform indexers which files you don't want to be indexed or want to keep hidden from search engine pages.

Ref 5: Robots.txt

![image](https://github.com/user-attachments/assets/1cfb2de9-5ee1-44c8-884f-df4c9b41e8ad)


We confirmed that the page exists, and there's only one word on it. We'll note it down for now. Now, let's access the pages we found earlier.

Ref 6: Login Page

![image](https://github.com/user-attachments/assets/a31a02b9-e123-41b2-8a05-57d8d0eb38a0)

A login page was found; we can use the username we found earlier to try and access it using bruteforce. However, let's first check if the word found in the robots.txt directory is the actual password for this user.

Ref 7: Login Succeeded

![image](https://github.com/user-attachments/assets/ee47965a-bb00-4725-a1e0-084a2180395c)

The word found was indeed the password, and upon logging in, we discovered a command panel. Let's test some commands on it.
Command used: ls
What the command does: shows the files that exist in the current directory.

Ref 8: Exposed files

![image](https://github.com/user-attachments/assets/bbbcac13-29d4-40c7-8304-e7bd79cce388)

after using `ls`, we found two interesting files in this directory. The file named `Sup3rS3cretPickl3Ingred.txt` should contain the first flag, and the file `clue.txt` should contain a hint about where to find the second flag. Let's try to read the first file by navigating to it through the browser, accessing it as if it were a directory with the following URL: http://<insert_ip_here>/Sup3rS3cretPickl3Ingred.txt

Ref 9: First flag found

![image](https://github.com/user-attachments/assets/2461fb27-c88f-4a1b-b211-38fdfb079677)

By navigating to the file, we found the first flag! Now let's follow the same procedure to access the `clue.txt` file.

Ref 10: clue.txt content

![image](https://github.com/user-attachments/assets/19eb60f0-4ec5-497c-b251-deaf365cb0ae)

Upon accessing the file, we found a hint to look in the system files for the other ingredient. Therefore, let's return to the command panel to search more thoroughly in the user's files.
after using the command `ls /home/rick`, we found a file named "Second Ingredient."

Ref 11: File Found

![image](https://github.com/user-attachments/assets/7691a542-34c1-4a13-8ae7-866a64ae2864)

When trying to use the `cat` command, one of the most traditional commands for reading files, we received a message indicating that the command has been disabled by the administrator. Therefore, we must try to access the file in another way.

Ref 12: Command cat disabled

![image](https://github.com/user-attachments/assets/ba0e2294-c678-4248-a026-05dbc3661ec4)

Fortunately, another command similar to `cat`, `less`, is enabled. By using it, we can read the content of the file with the second flag:

Ref 13: Second flag found

![image](https://github.com/user-attachments/assets/7074473f-d780-48a5-a440-263926ed5459)

Now that we have the first two ingredients, the last one should be in the `/root` directory, so let's check if we can access it through the command panel.

Ref 14: access denied

![image](https://github.com/user-attachments/assets/799395cc-f9b3-4427-818d-247a86f974ee)

As expected, we do not have access to the root directory, so we cannot see anything inside it. Let's use the command `sudo -l` to show which commands are allowed to be executed as an administrator for the current user.
Incredibly, due to a configuration error by the system administrator, the current user can use all commands as an administrator without the need for a password. Therefore, we can take advantage of this to gain access to the root directory.

Ref 15: Sudo -l results

![image](https://github.com/user-attachments/assets/d570d29b-4e0a-4df6-b1ad-e59c7fcb0ce9)

By using the command `sudo ls /root`, we found the third and final flag. Now we just need to access it.

Ref 16: File Found

![image](https://github.com/user-attachments/assets/bfccb8ea-e6f4-4d6b-90aa-b166957138d9)

Ref 17: Final Flag found

![image](https://github.com/user-attachments/assets/602c40e5-f59b-4173-963a-3cad2236b085)

