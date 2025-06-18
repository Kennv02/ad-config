<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computer)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create a Resource Group, Virtual Network and two Virtual Machines
- Install Active Directory Domain Services 
- Configure, edit, and maintain connection between your Virtual Machines network settings through Azure 
- Open PowerShell and test DNS connection between both VMs

<h2>Deployment and Configuration Steps</h2>

<p>
In the AD (Active Directory) configuration and deployment guide in Azure, I will walk-through a step by step guide from creating as resource group, virtual network, and virtual machines to deploying Active Directory within Azure's VMs (Virtual Machines)
</p>
<br />

<p> 
  <img src="https://github.com/user-attachments/assets/fbcf38be-e99e-4be2-b585-60f9caee4baa" style="display:inline-block; margin-right:10px;" width="250" />
  <img src="https://github.com/user-attachments/assets/c65d10e6-78f7-4af6-a790-81fd9eb3b341" style="display:inline-block; margin-right:10px;" width="250" />
  <img src="https://github.com/user-attachments/assets/06889b01-fbae-4dc0-a3b9-dcb963502421" style="display:inline-block; margin-right:10px;" width="250"
</p>

<p>
Before I deployed Active Directory through Azure Cloud, I created a resource group, virtual network, and virtual machines using Azure. I outlined examples to showcase what I am doing to streamline an optimal process for Active Directory configuration.

Please note the following:

- Resource Group (east-us2)
- Virtual Network
- Virtual Machines: Client-1 and dc-1 (domain controller)
- Image used for **dc-1**: windows server 2022 Datacenter: Azure Edition x64
- Image used for **client-1**: Windows 10

When creating the VMs, I came across assigning the windows images for a virtual network machine. For some reason, windows 10 and windows server 2022 was not available in zone 1. To fix this, I picked a different zone (zone 2) in the "availability zone" section. 
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/8ba2e751-2edf-4078-a48f-f4d9e00f77f7"
</p>
<p>
Once VMs are created, I logged in to "Remote Desktop Connection" as DC-1 (Copy IP Address and apply to Remote Desktop). I wil begin configuring AD through DC-1.

</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/56a3dce5-8fde-4f93-94bf-7b9b8623d9ee" style="display:inline-block; margin-right:10px;" width="350" />
<img src="https://github.com/user-attachments/assets/5a906b24-5d16-4b01-b337-5f98b444d846"style="display:inline-block; margin-right:10px;" width="350" />

Logging in as DC-1 Remote Desktop, I opened Server Manager and installed "Active Directory Domain Services" as shown above. 
The purpose of Active Directory is to manage and organize network resources such as users, computers, groups, printers, and security policies.
AD helps Help Desk teams with system management and supports staff with items tasks such as:

- Passwords Resets
- Account Lockouts
- Enable/Disable Accounts
- User Permissions
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/00f9633f-e9d8-46b2-8b97-8d2e0bd5925a" />

 With Active Directory installed. I created a new "forest" with the root domain name, "mydomain.com". I clicked the flag cursor on the top right corner

 - A forest in AD terms is essentially just one big world that holds everything such as computers, domains, policies, for an entire organization.
 - There can be one or more domains in a forest like: different countries in a world.
 - Resources may be shared with all domains.

As I moved forward to "domain controller options", the system prompted me to add a Directory Services Restore Mode (DSRM) password. I added "Password1".

- Under DNS Options, **uncheck** DNS delegation and click next.
- I continued to click next for other topics and clicked Install for "Prerequesite Check" and "Installation"
 The system will automatically shut down and will have to log back in.

Please note, I intended to log in within the root domain. In this case, "mydomain.com" is the root domain name.
As I logged in to "Remote Desktop Connection", the username is, "mydomain.com\labuser" and I entered the password I created. In my case, it is "Cyberlab123!"
</p>

<p>
 <img src="https://github.com/user-attachments/assets/fbc98b0e-6178-4e0b-81d1-31a8952b5a4c"  />

In this section of the AD deployment through Azure Cloud, I edited the IP configuration of DC-1 Virtual Machine. 
The reason I am doing this is to ensure the Private IP Address of DC-1 doesn't change when restarting the virtual machine.

I will explain what is happening in the screenshot. I changed the Private IP Address by doing the following: 

- Opened DC-1 -> Network Settings -> Settings -> IP Configuration -> Private IP address settings -> Change Allocation from Dynamic to Static.
- **Dymanic:** Private IP address may change when restarting the VM.
- **Static:** Private IP address will remain the same regardless of the VM restarting.

There is a couple of reasons why I am doing this. 

- I wanted the Private IP address for DC-1 to remain the same now that we are using a domain account for Active Directory.
- Client-1 is another VM that will be used as a client to join the domain of DC-1 and keeping the Private IP address of DC-1 to "static" will ensure the two virtual machines can successfully ping between one another.
- Client-1 will be able access files, resources, etc that DC-1 provides.

What can happen if DC-1's private IP address remains as "dymanic"

- Other devices and domains will not be able to find DC-1 (domain controller)
- It can cause log in failures
- Active Directory relies on DNS (Domain Name System) and if the Private IP address changes the DNS will be outdated and incorrect and other servers won't be able to join.
- DNS failures can happen resulting in incorrect DNS (Domain Name System) and cause multiple network issues.
</p>
<br />

<p>
 <img src="https://github.com/user-attachments/assets/a2f28673-482b-49ea-9a62-bb63bf214429"

As previously mentioned, Client-1 needs to successfully connect to DC-1 Private IP address for the sake of Active Directory deployment. 
I did the following:

- Opened Client-1 -> Network Settings -> client1705_z2 -> DNS servers -> toggled "custom"

I previously copied DC-1's private IP address and entered it to the DNS server under client-1.

This approach ensured Client-1 finds DC-1's through DNS. 
DNS is essential for Active Directory and can ensure a successful connection between both virtual machines.
</p>
<br />

<p>
<img src="https://github.com/user-attachments/assets/14e7ff97-4cf4-4ee7-97be-3b16c343b094" />

I made sure there is a successful connection between both virtual machines, so I opened "remote desktop connection" and logged in as Client-1.
I opened PowerShell through my Client-1 Virtual Machine and I entered the command, "ping" following the private IP adress "10.0.0.4" and we can see the VM reached the IP address of DC-1. 

I then used the command, "ipconfig /all" to get speficic details in the network configuration. 
We can see the following:

- Hostname: Client-1 (current vm)
- IPv4: 10.0.0.5 (client-1's ip address)
- DNS server: 10.0.0.4 (DC-1 private IP address)

Based on this information, we can assume client-1 can connect with dc-1 and can also join the domain and access the AD services. 
</p>
