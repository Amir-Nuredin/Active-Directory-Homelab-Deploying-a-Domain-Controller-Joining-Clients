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

Now that I downloaded the ISO image, I had to create the VM for the Windows Server 2022 and configure it with the right amount of storage, RAM, and also correct networking. The stats for the VM are in IM4. One important thing I had to make sure I configured correctly was that the Windows Server 2022 VM was on the same LAN as all my other VMs, so that they could communicate, and especially later when the Windows 10 VM needs to join the domain. After this, I attached the ISO and loaded up the VM. 

<img width="366" height="500" alt="Screenshot 2026-05-18 175810" src="https://github.com/user-attachments/assets/2329ad9b-f7b6-45ea-b482-4561559bf234" />

IM4: Windows Server 2022 VM setup

### Part 3: Installing Windows Server

Now that the VM was set up and the ISO was attached, I could Install an setup the Windows Server. It was the typical Windows install process. I chose the language, chose the type of desktop (Windows Server 2022 Standard (Desktop Experience)), and set a password for the admin account, as this is an administrative type of server (IM5). Once that was all set and done, the install process started, and I let it finish (IM6). Once it was installed, I logged in with the credentials that I set, and the VM loaded up with Server Manager being the first application to pop up (IM7). 

<img width="600" height="300" alt="Screenshot 2026-05-18 181650" src="https://github.com/user-attachments/assets/8ea0d935-2183-4ec5-9bca-b7d1a9a4d86b" />

IM5: Setting up Login Credentials for Windows Server Admin Account

<img width="600" height="300" alt="Screenshot 2026-05-18 180447" src="https://github.com/user-attachments/assets/19f55616-5587-41bb-bf46-fef3436a465a" />

IM6: Installation Process for Windows Server VM

<img width="600" height="400" alt="Screenshot 2026-05-18 181923" src="https://github.com/user-attachments/assets/1f99c7c7-0c47-49ff-8c02-e1a3888c2a44" />

IM7: Windows Server VM loaded up with Server Manager

### Part 4: Configuring Networking For Windows Server

This step, before starting the real AD work, was crucial, as if I didn't set this up correctly, the domain join later would fail, and the Windows Server VM wouldn't be able to communicate with the Windows 10 VM. 

1. **CONFIGURING SERVER WITH STATIC IP.** The reason for configuring a static IP instead of using DHCP is that the Domain Controller (will be discussed later) must always be reachable at the same address. If DHCP were used, the IP address would change every day, causing the network configuration and DNS to break. For this reason, I configured the server with a static IP address. I had to make sure that the IP configuration was set up so that it was on the same subnet as my other VMs (10.10.10.x), routed through my pfSense firewall (gateway), and it was able to access the Internet. I also had to configure DNS correctly as well. For now, I had to set the DNS server to 8.8.8.8, not its own IP. This is because, at this point, the Windows Server wasn't a DNS server yet, so it couldn't resolve its own DNS records. So until the DNS server was installed on the Windows Server VM, I temporarily put the DNS server as 8.8.8.8, and it would be changed later (IM8).
2. **VERIFYING NETWORK CONNECTIVITY.** To verify that the network configuration was correct, I went into the Command Prompt and ran a few commands, including pinging the DNS server, pinging the IP address of pfSense, and pinging the IP address of the Windows 10 VM to confirm connectivity between VMs. They were all successful (IM9), meaning that I was ready to move into the real AD work

<img width="700" height="450" alt="Screenshot 2026-05-18 185859" src="https://github.com/user-attachments/assets/b17c2215-7fca-448f-b925-7b13ed03606f" />

IM8: Configuring networking for Windows Server VM

<img width="466" height="600" alt="Screenshot 2026-05-18 190104" src="https://github.com/user-attachments/assets/cac7e3ab-6ea5-49e2-86a1-80ef6b5d8911" />

IM9: Confirming Network Configuration was Successful for Windows Server VM

### Part 5: Installing Active Directory Domain Services 

Now that the Windows Server VM was fully functional and ready to go, I could start working with AD and setting up basic administration. To install AD, I had to go to Server Manager, and in the top right select "manage." Then I clicked Add roles and features from the dropdown options. A wizard would then pop up asking me what type of installation I wanted and the server I wanted to choose (IM10). After that, I selected the role I wanted, which, of course, was AD. I then went through the rest of the installation wizard and installed AD onto the Windows Server (IM11&12). 

<img width="650" height="500" alt="Screenshot 2026-05-19 190013" src="https://github.com/user-attachments/assets/7078cd97-19a1-4851-bad6-d17bcc5d4b8f" />

IM10: Roles and Features Install Wizard in Server Manager

<img width="600" height="425" alt="Screenshot 2026-05-19 190448" src="https://github.com/user-attachments/assets/1c5d8648-f144-483f-ba57-bb2ca19a61ec" /><img width="497" height="299" alt="Screenshot 2026-05-19 190559" src="https://github.com/user-attachments/assets/b1c58b11-c087-4933-b007-f8ad123bc2e0" />

