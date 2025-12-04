# Home-Lab-Project
![Image](https://github.com/user-attachments/assets/ac512267-6287-467c-9023-6a4c1e9ba388)

Welcome to my comprehensive IT infrastructure project, where I have meticulously designed and implemented a homelab environment integrating Active Directory solutions, both on-premises and in the cloud through Azure Active Directory. This project highlights my proficiency in deploying and managing complex IT systems, utilizing a range of tools and skills to ensure robust operational capabilities.

This project not only showcases my technical expertise in IT infrastructure management but also underscores my ability to design, deploy, and optimize enterprise-grade solutions. It reflects my commitment to excellence in IT service delivery and my readiness to contribute effectively to organizational IT operations.      

-----------------------------------------------------------------------------------------------------
# Table of Contents

1. [Homelab Creation](#homelab-creation)
2. [User and Group Creation](#user-and-group-creation)
3. [Microsoft 365 Integration and Management](#microsoft-365-integration-and-management)
4. [Group Policy Management and Implementation](#group-policy-management-and-implementation)
5. [Password Reset](#password-reset)
6. [Multi-factor Authentication](#multi-factor-authentication-mfa)
7. [Software Deployment](#software-deployment)
8. [Remote Access and Troubleshooting](#remote-access-and-troubleshooting)
9. [VPN Configuration](#vpn-configuration)
10. [Monitoring and Visualization](#monitoring-and-visualization)
11. [Task Automation with PowerShell](#task-automation-with-powershell)
12. [Project Conclusion](#project-conclusion)

-----------------------------------------------------------------------------------------------------
## Homelab Creation

In this homelab setup, we will be using Hyper-V to create a virtualized environment. The primary components of this setup will include:
<img width="1344" height="768" alt="Image" src="https://github.com/user-attachments/assets/77bd7656-d959-4d80-9eab-a6cbbaf0135f" />

1. **Four Servers**:
    - One server (Windows Server 2022) will act as the **Domain Controller (DC01)**, responsible for managing the Active Directory (AD) and handling authentication and authorization within the domain.
    - The second server (Windows Server 2022) will be used for other purposes such as file storage, applications, or additional services required within the network(FS01).
    - The third server (Windows Server 2022) will be used for other purposes such as database system, with a few test records(DB01).
    - Additionally, a Linux server will be implemented to run Docker with simple web page.
2. **Four Client Machines**:
    - These machines will be used to simulate end-user workstations within the network. Each machine will be joined to the domain controlled by the Domain Controller.
  
    - Naming Convention
        - Prefix: Client
        - Unique Number: 001, 002, 003, 004

        Detailed Naming List:
        1. Client-001
        2. Client-002
        3. Client-003
        4. Client-004
### Organizational Units (OUs) for Departments

To keep our domain structure well-organized, we will create Organizational Units (OUs) for each department. This will help us manage users, computers, and other resources efficiently within Active Directory. The following OUs will be created:
<img width="502" height="452" alt="Image" src="https://github.com/user-attachments/assets/f13e0d1e-a06c-41ab-bfe9-aa1b1d8ffa9d" />

1. **IT** - Contains users such as IT Support, Sysadmins, and Network Engineers.
2. **Finance** - Contains users such as Finance Managers and Finance Analysts.
3. **Sales** - Contains Sales Representatives.
4. **HR** - Contains the HR Manager and related personnel.
5. **Marketing** - Contains Marketing Specialists and Content Writers.
6. **Development** - Contains Developers and DevOps Engineers.
7. **Customer Service** - Contains Customer Service Representatives.
8. **Design** - Contains Graphic Designers.
9. **Administration** - Contains the Office Manager and administrative staff.

### Summary of Steps

| Step | Description |
|------|-------------|
| **1. Set up Hyper-V on your host machine** |   |
|     1.1 | **Install Hyper-V**: Open the Control Panel, navigate to Programs > Programs and Features > Turn Windows features on or off, check the box for Hyper-V, click OK, and restart your computer. <br/><br/> <img width="1052" height="936" alt="Image" src="https://github.com/user-attachments/assets/d32e20fd-df5a-4964-8359-30d90abc5b51" /> |
|     1.2 | **Configure Hyper-V**: Open Hyper-V Manager from the Start menu, select your host machine, and configure virtual switches by going to Virtual Switch Manager in the right-hand Actions menu. Create a new External virtual switch to allow VMs to access your physical network. <br/><br/> <img width="1442" height="1368" alt="Image" src="https://github.com/user-attachments/assets/bcd3eb97-f6d9-459e-829b-17b11d53e5b9" />|
| **2. Create Virtual Machines** |   |
|     2.1 | **Create the Domain Controller VM**: In Hyper-V Manager, click New > Virtual Machine, and follow the wizard to set up the VM with appropriate settings such as name, generation, memory, network, and virtual hard disk. Install an operating system later. <br/> In my case, I'm going to choose to change the default path to store the virtual machine, as it seems more organized to me. In this case, I'll opt to place it in C:\Hyper-V\VMs\\. <br/><br/> <img width="1404" height="1060" alt="Image" src="https://github.com/user-attachments/assets/9ebc2568-b066-4932-a576-3e07fd577fe2" /> <br/><br/> Afterwards, I select Generation 1, as Generation 2 can be prone to errors. <br/><br/> <img width="1406" height="1060" alt="Image" src="https://github.com/user-attachments/assets/15f9f4c2-0509-4c77-a858-4a35cc129302" /> <br/><br/> Now, I need to allocate the RAM to the virtual machine. In this case, I'll opt for the minimum recommended for Windows Server 2022 with Desktop Experience, which is 4GB (4096 MB). <br/> Additionally, I'll choose to leave the **'Use dynamic memory for this virtual machine'** checkbox checked. <br> When a virtual machine uses dynamic memory, it means that its memory allocation can adjust automatically based on the demand from the operating system and applications running within the virtual machine. Instead of assigning a fixed amount of memory, the virtual machine can request and release memory as needed, allowing for more efficient utilization of host resources. <br/><br/> <img width="1406" height="1066" alt="Image" src="https://github.com/user-attachments/assets/722357ba-379b-4679-8389-2d072e3d73b8" /> <br/><br/> Then, I select the network adapter to which the virtual machine will connect. In this case, it's the network adapter created in step 1.2 (WAN), which is associated with my Qualcomm Atheros QCA9377 wireless network card. <br/><br/> <img width="1404" height="1058" alt="Image" src="https://github.com/user-attachments/assets/59c9524a-0f89-4926-95a3-1c7cbcdec257" /> <br/><br/> Next, I will create a virtual hard disk of 2TB (2048GB), which will be more than enough for any practice you may want to perform. <br/><br/> <img width="1406" height="1058" alt="Image" src="https://github.com/user-attachments/assets/58013fe8-8e5a-49a1-afb2-dc662f440bd0" /> <br/><br/> Finally, I select the option 'Install an operating system from a bootable CD/DVD-ROM' and locate the location of the .ISO file containing the Windows Server 2022 Operating System. <br/><br/> ![DC01-6](images/dc01-6.PNG) <br/><br/> Once this is done, the virtual machine is successfully created. Now, all that's left is to connect to it, start it up, and follow the Windows Server installer. <br/><br/> ![DC01-7](images/dc01-7.PNG) |
