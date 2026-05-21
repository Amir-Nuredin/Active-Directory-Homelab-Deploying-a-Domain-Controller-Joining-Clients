# Active Directory Homelab: Deploying a Domain Controller and Joining Clients

## Objective

This lab/project aims to develop practical skills in deploying and managing a Windows-based enterprise network using Active Directory Domain Services (AD DS). In this lab, I focused on transforming a standalone virtualized environment into a centralized domain-based infrastructure. Through the use of Windows Server 2022, I installed and configured Active Directory, promoted the server to a Domain Controller, and implemented DNS to support domain operations. I then created and organized domain resources, including Organizational Units (OUs), users, and security groups, to simulate real-world identity and access management practices. Additionally, we joined a Windows 10 client machine to the domain to demonstrate centralized authentication and domain communication. The final step in this lab involved verifying proper functionality through authentication testing, DNS resolution, and network connectivity checks, ensuring a fully operational enterprise-style environment.

### Skills Learned

This project has provided hands-on experience with the following security solutions/skills:

- Active Directory Domain Services (AD DS) deployment and configuration
- Windows Server 2022 installation and Domain Controller promotion
- Creation and management of Organizational Units (OUs), users, and security groups
- Domain joining and centralized authentication with Windows 10 clients
- Understanding of identity and access management in enterprise environments
- Network configuration, including static IP addressing and internal communication

### Tools Used

- Windows Server 2022 (Desktop Experience)
- Active Directory Domain Services (AD DS)
- DNS Server (Windows Server)
- Windows 10 (Domain-Joined Client VM)
- VirtualBox (Virtualization Platform)
- Server Manager (Windows Server Administration)
- Command-Line Tools (ping, nslookup, ipconfig, whoami, etc.)

## Current Lab Environment Architecture/Setup

Below are screenshots of my lab Architecture before and after this week's lab/project (IM1&2). The new additions to this week's lab included a new Windows 2022 Server VM, which included Active Directory Domain Services and Server Manager. 

<img width="700" height="400" alt="image" src="https://github.com/user-attachments/assets/7cc09cb1-84e4-44fe-8081-02dbdaf1a5af" /> <img width="700" height="400" alt="image" src="https://github.com/user-attachments/assets/d8356180-ca33-4c81-9638-eeccc6ed3fb3" />

IM1&2: Homelab Architecture Before and After this week's Lab/Project

## Steps/Procedure

### Part 1: Download Required Software

1. **DOWNLOAD WINDOWS SERVER ISO IMAGE.** The first step of this project was to download the software I needed to complete this lab. The only thing I needed to download was a Windows Server 2022 ISO image so that I could later attach it to the VM. To do this, I went to the website "https://www.microsoft.com/en-us/evalcenter/" and searched for Windows Server 2022 (IM3). Once it was located, I downloaded it.
2. **VERIFYING EXISTING SOFTWARE.** Before going forward, I made sure that I had all the necessary VMs working so I wouldn't run into any issues. I needed to check if I had the following VMs working: pfSense, Windows 10, Kali Linux, and Ubuntu. They were all good, so we could move on

<img width="800" height="400" alt="Screenshot 2026-05-18 175600" src="https://github.com/user-attachments/assets/4b279bb6-573b-4254-a5a2-caeea8450cb6" />


IM3: Downloading Windows Server 2022 ISO Image

### Part 2: Creating Windows Server VM

Now that I downloaded the ISO image, I had to create the VM for the Windows Server 2022 and configure it with the right amount of storage, RAM, and also correct networking. The stats for the VM are in IM4. One important thing I had to make sure I configured correctly was that the Windows Server 2022 VM was on the same LAN as all my other VMs, so that they could communicate, and especially later when the Windows 10 VM needs to join the domain. After this I attached the ISO and loaded up the VM. 

<img width="366" height="500" alt="Screenshot 2026-05-18 175810" src="https://github.com/user-attachments/assets/2329ad9b-f7b6-45ea-b482-4561559bf234" />

IM4: Windows Server 2022 VM setup

### Part 3: Installing Windows Server

Now that the VM was set up and the ISO was attached, I could Install an setup the Windows Server. It was the typical Windows install process. I chose the langage, chose the type of desktop (Windows Server 2022 Standard (Desktop Experience)), and Set a password for the admin account as this is a administration type of server (IM5). Once that was all set and done, the install process started and I let it finish (IM6). Once it Installed, I logged in with the credentials that I set and the VM loaded up with Server Manager being the first application to pop up (IM7). 

<img width="600" height="300" alt="Screenshot 2026-05-18 181650" src="https://github.com/user-attachments/assets/8ea0d935-2183-4ec5-9bca-b7d1a9a4d86b" />

IM5: Setting up Login Credentials for Windows Server Admin Account

<img width="600" height="300" alt="Screenshot 2026-05-18 180447" src="https://github.com/user-attachments/assets/19f55616-5587-41bb-bf46-fef3436a465a" />

IM6: Installation Process for Windows Server VM

<img width="600" height="400" alt="Screenshot 2026-05-18 181923" src="https://github.com/user-attachments/assets/1f99c7c7-0c47-49ff-8c02-e1a3888c2a44" />

IM7: Windows Server VM loaded up with Server Manager

### Part 4: Configuring Networking

This step before starting the real AD work was crucial as if I didn't set this up correctly, the domain join later would fail and the Windows Server VM wouldn't be able to communicate with the Windows 10 VM. 

1. **CONFIGURING SERVER WITH STATIC IP.** The reason for configuring a static IP instead of using DHCP is because the Domain Controller (will be discussed later) must always be reachable at the same address. If DHCP was used, the IP address would change everyday, causing the network configuration and DNS to break. For this reason, I configured the server with a static IP address. I had to make sure that the IP configuration was setup so that it was on the same subnet as my other VMs (10.10.10.x), routed through my pfSsense firewall (gateway), and it was able to access the Internet. I also had to configure DNS correctly as well. For now, I had to make the DNS server 8.8.8.8 and not its own IP. This is because at this point, The Windows Server wasn't a DNS server yet, so it wouldn't be able to point to itself for DNS. So until the DNS server was installed on the Windows Server VM, I temporarily put the DNS server as 8.8.8.8 and it would be changed later (IM8).
2. **VERIFYING NETWORK CONNECTIVITY.** To verify that the network configuration was correct, I went into the Command Prompt and ran a few commands, including pinging the DNS server, pinging the IP address of pfSense, and pinging the IP of the Windows 10 VM to confirm connectivity between VMs. They were all succesfull (IM9), meaning that I was ready to move into the real AD work

<img width="700" height="450" alt="Screenshot 2026-05-18 185859" src="https://github.com/user-attachments/assets/b17c2215-7fca-448f-b925-7b13ed03606f" />

IM8: Configuring networking for Windows Server VM

<img width="466" height="600" alt="Screenshot 2026-05-18 190104" src="https://github.com/user-attachments/assets/cac7e3ab-6ea5-49e2-86a1-80ef6b5d8911" />

IM9: Confirming Network Configuration was Successfull for Windows Server VM














  


