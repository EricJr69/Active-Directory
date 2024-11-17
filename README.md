# Integrated Active Directory Environment in VMware

## Introduction

For this project, VMware was utilized to create an integrated environment that consisted of a Windows Server 2019 Virtual Machine (VM) serving as the Domain Controller (DC) with Active Directory (AD) service. A custom PowerShell script was then executed to populate AD with approximately 1000 fictional users. I then created the following VMs:  Windows 10 Pro, Ubuntu 22.04.4, and Ubuntu 23.10.1. The VMs were then integrated into the AD domain to create a centralized management system for user accounts, computers, and other network resources. Initially Virtual Box was utilized with success and later VMware was used to confirm the ability to reproduce in another environment and test a new release of Ubuntu. 

<br />

## Project Architecture

![AD ARCH](https://i.imgur.com/JW4alB6.png)

<br />

## Technologies and Components Utilized:

- Active Directory
- AD Domain Service
- NAT
- DNS
- Networking
- PowerShell
- Oracle VirtualBox
- VMware
- Windows Server 2019
- Windows 10 Pro
- Ubuntu 22.04.4
- Ubuntu 23.10.1

<br />

## Windows Server 2019 Setup

- Two network adapters were used to separate traffic between external and internal. <br> ![adapters](https://i.imgur.com/WQydx1Ml.png) <br>
- The following roles and services were configured. <br> ![roles](https://i.imgur.com/hDJ4vyfl.png)
- PowerShell script used to generate approximately 1000 fictional users. <br> ![ps scrip](https://i.imgur.com/dhNIRcql.png) <br> ![AD Users](https://i.imgur.com/CG0Pu8vl.png)
- After the configurations were completed, I then took time to explore AD by performing tasks such as: creating groups, organizational units, modifying user permissions, disabling users, deleting users, and so on.  <br> ![AD users and groups](https://i.imgur.com/2FVLj5ml.png)

<br />

## Connecting Windows 10 Pro to AD

- The creation of the Windows VM was straightforward and I encounted no issues.
- During the installation I placed the VM on the internal network and created a local account. 
- I then updated the system's name and joined it to my domain. <br> ![Change name and join domain](https://i.imgur.com/96BzAlim.png)
- From there I verified the VM was connected to AD by logging into several of the random users I had created. <br> ![Change name and join domain](https://i.imgur.com/ToE60Pgm.png)

<br />

## Connecting Ubuntu 22.04.4 to AD

I wanted to expand the lab to include systems other than Windows, so I decided to connect Ubuntu to AD. This was by far the most challenging portion of the lab. One is led to believe that while installing Ubuntu you can simply choose to use AD in the setup, and all will be fine. I never got it to succeed and always ended up getting a failed message. After searching the web, it appears to be a common issue and it took several YouTube videos, official documentation, and various websites to develop a workaround that would allow my VM to connect with AD. <br>

![.conf edit](https://i.imgur.com/sdsE6ppl.png)

#### STEPS

Computer Name: LINUX <br>
Domain Name: mydomain.com <br>
Host Name: LINUX.mydomain.com <br>
Administrator Account: username

  1. Create your Ubuntu VM, update everything, and take a snapshot so you can easily go back if something goes wrong. Network settings will be the same as your  Windows 10 Pro VM.
  2. Open up the command line interface.
  3. Verify you can ping the DC and that the DC can ping back.
  4. Set the host name for the machine: <br> ```sudo hostnamectl set-hostname LINUX.mydomain.com```
  5. Verify the host name: <br> ```hostnamectl```
  6. Install the following: <br> ```sudo apt install sssd-ad sssd-tools realmd adcli```
  7. Discover the DC: <br> ```sudo realm -v discover mydomain.com```
  8. Install the following: <br> ```sudo apt-get install -y krb5.conf```
  9. Edit the krb5.conf file. (Capilization matters, verify default_realm is your DC and add rdns = false) <br> ```sudo nano /etc/krb5.conf``` <br> <br>
![.conf edit](https://i.imgur.com/uTKdqMWl.png)
  10. You might have to install this package: <br> ```sudo apt install krb5-user```
  11. Obtain Kerberous ticket with an account that has admin priviledges: <br> ```kinit username```
  12. Connect the system to the DC: <br> ```realm join -v -U username mydomain.com```
  13. Verify you have can see random users in your AD: <br> ```id user@mydomain.com``` <br> <br> ![access to AD](https://i.imgur.com/vrfmAnDl.png)
  14. Now log off your primary account and pick a random user in AD. <br> ```user@mydomain.com``` <br> <br> ![Linux Logon](https://i.imgur.com/TAy4kSNl.png)
  15. Finally, once Ubuntu does the initial setup, open the command line interface and verify: <br> ```who``` <br> <br> ![who](https://i.imgur.com/s2djFZ3l.png)

## UPDATE

When reviewing this lab I discovered a new release of Ubuntu 23.10.1. So I decided to investigate if the issue of joining an AD domain was still present. The installation ended up being very straightforward and the ability to join an AD domain from set-up was a success. From there I backtracked and tried a newer version of Ubuntu 22.04.4 and discovered the AD issue still persists; however, my workaround still manages to connect to the AD domain. VMware was also utilized to recreate the lab and verify if any issues encountered might be been related to Virtual Box.      

## Conclusion

In this project, VirtualBox was used to create an integrated AD environment. The first VM was Windows Server 2019 that served as the DC, DHCP, and Active Directory populated with approximately 1000 users. Once the networking configurations were complete for the server, I then created three more VMs and connected them to an internal network using Active Directory. I found the initial setup for the Windows VM straightforward; however, I did encounter difficulties by adding the additional goal of connecting an Ubuntu 22.04.4 VM to Active Directory. After researching and testing I was able to find a solution that successfully fixed the problem and have created a project showing how AD can provide centralized management of user accounts, computers, and other network resources. On review I also discovered the release of Ubuntu 23.04.4 had fixed the issue of joing AD on set-up; however, the previous version still has the same difficulties.   

![AD Computers](https://i.imgur.com/ur9L7kXl.png)
![DHCP Reservations](https://i.imgur.com/ur9L7kXl.png)

<br />
