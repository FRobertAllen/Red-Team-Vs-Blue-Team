# Red-Team Vs Blue-Team

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

### Identify the target IP
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


### Scan Target IP
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

### Inspecting the Webserver 
Let's navigate the webserver and take a look.

![webserver](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/webserver.PNG)

In the company blog directory we can see a third username Ryan, who probably have the highest privilege of access as the CEO

![company_blog](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/company_blog.PNG)

In the *company folders* directory, we can see reference to a "*secret_folder*" for documents within this directory, which is now a target for this Penetration Test.

![company-fold_company-culture](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/company-fold_company-culture.PNG)

The *meet_our_team* folder confirms the three users, and each document has references the *secret_folder*.

![meet our team](https://github.com/FRobertAllen/Red-Team-Vs-Blue-Team/blob/main/Images/meet%20our%20team.PNG)


Ashton's login screen, we need his password to gain access to his *secret_folder*.
