# Integrated Active Directory Environment in VMware

## Introduction

This project demonstrates the creation of an integrated Active Directory (AD) environment using VMware and VirtualBox. A Windows Server 2019 Virtual Machine (VM) was set up as the Domain Controller (DC) with Active Directory service, and a custom PowerShell script was executed to populate AD with approximately 1000 fictional users. Three additional VMs were created: Windows 10 Pro, Ubuntu 22.04.4, and Ubuntu 23.10.1, and integrated into the AD domain to create a centralized management system for user accounts, computers, and other network resources. Initially, difficulties were encountered when attempting to connect an Ubuntu 22.04.4 VM to Active Directory, but a solution was found and implemented. A review of the lab revealed that Ubuntu 23.10.1 resolved the AD domain joining issue, while Ubuntu 22.04.4 still required the workaround solution.

<br />

## Project Architecture

![AD ARCH](https://i.imgur.com/JW4alB6.png)

<br />

## Technologies and Components Utilized:

- Active Directory
- AD Domain Service
- DNS
- NAT
- Networking
- Oracle VirtualBox
- PowerShell
- Ubuntu 22.04.4
- Ubuntu 23.10.1
- VMware
- Windows 10 Pro
- Windows Server 2019

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

To expand the lab's scope, I attempted to connect Ubuntu to Active Directory (AD). However, this proved to be the most challenging part of the project. Despite the apparent simplicity of selecting AD during Ubuntu installation, I consistently encountered failure messages. After conducting extensive research online, including YouTube videos, official documentation, and various websites, I discovered that this issue is common and developed a workaround to successfully connect my Ubuntu VM to AD. <br>

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

During a review of this lab, I discovered a new release of Ubuntu 23.10.1 and investigated whether the issue of joining an AD domain still existed. The installation was straightforward, and joining an AD domain from setup was successful. I then tested a newer version of Ubuntu 22.04.4 and found that the AD issue persists, although my workaround still enables connection to the AD domain. Additionally, I utilized VMware to recreate the lab and verify that the issues encountered were not related to VirtualBox.

## Conclusion

This project created an integrated AD environment using VirtualBox. The first VM, Windows Server 2019, served as the DC, DHCP, and Active Directory, populated with approximately 1000 users. After completing the server's networking configurations, I created three more VMs and connected them to an internal network using Active Directory. While the initial Windows VM setup was straightforward, I encountered difficulties connecting an Ubuntu 22.04.4 VM to Active Directory. After researching and testing, I found a solution that successfully fixed the problem, demonstrating how AD provides centralized management of user accounts, computers, and network resources. Upon review, I also discovered that Ubuntu 23.10.1 resolved the AD domain joining issue, whereas the previous version still requires a workaround.

![AD Computers](https://i.imgur.com/ur9L7kXl.png)
![DHCP Reservations](https://i.imgur.com/ur9L7kXl.png)

<br />