IM1&12: AD Install complete and in Server Manager

### Part 6: Promoting Windows Server to a Domain Controller

Now that AD was installed on the server, I needed to promote the Server into a domain controller. By doing this, the Server now becomes a domain and also a DNS server for itself and any other machines on the domain. 

1. **PROMOTING SERVER TO DOMAIN CONTROLLER.** After AD installation, a yellow warning flag appears in the top menu of Server Manager, and had a option to click that says "Promote this server to a domain controller (IM13)." After clicking that, an AD DS wizard pops up to configure the domain
2. **CREATING A NEW FOREST.** In the wizard, it asks for the deployment type. I chose "Add a new forest." A forest is essentially the top-level AD structure in the domain. I named it "Corp.Local." This means that this is the name for my enterprise domain, and users logging into it will log in as "Corp\username" and the username, of course, being any specific user. I also checked the box for DNS server in the additional options section of the wizard (IM14).
3. **SETTING DSRM PASSWORD.** The last step before promotion was setting a DSRM (Directory Services Restore Mode) password. This password is created when you first promote a server to an Active Directory Domain Controller. It is used to log in and restore or repair the Active Directory database when normal directory services fail. I set a secure password and stored it in case of future use. Once this was all done, the system rebooted so that the configurations would activate. 

<img width="334" height="109" alt="Screenshot 2026-05-19 191003" src="https://github.com/user-attachments/assets/774f1da8-5af0-4e6b-a84e-6f586cac7b86" />

IM13: Option to Promote Server to Domain Controller

<img width="750" height="500" alt="Screenshot 2026-05-19 191355" src="https://github.com/user-attachments/assets/eec1476a-1864-4976-8e71-30f02da9f78b" />

IM14: AD DS Promotion Wizard

### Part 7: Post-Installation Configurations

Now that the Windows Server was a DC and had AD working on the server, I needed to do a couple of things to ensure the domain joins and other similar tasks would work later on. 

1. **CHANGING DNS SETTINGS.** As mentioned earlier, the Windows Server wasn't a DNS server yet, so it wouldn't be able to point to itself until designated as one. Now that it was, I had to change it to itself so that it would use its own DNS server. The IP address of the DNS server would be the same static IP address assigned to it when first created, as it is a DNS server, so it has to use its own IP. I went back into Internet Settings and Adapter Options, then Properties and IPV4, then changed the IP address of the DNS Server from 8.8.8.8 to the IP address of the Server, 10.10.10.104 (IM15). To verify that DNS was working, I went into the Command Prompt and ran the commands "nslookup corp.local" to confirm it would return the IP of the DNS server, and "ping corp.local" to ensure connectivity with DNS, and they both worked (IM16)
2. **VERIFY AD SERVICES ARE WORKING.** To check this, in Server Manager, I went to tools, then clicked "Active Directory Users and Computers." When opened, it shows my domain Corp.Local with the groups and users within it, confirming that AD was set up correctly (IM17).

<img width="700" height="450" alt="image" src="https://github.com/user-attachments/assets/2b0e5274-f42e-4c53-b943-ce854fb4bb20" />

IM15: Changing DNS IP to the IP of the Windows Server

<img width="450" height="250" alt="Screenshot 2026-05-19 192738" src="https://github.com/user-attachments/assets/2aae95df-d3f4-49f3-a277-5516e7a755f6" />

IM16: Testing DNS connectivity

<img width="600" height="450" alt="Screenshot 2026-05-19 192924" src="https://github.com/user-attachments/assets/f64cc7b0-9348-4a7f-b5b8-cd5c984b1f53" />

IM17: Confirming AD was up and running

### Part 8: Creating Organizational Units (OUs)

Now we can actually start working with AD. The first thing I did was create some Organizational Units. OUs are folders inside AD that help organize users and computers, apply group policies much more easily, separate admin versus normal users, and provide many other benefits. I wanted to create a few basic ones to simulate creating different OUs for different roles in a real enterprise network. To do this, I right-clicked the Corp.Local section in AD users and computers, and went to New and then OU (IM18). Once clicking that, there is a pop-up that tells you to type in the name of the OU. I did this process and created 4 OUs, Users, Workstations, Servers, and ITadmins (Ignore the Staff OU, that was a mistake) (IM19). 

<img width="600" height="450" alt="image" src="https://github.com/user-attachments/assets/282424a1-3650-424f-a9c1-46ff41d7825b" />
 
IM18: Creating New OUs in AD

<img width="194" height="351" alt="image" src="https://github.com/user-attachments/assets/3ccf40a7-91cc-46d2-98d7-27b5c324d2a2" />

IM19: New OUs under Corp.Local Domain















  


