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
|     2.1 | **Create the Domain Controller VM**: In Hyper-V Manager, click New > Virtual Machine, and follow the wizard to set up the VM with appropriate settings such as name, generation, memory, network, and virtual hard disk. Install an operating system later. <br/> In my case, I'm going to choose to change the default path to store the virtual machine, as it seems more organized to me. In this case, I'll opt to place it in C:\Hyper-V\VMs\\. <br/><br/> <img width="1404" height="1060" alt="Image" src="https://github.com/user-attachments/assets/9ebc2568-b066-4932-a576-3e07fd577fe2" /> <br/><br/> Afterwards, I select Generation 1, as Generation 2 can be prone to errors. <br/><br/> <img width="1406" height="1060" alt="Image" src="https://github.com/user-attachments/assets/15f9f4c2-0509-4c77-a858-4a35cc129302" /> <br/><br/> Now, I need to allocate the RAM to the virtual machine. In this case, I'll opt for the minimum recommended for Windows Server 2022 with Desktop Experience, which is 4GB (4096 MB). <br/> Additionally, I'll choose to leave the **'Use dynamic memory for this virtual machine'** checkbox checked. <br> When a virtual machine uses dynamic memory, it means that its memory allocation can adjust automatically based on the demand from the operating system and applications running within the virtual machine. Instead of assigning a fixed amount of memory, the virtual machine can request and release memory as needed, allowing for more efficient utilization of host resources. <br/><br/> <img width="1406" height="1066" alt="Image" src="https://github.com/user-attachments/assets/722357ba-379b-4679-8389-2d072e3d73b8" /> <br/><br/> Then, I select the network adapter to which the virtual machine will connect. In this case, it's the network adapter created in step 1.2 (WAN), which is associated with my Qualcomm Atheros QCA9377 wireless network card. <br/><br/> <img width="1404" height="1058" alt="Image" src="https://github.com/user-attachments/assets/59c9524a-0f89-4926-95a3-1c7cbcdec257" /> <br/><br/> Next, I will create a virtual hard disk of 2TB (2048GB), which will be more than enough for any practice you may want to perform. <br/><br/> <img width="1406" height="1058" alt="Image" src="https://github.com/user-attachments/assets/58013fe8-8e5a-49a1-afb2-dc662f440bd0" /> <br/><br/> Finally, I select the option 'Install an operating system from a bootable CD/DVD-ROM' and locate the location of the .ISO file containing the Windows Server 2022 Operating System. <br/><br/> <img width="1400" height="1062" alt="Image" src="https://github.com/user-attachments/assets/de3f8c16-c6e4-4216-9a0f-8c78347a10f3" /> <br/><br/> Once this is done, the virtual machine is successfully created. Now, all that's left is to connect to it, start it up, and follow the Windows Server installer. <br/><br/> <img width="2064" height="464" alt="Image" src="https://github.com/user-attachments/assets/b1f482c3-10a6-481e-898b-9d1028b8909a" /> |
|     2.2 | **Create the fourth Server VM**: Follow the same steps as above but name this VM Server2. <br/><br/> Next, I create the second virtual machine, which will also have Windows Server 2022 as its operating system. I will avoid detailing the installation as it is the same as the DC01 virtual machine, only the name is different. <br/> In this case, the virtual machine will be named 'FS01'. <br/><br/> <img width="1402" height="1062" alt="Image" src="https://github.com/user-attachments/assets/0ce534b9-ecf0-4141-a5ab-ed5f90c20be9" /> <br/><br/> Then, once all the fields are filled out, I have the 2 server machines created. <br/><br/> <img width="2064" height="464" alt="Image" src="https://github.com/user-attachments/assets/3325d8b8-c4e2-40e0-b5ef-2b209ad2fc33" /> |
|     2.3 | **Create the Client VMs**: Repeat the VM creation process four times for client machines, naming them Client-001, Client-002, Client-003, and Client-004. Assign at least 1024 MB of memory to each client VM. <br/><br/> Now, I repeat the process but instead of using the Windows Server 2022 .iso file as the boot disk, I choose the Windows 10 .iso file. <br/><br/> <img width="1402" height="1062" alt="Image" src="https://github.com/user-attachments/assets/123e067f-e33d-45e8-89c1-bdcda02ddd36" /> <br/><br/> <img width="1396" height="1064" alt="Image" src="https://github.com/user-attachments/assets/9c8c47bb-b615-4e12-8340-72270893da35" /> <br/><br/> Then, once the 4 servers (Windows Server 2022) and the four client machines (Windows 10) are created, I have the following: <br/><br/> |
| **3. Install the necessary operating systems on all VMs** |   |
|     3.1 | **Prepare installation media**: Obtain ISO files for Windows Server (for the servers) and Windows 10/11 (for the client machines). <br/><br/> In this case, I chose to download both .iso files (Windows Server 2022 and Windows 10) beforehand, but you can also choose the option 'Install an operating system later,' which creates the virtual machine and waits for you to attach the desired .iso file later. <br/><br/> If you prefer to do it that way, you can download the .iso files from the official Microsoft website: <br/><br/> -  [Windows Server 2022](https://www.microsoft.com/es-ES/evalcenter/evaluate-windows-server-2022)<br/> -  [Windows 10](https://www.microsoft.com/es-es/software-download/windows10) <br/> |
|     3.2 | **Install Windows Server on DC1 and Server2**: Start the VM, connect to it, attach the Windows Server ISO, and follow the installation prompts to install Windows Server. Configure the server with appropriate settings (e.g., server name, IP address). <br/><br/> Once the virtual machine is created and the Windows Server 2022 disk is 'inserted,' I will start it and proceed with the installation. I will perform this process for all the four servers the DC01 (Domain Controller) server , the FS01 server, DB01 server, and WEB01 server. <br/><br/> <img width="1536" height="1243" alt="Image" src="https://github.com/user-attachments/assets/f4b9c88d-1e78-4ad2-943a-3de0500db985" /> <br/><br/> After clicking 'Install,' I need to select the version I want. Among the options are the Standard and Datacenter versions, each with a Desktop Experience (GUI) version and a Server Core version. In this case, I will select the Standard version with Desktop Experience: <br/><br/> <img width="1538" height="1239" alt="Image" src="https://github.com/user-attachments/assets/5c371753-d540-450f-9ed3-15fc034bd99a" /> <br/><br/> Next, I need to select the partition on which the operating system will be installed. In this case, it is the only partition I have: the 100GB partition created when I created the virtual machine, but I could also create another partition and install it there. <br/><br/> <img width="1538" height="1234" alt="Image" src="https://github.com/user-attachments/assets/2bb1eecb-a93a-495e-8929-6659d13d5f63" /> <br/><br/> After selecting the partition, the installer will continue copying files and installing the operating system. The installation time varies depending on the resources, both those we have and those allocated to the virtual machine. <br/><br/> <img width="1538" height="1241" alt="Image" src="https://github.com/user-attachments/assets/4b8e0d97-b45f-4f3e-8027-033dddf1f3a2" /> <br/><br/> Now, once the above is completed, you will be prompted to create a password for the 'Administrator' user, who has full control over the operating system. This account is similar to the superuser in Linux. It is essential to remember this password. <br/><br/> <img width="1529" height="1244" alt="Image" src="https://github.com/user-attachments/assets/cef956fb-3e8b-45d4-8bb3-59a70af0852c" /><br/><br/> Now, I can log in with the credentials provided recently. <br/><br/> <img width="849" height="647" alt="Image" src="https://github.com/user-attachments/assets/e8014092-3922-43b2-ae3e-95254d81e994" /> <br/><br/> Once logged in, the 'Server Manager' will automatically open (this can be disabled), which allows managing Roles and Features, configuring the server's Firewall, changing the name, changing the IP address, managing storage, etc. <br/><br/> In this case, within the 'Local Server' section, I will change its name and IP address. The IP address will change from being managed by DHCP to static, which is seen as a good practice (in servers, not in client machines), as it increases reliability among other reasons. <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/af774325-b91d-47e0-b35e-adc0c21cd355" /> <br/> <br/> The **name** will be: DC01. <br/><br/> <img width="644" height="752" alt="Image" src="https://github.com/user-attachments/assets/23b706db-1e4e-42cf-8d6a-9860f0e9b55c" /> <br/><br/> The **static IP address** for DC01 will be: 10.10.0.10 <br/> The **subnet mask** will be: 255.255.255.0, which means that the first 3 octets of the IP address (10.10.0) will correspond to identifying the network portion, while the fourth and last octet will correspond to identifying the host portion. In this case, with the /24 subnet mask, we get 2^8 - 2 (254) available hosts for the devices and/or servers (2 raised to the available bits, minus 2 because there are 2 addresses that cannot be assigned: 10.10.0.0, which corresponds to the network address, and 10.10.0.255, which corresponds to the broadcast address). <br/> The **default gateway** will be 10.10.0.1, corresponding to the IP address of the default gateway that I have. <br/> The **primary DNS server** will be: 8.8.8.8, corresponding to Google's public DNS server. <br/> **The alternate DNS server** will be: 8.8.4.4, also corresponding to another public DNS server provided by Google. <br/><br/> <img width="397" height="460" alt="Image" src="https://github.com/user-attachments/assets/3c7e0dbf-08bb-43ec-91d8-07643bc06a7f" /> <br/><br/> To apply the changes (the server name change), it is necessary to restart the computer. <br/><br/> That's all for the DC01 server (temporarily, although it's still not a domain controller). Now, I will proceed to install the Windows Server operating system on the other server machine (FS01).  |
|     3.3 | **Install Windows 10/11 on client VMs**: Start each client VM, connect to it, attach the Windows 10/11 ISO, and follow the installation prompts to install Windows. Configure each client with appropriate settings (e.g., machine name, IP address). <br/><br/> Once the Windows 10 disk is 'inserted' into the client machine, I proceed to start it to initiate the installation process. <br/><br/> <img width="1534" height="1288" alt="Image" src="https://github.com/user-attachments/assets/7168ac55-4695-401b-a34f-0166c7932b32" /> <br/><br/> Then, I press "Install now," and enter the product key (if available). In this case, I don't have one, so I select "I don't have a product key. <br/><br/> <img width="1536" height="1292" alt="Image" src="https://github.com/user-attachments/assets/ab06df6c-c493-4c21-846c-3a110d7f3eb2" /> <br/><br/> Now, I select the version of Windows that I want to install. It's crucial to choose the Pro or Education version, as the Home version lacks the capability to join an Active Directory domain, thus cannot be centrally managed. In this case, I will opt for the Windows 10 Pro version. <br/><br/> <img width="1542" height="1284" alt="Image" src="https://github.com/user-attachments/assets/fea2a0d1-fe05-49fc-bc99-4c2e4f01d1ec" /> <br/><br/> Now, I select again the partition on which I want to install the operating system. In this case, it's the only partition I have created. <br/><br/> <img width="1536" height="1288" alt="Image" src="https://github.com/user-attachments/assets/c7d94440-ea9c-437a-bd51-421bdf274961" /> <br/><br/> Now, I must wait for the installation process to finish. <br/><br/><img width="1404" height="1056" alt="Image" src="https://github.com/user-attachments/assets/ae5de9da-148d-4da4-a647-40086407c89d" /> <br/><br/> That's it. Now, once the operating system has started, I complete the final configurations (region, keyboard, network, local account, privacy, etc.), and once the desktop is displayed, I proceed to change the computer name to "Client-001": <br/><br/> <img width="1204" height="946" alt="Image" src="https://github.com/user-attachments/assets/b7da388d-34be-4217-85cc-5dde7c66b8d6" /><br/><br/> Finally, I repeat the process for the rest of the computers, only varying the name (Client-002, Client-003, and Client-004). |
| **4. Configure the Domain Controller** |   |
|     4.1 | **Install Active Directory Domain Services (AD DS):** Log in to DC1, open Server Manager, click Add roles and features, select Active Directory Domain Services, and complete the installation. <br/><br/> Now, to be able to use the Windows Directory Service (**Active Directory**), I need to install Active Directory Domain Services (AD DS) on the server designated as the domain controller. To do this, I go to the Server Manager and select the 'Add Roles and Features' option. Then, I select Active Directory Domain Services (AD DS) and follow the steps. <br/><br/> <img width="1534" height="1236" alt="Image" src="https://github.com/user-attachments/assets/8ef997f2-0fb3-48c6-be04-b8b5128c076d" /><br/><br/> In the next window, I select the 'Role-based or feature-based installation' option and also verify in the top left corner that the destination server matches the server where I want to install the role or feature (in this case, DC01, which is correct). <br/><br/><img width="1180" height="838" alt="Image" src="https://github.com/user-attachments/assets/925df8c9-8533-4e37-bceb-5306b1e9dc98" /> <br/><br/> Again, I select the desired server (DC01) and click Next. <br/><br/><img width="1176" height="842" alt="Image" src="https://github.com/user-attachments/assets/d970b3c9-db2f-44e3-9cdc-5d5b0aac42a7" /> <br/><br/> Next, I select the Active Directory Domain Services option. Clicking on this option will open an additional window that will identify all the roles and features that will be installed and are necessary for the proper functioning of this role. <br/><br/><img width="1174" height="834" alt="Image" src="https://github.com/user-attachments/assets/541c41c0-1e3e-4a1d-adcf-22c508d7937f" /> <br/><br/> <img width="1180" height="842" alt="Image" src="https://github.com/user-attachments/assets/b24e5a8a-5cc1-436f-93be-61bc01f51fc7" /> <br/><br/> After clicking the 'Add Features' option, the original Active Directory Domain Services checkbox will appear checked. <br/><br/><img width="1182" height="840" alt="Image" src="https://github.com/user-attachments/assets/f26725d7-75a2-454a-ac9e-96dbebd2dfb3" /> <br/><br/> Next, the features that can be installed on the server will appear. In this case, I don't need to add any, so I click Next. <br/><br/><img width="1184" height="838" alt="Image" src="https://github.com/user-attachments/assets/b49fa26b-fa95-4175-9c16-675ef6400991" /> <br/><br/> Finally, a summary of the installed roles and features will appear. I can also check the box indicating to the server that it should restart if necessary to complete the installation. This box should be checked carefully, as it is not appropriate to restart a server that is in operation in a business environment unless this restart has been scheduled in advance and does not affect the proper operation of any service. Once verified that everything is correct, I press 'Install' to begin the installation of AD DS. <br/><br/><img width="1180" height="846" alt="Image" src="https://github.com/user-attachments/assets/d6001448-2082-40e0-8a50-a56c6663997e" /> <br/><br/> Once this is done, the installation will begin. I can close the window and let the installation run in the background or simply wait for it to finish. <br/><br/> <img width="1174" height="840" alt="Image" src="https://github.com/user-attachments/assets/6b2fda7b-bccb-4cc8-9206-64a27b5f9d4b" /> <br/><br/> Finally, the installation is complete, but this doesn't mean that the server is already a Domain Controller, as only the Active Directory Domain Services (AD DS) were installed. To accomplish this, I must promote the server to a Domain Controller (DC), which will be done in the next step. <br/><br/><img width="1534" height="1242" alt="Image" src="https://github.com/user-attachments/assets/1cd08351-4649-4c86-8070-2d5a6f594904" /> <br/><br/> |
|     4.2 | **Promote DC1 to a Domain Controller:** After installation, click on the notification flag in Server Manager, select Promote this server to a domain controller, choose Add a new forest, enter a root domain name (e.g., jmsoft.local), and complete the wizard. Restart the server as prompted. <br/><br/> Now, once Active Directory Domain Services are installed, I need to promote the server to a domain controller to create the forest, the domain, and then add the desired users. To do this, I need to go to the notification that appears in the Server Manager and click on 'Promote this server to a domain controller.' <br/><br/><img width="1534" height="1242" alt="Image" src="https://github.com/user-attachments/assets/1cd08351-4649-4c86-8070-2d5a6f594904" /> <br/><br/> Once that option is selected, a window will open with three options: <br/><br/> **1 - Add a domain controller to an existing domain**: This option is useful when you want to add an additional server that provides authentication and authorization services within the same domain (a previously created domain). This increases redundancy and availability. It also helps distribute the load among multiple Domain Controllers, improving performance. <br/><br/> **2 - Add a new domain to an existing forest**: This involves creating an additional domain within an Active Directory forest. A forest is a collection of one or more domains that share a common schema and global catalog configuration. Domains within the same forest have implicit transitive trust relationships, allowing for authentication and access to resources across domains. <br/><br/>**3 -  Add a new forest**: This means creating a completely independent instance of Active Directory. A forest is the highest security and administrative boundary in AD. <br/><br/> In this case, I will select the last option (Add a new forest), and the root domain name will be '**campushub**'. If there is a website called 'jmfsoft.com', it is good practice not to use that domain name as the root domain name to avoid DNS resolution conflicts. <br/><br/><img width="1146" height="836" alt="Image" src="https://github.com/user-attachments/assets/e8e7bdb3-8d9c-4b7a-acc6-e806e2259db6" /> <br/><br/> Once 'Next' is pressed, a window will open with multiple options: <br/><br/><img width="1142" height="840" alt="Image" src="https://github.com/user-attachments/assets/ec38a761-e2a0-4026-898e-f80c25e42aa8" /> <br/><br/> Forest functional level and domain functional level refer to the compatibility settings and features available in an Active Directory environment. These levels determine which Active Directory functions and features can be used, based on the versions of Windows Server running on the domain controllers in the environment. <br/><br/>1 - The "**Domain functional level**" specifies the features and functionalities available within a specific domain in Active Directory. <br/><br/>2 - The "**Forest functional level**" specifies the features and functionalities available across the entire Active Directory forest, which includes all domains within that forest.<br/><br/> By raising the functional levels of the domain or forest, all domain controllers must be running at least the minimum version of Windows Server required by the new functional level. Once the functional level of the forest or domain is raised, it cannot be reverted to a previous functional level without restoring from a backup. <br/><br/> Next, there is a section called 'Specify Domain Controller Capabilities,' which has three options: <br/><br/> **1 - Domain Name System (DNS) Server**: This option allows the Domain Controller to provide name resolution services that are essential for Active Directory functionality. DNS is critical for clients and servers to locate resources within the domain. AD depends on DNS to locate domain controllers, replication services, and other critical resources. SRV (Service Location) records in DNS enable clients to locate services such as domain controllers. <br/><br/>**2 - Global Catalog (GC)**: This option indicates that the domain controller contains a partial copy of all objects in the Active Directory forest. Domain controllers acting as GCs store key information and allow quick searches across the entire forest. During logon, the GC can provide information about universal group membership, which is crucial for user authentication and authorization. <br/><br/>**3 - Read-Only Domain Controller (RODC)**: This is a special type of domain controller designed for environments where the physical security of the server cannot be guaranteed, such as branch offices or remote locations. The RODC stores a read-only copy of the Active Directory database. It cannot make changes to AD, which helps protect the integrity of the directory if the server is physically compromised. By default, the RODC does not store user credentials (passwords) unless explicitly configured to do so. <br/><br/> Finally, we need to specify the **Directory Services Restore Mode (DSRM) password**, which is an important security measure in Active Directory domain controllers. It is used in directory recovery situations when the system is in a degraded state or when critical maintenance tasks need to be performed. The DSRM password allows logging in to a domain controller even if the operating system cannot start normally or if the Active Directory service is inaccessible. This provides emergency access to the domain controller in critical situations. <br/><br/><img width="1140" height="838" alt="Image" src="https://github.com/user-attachments/assets/8f1b5f91-87e1-4a5c-b255-42eba3d33666" /> <br/><br/> Next, we must verify the NETBIOS name, which is a network identifier used in Microsoft operating systems to identify resources on a local network. In the context of Active Directory, the NetBIOS name is primarily used for backward compatibility and to enable communication with legacy systems that use this technology. <br/><br/><img width="1146" height="838" alt="Image" src="https://github.com/user-attachments/assets/121cff5e-67bc-4f1f-abd9-b5c959fb2e12" /> <br/><br/> Next, the paths will be displayed, indicating the location of the folder containing the database, the folder containing the log files, and the SYSVOL folder. <br/><br/>1 - **The database folder (C:\Windows\NTDS by default)** stores the Active Directory database, where all directory objects and attributes are stored, including users, groups, policies, and system configurations. The NTDS.DIT file is the primary Active Directory database file. It contains all directory data, such as users, groups, computer objects, etc. Active Directory domain controllers (DCs) access this database to provide directory services.<br/><br/>2 - **The log files folder (C:\Windows\NTDS by default)** contains the transaction log files that record all operations performed on the Active Directory database. Transaction log (LOG) files are sequential and are used to ensure the consistency and integrity of the Active Directory database. Each transaction performed in the database is recorded in these files before being committed and written to the database. In the event of system failure or data loss, the log files can be used to recover lost data or to restore the database to a consistent state.<br/><br/> 3 - **The SYSVOL folder (C:\Windows\SYSVOL by default)** stores shared data and group policies in an Active Directory environment. SYSVOL contains files and folders that are replicated among all domain controllers in the domain. This includes user login scripts, login policies, group policies, login script files, etc.<br/><br/> In summary, these folders are critical for the operation and integrity of an Active Directory environment. The database folder stores the AD database, the log files folder records transactions, and the SYSVOL folder stores shared data and group policies. <br/><br/><img width="1138" height="834" alt="Image" src="https://github.com/user-attachments/assets/dac9cd36-7c90-4d48-873d-40d70815d413" /> <br/><br/> Then, a summary of all the selected options and configurations is displayed. This allows reviewing the choices to confirm if I want to make a last-minute change before promoting the server as a domain controller. In my case, I reviewed all the options and they are correct, so I will proceed with the installation. <br/><br/> Additionally, I can copy the executed script which contains the commands executed by the operating system to perform all the above. This is useful if I want to run a script in Windows PowerShell and automate future installations. <br/><br/> <img width="1368" height="854" alt="Image" src="https://github.com/user-attachments/assets/e02aaf54-b8f7-43ac-9c8a-953e7378fcc1" /> <br/><br/> As a final step prior to installation (i.e., promoting the server to Domain Controller), prerequisites are checked to see if the system is suitable for promotion. In this case, although it threw some usual warnings, the system is suitable to be promoted to a domain controller. After this, I click on Install and wait for the installation to complete. When the promotion operation is finished, the system will automatically restart. <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/99abab35-9321-4a27-b485-f6b782f81430" /> <br/><br/> After the server restarts, the login screen changes. Below the user and password, a message appears saying **'Sign in to JMFSOFT'**, indicating that, unless specified otherwise, I'll sign in to the domain with that name. This doesn't necessarily mean that the server is a domain controller, as the login process on client machines will be the same, but it does indicate that the server is now part of the JMFSOFT domain. <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/d35543fe-4c8b-4160-addb-b5076f979dc0" /> <br/><br/> After promoting the server to a domain controller, new sections will appear in the Server Manager that didn't exist before. For example, on the left side, you'll see AD DS (Active Directory Domain Services) and DNS (Domain Name System). Then, in the tools section, several new tools will appear, such as Active Directory Administrative Center (ADAC), DNS, Active Directory Domains and Trusts, Active Directory Sites and Services, Active Directory Users and Computers (ADUC), among others. <br/><br/> With this, I conclude step 4.2, which aims to promote the server to a domain controller. |
| **5. Join all client machines to the domain** |   |
|     5.1 | **Configure network settings:** Ensure that all client machines and the second server can resolve the domain by setting the DNS server to the IP address of DC1. <br/><br/> To join client computers (and also the secondary server) to the newly created domain (jmfsoft.local), the first thing I need to do is change the DNS server to the IP address of the domain controller. This is necessary for several reasons: <br/><br/>1 - **Name Resolution**: Active Directory relies on the DNS service to resolve domain names, such as the domain name you are joining. The DNS server configured on the client needs to correctly resolve these names to locate the domain and complete the join process. <br/><br/>2 - **Domain Controller Location**: The client needs to locate the Domain Controller to join the domain and obtain configuration information. The IP address of the Domain Controller is used to identify where the Active Directory infrastructure is located on the network. <br/><br/>3 - **Resource Records in DNS**: During the domain join process, the client needs to look up specific resource records (e.g., SRV records) in DNS to locate Active Directory services, such as authentication and replication services. These records are associated with the domain name and are resolved via the DNS server configured on the client.<br/><br/>4 - **Communication with the Domain Controller**: After joining the domain, the client must be able to communicate with the Domain Controller to authenticate users, obtain group policies, and perform other Active Directory operations. Configuring the DNS server is essential for the client to correctly communicate with the Domain Controller. <br/><br/> If I do not point the DNS server to the IP address of the server, I get the following error: <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/37546a38-188e-4c5b-bedd-21ecb8254a7d" /> <br/><br/> So, to solve this issue and allow the client machines (and the additional server) to join the 'jmfsoft.local' domain, I go to the network settings and set the DNS server to the IP of the domain controller, which in this case is 10.10.0.10 Although in some parts of the project the network is shown as both 192.168.0.0/24 and 192.168.1.0/24, this is because I am doing it in two geographically distant locations with two different ISPs, so this number will vary at times, but the concept remains the same. <br/><br/> Therefore, to do this, I go to the wireless connection icon -> Network and Sharing Center -> Change adapter settings -> Select the network adapter and right-click on it -> Properties -> Internet Protocol Version 4 -> Properties and then set the DNS server address to match the domain controller's IP address. <br/><br/> <img width="819" height="975" alt="Image" src="https://github.com/user-attachments/assets/34287da3-f881-4b44-8060-d928a2e6c76d" /> <br/><br/> |
|     5.2 | **Join each client to the domain:** On each client machine, open System Properties (right-click on This PC > Properties > Advanced system settings), click on Change next to To rename this computer or change its domain or workgroup, select Domain, enter the domain name (e.g., jmsoft.local), provide the credentials of a domain user, and restart each client machine after joining the domain. <br/><br/> Once this is done, I should be able to join the domain correctly, as the client computer can now correctly resolve the domain name. Now, I will try to join again through 'Change the name of this PC (Advanced):' <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/7da3899b-7739-4c04-8591-68283805f374" /> <br/><br/> Once the network configurations are completed, I need to enter credentials. The user account I need to enter must have at least 'Add computer to the domain' permissions. This permission can be granted by a Domain Admin. <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/5b104ada-9ed4-455a-ad77-01d934060b08" /> <br/><br/> Once the credentials are entered, a message will appear indicating that the computer has been successfully joined to the domain.  <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/b28560b9-f123-41f5-b36b-cb6aa67bb6d7" /> <br/><br/> For this to take effect, simply restart the computer and log in with an account that belongs to the 'jmfsoft.local' domain: <br/><br/> <img width="838" height="957" alt="Image" src="https://github.com/user-attachments/assets/b1e5d2fc-8187-41e7-81bc-8600d98f5af8" /> <br/><br/> Now, at the next login, the same message that appeared on the Domain Controller will appear: 'Sign in to JMFSOFT: <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/09075294-c647-441f-9cdb-3c57cba1ba65" /> <br/><br/> Simply enter the credentials of a user who belongs to the domain, and step 5.2 is complete. Next, I will perform the same process (joining devices to the domain) with the rest of the client computers and the additional server. |
| **6. Create Organizational Units (OUs) in Active Directory for each department** |   |
|     6.1 | **Open Active Directory Users and Computers (ADUC) on DC1**: Go to Server Manager > Tools > Active Directory Users and Computers (ADUC) or Active Directory Administrative Center (ADAC). <br/><br/> **1 - ADAC (Active Directory Administrative Center):** <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/09050af7-ed91-4314-8cd9-c907c833007b" /> <br/><br/> **Modern User Interface**: ADAC has a more modern and intuitive user interface, based on PowerShell and Windows Presentation Foundation (WPF) technologies. It was introduced with Windows Server 2008 R2. <br/><br/> **Advanced Features**: It offers advanced features such as the Active Directory Recycle Bin, which allows the recovery of deleted objects without having to restore the entire system. It enables more easily configurable Role-Based Access Control (RBAC). It incorporates PowerShell Search and command history, which facilitates task automation. <br/><br/> **Improved Security**: It supports smart card authentication and other enhanced authentication methods. <br/><br/> **Simplified Management**: It provides a consolidated and simplified view to manage Active Directory objects, such as users, groups, organizational units, and others. <br/><br/> **2 - ADUC (Active Directory Users and Computers):** <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/0ce8d3a0-911a-41d1-8f0c-2445b6ca5213" /> <br/><br/> **Classic User Interface**: ADUC has a more traditional user interface, based on the Microsoft Management Console (MMC) snap-in. It is an older tool compared to ADAC and has been used since Windows 2000. <br/><br/> **Basic Features**: It allows basic management of users, groups, computers, and organizational units in Active Directory. It does not have direct access to some of the more advanced features available in ADAC, such as the AD Recycle Bin. <br/><br/> **Common Use in Classic Scenarios**: Although it is more basic, it is still widely used for daily administration tasks due to its familiarity and simplicity. <br/><br/> **Compatibility**: It is compatible with earlier versions of Windows Server and remains a reliable tool for direct management of objects in Active Directory. <br/><br/> |
|     6.2 | **Create OUs**: Right-click on the domain (e.g., jmsoft.local) and select New > Organizational Unit. Create OUs for each department (e.g., IT, Finance, Sales, HR, Marketing, Development, Customer Service, Design, Administration). <br/><br/> To create organizational units (OUs), I have two options: do it from Active Directory Users and Computers (ADUC) or from Active Directory Administrative Center (ADAC): <br/><br/> **1 - Create from Active Directory Users and Computers (ADUC)**: To do this, I go to Server Manager, Tools, and select Active Directory Users and Computers. Then, once inside ADUC, I expand the domain (jmfsoft.local) and select where I want to create the organizational unit. <br/><br/><img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/e2cd7d01-bf00-491a-9449-bf8296b21402" /> <br/><br/> It is important to note that organizational units (OUs) cannot be created within containers, nor can group policies (GP) be applied to them. In this case, I will create an organizational unit called 'Argentina' within the domain and then create within it all the OUs related to the departments of the company at the Argentina site (assuming it is a company with many locations): <br/><br/><img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/d4822860-66f8-4cd4-b42a-82047c3f5232" /> <br/><br/> **2 - Create from Active Directory Administrative Center (ADAC)**: To do this, I go to Server Manager, Tools, and select Active Directory Administrative Center. Then, once inside, I select the location, right-click, New, and select 'Organizational Unit'. <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/9f9086bf-e29a-46f7-8bf4-289e2a2910c0" /> <br/><br/> Then, I complete the creation of the company's organizational units (IT, HR, Finance, etc.). <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/6af4dae0-9036-4a17-9957-3010983b6b4a" /> <br/><br/> |
|     6.3 | **Move user accounts to OUs**: After creating user accounts, move each account to the appropriate OU by right-clicking on the user, selecting Move, and choosing the appropriate OU. <br/><br/> Although user creation is part of the next section, I will create a test user and move it to a previously created organizational unit (in this case, IT) just to complete point 6.3 and finish with section 1 (Homelab Creation). <br/><br/> In this case, I will create the user named 'Prueba' in Customer Service and then move it to the IT Organizational Unit. <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/80b57cc4-3cca-4df8-b1dd-a7e88e2eedef" /> <br/><br/> Then, to move the user, I simply right-click on it and select 'Move...'. Now, I just need to choose which organizational unit (OU) to move it to. In this case, I will move it to IT: <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/47f5c40f-ba06-4c7e-a031-decd9df77382" /> <br/><br/> Once done, the user has been successfully moved from 'Customer Service' to 'IT'. <br/><br/> <img width="2560" height="1600" alt="Image" src="https://github.com/user-attachments/assets/b2890311-6ceb-4c5a-98f2-311154d35cd5" /> |

-------------------------------------------------------------------------------------------------

## User and Group Creation

### User Accounts and Domain Access

Although we are creating only four virtual machines to simulate end-user workstations, we will create 20 distinct user accounts within Active Directory. This approach avoids the unnecessary complexity and resource consumption of creating 20 separate machines, while still allowing us to manage and test all 20 user accounts effectively. Each user will be able to log into any of the four client machines and access the domain as if they were on a unique machine.

### User List

1. **Juan Martín Franco**
   - **Username:** juanma
   - **Occupation:** IT Support
   - **Email:** juanmafranco@jmfsoft.com
   - **Phone:** 2325 65 1813
   - **Department:** IT
   - **Location:** Argentina

2. **Bob Smith**
   - **Username:** bsmith
   - **Occupation:** Sysadmin
   - **Email:** bob.smith@jmfsoft.com
   - **Phone:** (555) 123-4562
   - **Department:** IT
   - **Location:** San Francisco

3. **Carol Davis**
   - **Username:** cdavis
   - **Occupation:** Finance Manager
   - **Email:** carol.davis@jmfsoft.com
   - **Phone:** (555) 123-4563
   - **Department:** Finance
   - **Location:** Chicago

4. **David Brown**
   - **Username:** dbrown
   - **Occupation:** Sales Representative
   - **Email:** david.brown@jmfsoft.com
   - **Phone:** (555) 123-4564
   - **Department:** Sales
   - **Location:** Miami

5. **Eve Miller**
   - **Username:** emiller
   - **Occupation:** HR Manager
   - **Email:** eve.miller@jmfsoft.com
   - **Phone:** (555) 123-4565
   - **Department:** HR
   - **Location:** Los Angeles

6. **Frank Wilson**
   - **Username:** fwilson
   - **Occupation:** IT Support
   - **Email:** frank.wilson@jmfsoft.com
   - **Phone:** (555) 123-4566
   - **Department:** IT
   - **Location:** New York

7. **Grace Moore**
   - **Username:** gmoore
   - **Occupation:** Marketing Specialist
   - **Email:** grace.moore@jmfsoft.com
   - **Phone:** (555) 123-4567
   - **Department:** Marketing
   - **Location:** Boston

8. **Hank Taylor**
   - **Username:** htaylor
   - **Occupation:** Developer
   - **Email:** hank.taylor@jmfsoft.com
   - **Phone:** (555) 123-4568
   - **Department:** Development
   - **Location:** San Francisco

9. **Ivy Anderson**
   - **Username:** ianderson
   - **Occupation:** Product Manager
   - **Email:** ivy.anderson@jmfsoft.com
   - **Phone:** (555) 123-4569
   - **Department:** Product
   - **Location:** Seattle

10. **Jack Thomas**
    - **Username:** jthomas
    - **Occupation:** Sysadmin
    - **Email:** jack.thomas@jmfsoft.com
    - **Phone:** (555) 123-4570
    - **Department:** IT
    - **Location:** San Francisco

11. **Kathy White**
    - **Username:** kwhite
    - **Occupation:** Customer Service Representative
    - **Email:** kathy.white@jmfsoft.com
    - **Phone:** (555) 123-4571
    - **Department:** Customer Service
    - **Location:** Houston

12. **Leo Harris**
    - **Username:** lharris
    - **Occupation:** Network Engineer
    - **Email:** leo.harris@jmfsoft.com
    - **Phone:** (555) 123-4572
    - **Department:** IT
    - **Location:** New York

13. **Mona Martin**
    - **Username:** mmartin
    - **Occupation:** Content Writer
    - **Email:** mona.martin@jmfsoft.com
    - **Phone:** (555) 123-4573
    - **Department:** Marketing
    - **Location:** Boston

14. **Nate Jackson**
    - **Username:** njackson
    - **Occupation:** Database Administrator
    - **Email:** nate.jackson@jmfsoft.com
    - **Phone:** (555) 123-4574
    - **Department:** IT
    - **Location:** Chicago

15. **Lara Vega**
    - **Username:** laravega
    - **Occupation:** Graphic Designer
    - **Email:** laravega@jmfsoft.com
    - **Phone:** (555) 123-4575
    - **Department:** Design
    - **Location:** Argentina

16. **Paul King**
    - **Username:** pking
    - **Occupation:** IT Support
    - **Email:** paul.king@jmfsoft.com
    - **Phone:** (555) 123-4576
    - **Department:** IT
    - **Location:** New York

17. **Quinn Scott**
    - **Username:** qscott
    - **Occupation:** Sales Representative
    - **Email:** quinn.scott@jmfsoft.com
    - **Phone:** (555) 123-4577
    - **Department:** Sales
    - **Location:** Miami
  
18. **Rachel Adams**
    - **Username:** radams
    - **Occupation:** Finance Analyst
    - **Email:** rachel.adams@jmfsoft.com
    - **Phone:** (555) 123-4578
    - **Department:** Finance
    - **Location:** Chicago

19. **Sam Turner**
    - **Username:** sturner
    - **Occupation:** DevOps Engineer
    - **Email:** sam.turner@jmfsoft.com
    - **Phone:** (555) 123-4579
    - **Department:** IT
    - **Location:** Seattle

20. **Tina Phillips**
    - **Username:** tphillips
    - **Occupation:** Office Manager
    - **Email:** tina.phillips@jmfsoft.com
    - **Phone:** (555) 123-4580
    - **Department:** Administration
    - **Location:** Los Angeles


### User creation through Azure Active Directory (currently Microsoft Entra ID)

### 1) Create User Account

1. In the your **Microsoft 365 admin account** expand the **Entra ID** dropdown
2. Under **Users** select **All Users** then **New User** and **Create New User**

<p align="center">
  <img width="1001" height="570" alt="Capture1" src="https://github.com/user-attachments/assets/cdbd8dea-1115-4b4f-a0e2-34150ac2e6fc" />
</p>

3. Create the user using the following information
  - **User Principle Name:** MichaelS
  - **Display Name:** Michael Scott
4. Enable **Auto-generate password** then select **Review and Create** then **Create** the user

<p align="center">
  <img width="714" height="603" alt="Capture2" src="https://github.com/user-attachments/assets/8d1534f2-8c15-4f1c-9b35-f6f0a9c02cff" />
</p>

---

### 2) Assign User the Application Admin Role

1. On the **Users** page select **Michael Scott**
2. Choose **Assigned Roles** and select **Add Assignments**

<p align="center">
  <img width="891" height="621" alt="Capture3" src="https://github.com/user-attachments/assets/40a60986-3d26-4167-b169-4981c3cc7b72" />
</p>

3. Select **Application Administrator** role then select **Next**

<p align="center">
  <img width="637" height="492" alt="Capture4" src="https://github.com/user-attachments/assets/fcec9dc8-401c-4585-956a-a027f25a1d24" />
</p>

4. For the **Assignment Type** choose the **Active** value then enter a justification and press **Assign**

<p align="center">
  <img width="570" height="556" alt="Capture5" src="https://github.com/user-attachments/assets/3b7fcc32-e266-4881-a29d-3fa27de11ff2" />
</p>

5. Hit the **Refresh** button to confirm the assignment was a success

<p align="center">
  <img width="1089" height="258" alt="Capture6" src="https://github.com/user-attachments/assets/ff781a51-0d70-4e8f-a735-2022a6370f48" />
</p>

---

### 3) Remove Assigned Role From a User

1. Select the **Roles and Administrators** tab
2. In the **All Roles** select the **Application Administrator** role from the list

<p align="center">
  <img width="1527" height="733" alt="Capture7" src="https://github.com/user-attachments/assets/cc02116e-3b06-4146-92ed-c13578f55da0" />
</p>

3. For the user named **Michael Scott** select the **Remove** option and select **Yes**

<p align="center">
  <img width="1330" height="234" alt="Capture8" src="https://github.com/user-attachments/assets/6a74f2ed-c242-4482-9542-0c9ffb98d1c3" />
</p>

4. **Refresh** and confirm that **Michael Scott** is no longer listed as having the **Application Administrator** role

---

### 4) Bulk Import Users using a CSV

1. Select the **Users** tab and make sure **All Users** is open
2. Select the **Bulk Operations** dropdown then select **Bulk Create**
3. Where it says **Upload Your CSV File** upload [this file](https://raw.githubusercontent.com/RyanKennon/Microsoft-Entra-ID/refs/heads/main/TheOfficeCSV.csv) and press **Submit**

<p align="center">
  <img width="1592" height="533" alt="Capture10" src="https://github.com/user-attachments/assets/965ecfbb-f9b9-4b4e-8a67-cd197a17c4f3" />
</p>

4. **Refresh** the page to confirm users were created.

---

### 5) Delete and Restore a User

1. Check the box next to **Ryan Howard** then press **Delete**

<p align="center">
  <img width="858" height="700" alt="Capture11" src="https://github.com/user-attachments/assets/f7858bc6-2e11-46bb-9b52-c6e88b3721c7" />
</p>

2. Select **Deleted Users** on the **Navigation Menu**
3. Check the box next to **Ryan Howard** then press **Restore Users**

<p align="center">
  <img width="1080" height="466" alt="Capture12" src="https://github.com/user-attachments/assets/8e303c85-1326-4ff0-b0a7-57426fc7095b" />
</p>

4. Navigate back to the **All Users** tab and confirm **Ryan Howard** appears

---

### 6) Update a Users Usage Location

1. Select **Properties** then scroll down until you find **Usage Location**
2. Make sure the location says **United States**

<p align="center">
  <img width="307" height="148" alt="Capture13" src="https://github.com/user-attachments/assets/080107c1-e49e-4fd8-9f83-97fb01479b02" />
</p>

3. If it doesn't say **United States** press the **Pencil** next to where it says **Settings**
4. In the **Usage Location** drop down select **United States** then **Save**

<p align="center">
  <img width="839" height="276" alt="Capture14" src="https://github.com/user-attachments/assets/8cbfe2bf-1cfa-4fba-9292-846b62ef2623" />
</p>

---

### 7) Add a Microsoft Entra ID P2 License to a User Account

1. Open the **Microsoft 365 Admin Center** and log in as your **Administrator Account**
2. On the **Navigation Menu** open the **Billing** section then select **Licenses**
3. Select the **Microsoft Entra ID P2** License

<p align="center">
  <img width="1050" height="675" alt="Capture16" src="https://github.com/user-attachments/assets/deca2b70-a3c6-4d38-9e62-456f1ebfd54c" />
</p>

4. Choose **Assign Licenses**
5. Search **Michael Scott** and select **Assign Licenses** at the bottom of the page

<p align="center">
  <img width="565" height="846" alt="Capture17" src="https://github.com/user-attachments/assets/b57c113f-2bfa-459f-b480-b61ce8f7adbd" />
</p>

---

### 8) Confirm User Has an Active License

1. Go back to the **Microsoft Entra Admin Center**
2. On the **All Users** page select **Michael Scott**
3. Select the **Licenses** menu item on the left and ensure that it says **AAD_PREMIUM_P2**

<p align="center">
  <img width="1124" height="637" alt="Capture15" src="https://github.com/user-attachments/assets/cfccf57b-f933-40f5-963a-cdd510abc7e2" />
</p>

---

### 9) Create Custom Subdomains

1. Open the **Domain Names** tab then select **Add Custom Domains**

<p align="center">
  <img width="1182" height="540" alt="Capture18" src="https://github.com/user-attachments/assets/ee92ddd1-b3b5-4589-8197-e82aa273c5cd" />
</p>

2. In the **Custom Domain** field create a custom subdomain for the sales department using the following format then select **Add Domain**
   `Sales.LabTenantName.onmicrosoft.com`

<p align="center">
  <img width="300" height="219" alt="Navigation2" src="https://github.com/user-attachments/assets/09d88988-53a0-4e2b-88d8-8232550ae5e5" />
</p>

3. Open the **Microsoft 365 Admin Center** link
4. Once there open the **Domains** tab and select **Add Domain**

<p align="center">
  <img width="1091" height="530" alt="Capture19" src="https://github.com/user-attachments/assets/c50eadcf-b2a0-4251-88d2-069e333be99b" />
</p>

5. In the **Domain Name** field create a custom subdomain for the sales department using the following format then select **Use This Domain**
   `Sales.LabTenantName.onmicrosoft.com`

<p align="center">
  <img width="671" height="261" alt="Capture20" src="https://github.com/user-attachments/assets/a117d3ce-ad57-4139-9bc9-9b64f2206bbe" />
</p>

6. Go back to the **Microsoft Entra Admin Center** and select **Refresh** on the **Custom Domain Names** page to find the new domain

<p align="center">
  <img width="1168" height="445" alt="Capture21" src="https://github.com/user-attachments/assets/b2674730-0761-455f-b094-118dd24f223a" />
</p>

---

### 10) Changing the Tenant Display Name

1. Open the **Overview** menu and select **Properties**
2. Change the **Name** and the **Technical Contact** to the following then select **Save**
  `**Name:** Dunder-Mifflin
   **Technical Contact:** *Your Global Admin Account*`

<p align="center">
  <img width="1092" height="681" alt="Capture22" src="https://github.com/user-attachments/assets/6207c6f0-4a37-4261-a87d-bcfad0373159" />
</p>

---

### 11) Setting Your Privacy Information

1. In the **Overview** menu in the **Properties** tab
2. Enter the email address for **Michael Scott** as the **Global Privacy Contact**
3. Enter this **[URL](https://github.com/RyanKennon/Microsoft-Entra-ID/blob/main/Privacy%20Statement%20Sample.pdf)** into the **Privacy Statement URL** text box then **Save**

<p align="center">
  <img width="723" height="288" alt="Capture23" src="https://github.com/user-attachments/assets/61405bcf-b2c6-4224-a1ff-9a458af4b4b5" />
</p>

---

### 12) Check Your Privacy Statement

1. Select your **Username** in the top right of the screen then select **View Account**

<p align="center">
  <img width="320" height="207" alt="Capture24" src="https://github.com/user-attachments/assets/5c79f75a-f8a1-4363-9358-0da2689df78d" />
</p>

2. Select the **Settings & Privacy** menu then select **Privacy**
3. Under **Organizational Privacy Statement** select **View**

<p align="center">
  <img width="1372" height="727" alt="Capture25" src="https://github.com/user-attachments/assets/36f0a534-064c-4e01-890a-67074e550bf5" />
</p>

4. Confirm the **Privacy Statement** pops up

---

### 13) Create a Security Group

1. In the **Groups** menu select the **All Groups** tab then select **New Group**

<p align="center">
  <img width="976" height="452" alt="Capture26" src="https://github.com/user-attachments/assets/8ed2f07a-e7f2-4b7a-9ecb-15e0dfdfe94c" />
</p>

2. Create a group using the following information:
   `**Group Type:** Security
   **Group Name:** sg-dm
   **Membership Type:** Assigned
   **Owners:** *Assign your administrator account as the group owner*
   **Members:** Dwight Schrute`

<p align="center">
  <img width="703" height="464" alt="Capture27" src="https://github.com/user-attachments/assets/ad632834-62f2-4792-8072-8231c4638d0f" />
</p>

3. Select **Create** at the bottom of the page
4. Verify the group is in the **All Groups** list

<p align="center">
  <img width="1043" height="355" alt="Capture28" src="https://github.com/user-attachments/assets/54fb8a30-5ea3-45e4-8ec4-577d717ee9f3" />
</p>

---

### 14) Create a Microsoft 365 group in Microsoft Entra ID

1. On the **All Groups** page select **New Group**
2. Create a group with the following information:
   `**Group Type:** Microsoft 365
   **Group Name:** Sales Representatives
   **Membership Type:** Assigned
   **Owners:** *Assign your administrator account as the group owner*
   **Members:** Jim Halpert, Dwight Schrute, Andy Bernard, Stanley Hudson, Phyllis Lapin`

<p align="center">
  <img width="755" height="604" alt="Capture29" src="https://github.com/user-attachments/assets/1b6a316d-0a93-487c-880b-a441ca25d4a6" />
</p>

3. Verify the group is in the **All Groups** list

<p align="center">
  <img width="1059" height="459" alt="Capture30" src="https://github.com/user-attachments/assets/d6e80dc7-3ba6-440d-ba85-fb98bb748863" />
</p>

---

### 15) Creating a Dynamic Group with All Users as Members

1. On the **All Groups** page select **New Group**
2. Enter the following information but do not create the group:
   `**Group Type:** Security
   **Group Name:** DynamicGroup
   **Membership Type:** Dynamic User
   **Owners:** *Assign your administrator account as the group owner*`
3. Where is says **Dynamic Users Members** select **Add Dynamic Query**

<p align="center">
  <img width="742" height="581" alt="Capture31" src="https://github.com/user-attachments/assets/08dd63a2-9616-4e7d-904f-f8b92f440f91" />
</p>

4. Above the **Rule Syntax** box select **Edit**
5. Enter the following expression in the **Rule Syntax** box then press **Ok**:
   `user.objectId -ne null`

<p align="center">
  <img width="841" height="148" alt="Capture32" src="https://github.com/user-attachments/assets/9061148c-befa-41d9-a651-4a6efb88d513" />
</p>

6. Select **Save** then **Create**
7. Verify the group is in the **All Groups** list

<p align="center">
  <img width="1305" height="515" alt="Capture33" src="https://github.com/user-attachments/assets/95905673-5afe-45d9-b82e-9c5aab9958c2" />
</p>

8. Open the **DynamicGroup** then select **Members**
9. Review the members

---

### 16) Enable Guest Users to Perform Self Service Sign-Up

1. On the **Users** menu navigate to **User Settings** and select **Manage External Collaboration Settings**

<p align="center">
  <img width="854" height="670" alt="Capture34" src="https://github.com/user-attachments/assets/9bcaef76-807a-4025-adf2-8bcfa1ad9dbd" />
</p>

2. For the option labeled **Enable Guest Self-Service Sign Up Via User Flows** mark **Yes**
3. **Save** at the top of the screen

<p align="center">
  <img width="317" height="104" alt="Capture35" src="https://github.com/user-attachments/assets/d246f918-b19f-4bab-bbe9-fc5d09a6ca5f" />
</p>

---

### 17) Configure External Collaboration Settings

1. Open the **External Identities** menu then select **All Identity Providers**
2. Find where it says **Email One-Time Passcode** then select **Configured**

<p align="center">
  <img width="826" height="564" alt="Capture36" src="https://github.com/user-attachments/assets/8692d091-fabe-4636-acb3-29089a34b71d" />
</p>

3. For **Email One-Time Passcode for Guests** select **Yes** then **Save**

<p align="center">
  <img width="234" height="76" alt="Capture37" src="https://github.com/user-attachments/assets/1ab0ff6a-8b36-4653-906c-8bce43f8b247" />
</p>

4. Select the **External Collaboration Settings**
5. Find where it says **Guest User Access** select **Guest User Access is Restricted to Properties and Memberships of Their Own Directory Objects (Most Restrictive)**

<p align="center">
  <img width="704" height="171" alt="Capture38" src="https://github.com/user-attachments/assets/1da09bdc-8a74-4001-834a-8d93573cf8e7" />
</p>

6. Find the **Guest Invite Settings** select **Member Users and Users Assigned to Specific Admin Roles Can Invite Guest Users Including Guests with Member Permissions**

<p align="center">
  <img width="774" height="197" alt="Capture39" src="https://github.com/user-attachments/assets/966fe1aa-e03c-4e24-bb25-93e42a970bfd" />
</p>

7. **Save** your changes

---

### 18) Add a Guest User to the Directory

1. Open the **Users** menu, on the **All Users** page select **New User** then **Invite External User**
2. Enter the email `externaluser@email.com` for the **Email**

<p align="center">
  <img width="839" height="49" alt="Capture40" src="https://github.com/user-attachments/assets/d14ed54d-e4d4-4f55-a8c6-a63232afbb15" />
</p>

3. Select the **Properties** tab
4. For the **User Type** make sure it is filled in as **Guest**

<p align="center">
  <img width="830" height="30" alt="Capture41" src="https://github.com/user-attachments/assets/3f5f81dd-e3b9-42e9-be4f-675d5fbf5a7a" />
</p>

5. Select **Review and Invite** then **Invite**

---

### 19) Bulk Invite Guest Users

1. On the **All Users** page select **Bulk Operations** then **Bulk Invite**
2. Where it says **Download CSV Template** select download and follow along with the template to create guest users
3. Enter this file into where it says **Upload Your CSV File** then **Submit**

<p align="center">
  <img width="297" height="274" alt="Capture42" src="https://github.com/user-attachments/assets/8b828459-1a13-4c2c-95ff-871e654e2743" />
</p>

4. Verify the guest users appear in the **All Users** page

---

### 20) Configure Google to be Used as an Identity Provider

1. Go to the [**Google APIs Site**](https://console.cloud.google.com/projectselector2/apis/dashboard?pli=1&supportedpurview=project&authuser=1) and sign in with your **Google Account**
2. Click **Select a Project** then **New Project** and select **create**
3. Open the project by clicking **Select Project** on the **Notifications** page

<p align="center">
  <img width="402" height="176" alt="Capture43" src="https://github.com/user-attachments/assets/4100de45-1ff2-447d-a879-7e926b7b5b91" />
</p>

4. On the left side of the screen select the **OAuth Consent Screen** then select **Get Started**
5. Enter the following information to create the project
   `**App Information**
   **App Name:** Microsoft Entra ID
   **User Support Email:** Select email name form dropdown menu
   **Audience**
   **Internal/External:** External
   **Contact Information:** Use User Support Email address
   **Finish**
   **Mark the agreement checkbox**`
6. Select the **Create OAuth Client** button
7. For the **Application Type** choose **Web Application**
8. Under **Authorized JavaScript Origins** select **Add URI** then add `https://microsoftonline.com` as the URI

<p align="center">
  <img width="580" height="531" alt="Capture44" src="https://github.com/user-attachments/assets/7a04389f-d9b3-42da-a572-2aedca770788" />
</p>

9. Under **Authorized Redirect URIs** select **Add URI**
    `**First URI:** https://login.microsoftonline.com
   **Second URI:** https://login.microsoftonline.com/te/**tenant ID**/oauth2/authresp
   **Third URI:** https://login.microsoftonline.com/te/**tenant name**.onmicrosoft.com/oauth2/authresp`

<p align="center">
  <img width="503" height="289" alt="Capture45" src="https://github.com/user-attachments/assets/9ecfcdbd-bbfc-4160-af1a-1dea8f6e9b5c" />
</p>

10. Once everything is entered select **Create**

---

### 21) Add a Test User in Google

1. On the **Google API Site** select the **Audience** tab on the left side of the screen
2. Scroll down to **Test Users** then select **Add Users**

<p align="center">
  <img width="158" height="106" alt="Capture46" src="https://github.com/user-attachments/assets/22742c20-318f-476b-82d8-760b128feea1" />
</p>

3. Enter **The Email Account Your Using for This Lab** then select **Save**

---

### 22) Add Authorized Domain to Branding

1. On the **Google API Site** select the **Branding** tab on the left side of the screen
2. Find the **Authorized Domains** section at the bottom of the page then select **Add Domain** and add the domain `microsoftonline.com`
3. In the **Developer Contact Information** make sure the email address your using in this lab appears in the box
4. Select **Save**

<p align="center">
  <img width="542" height="341" alt="Untitled Diagram-Page-47 drawio" src="https://github.com/user-attachments/assets/70b784e3-38d3-4d0f-abf7-82eec5155948" />
</p>

---

### 23) Find Google Client ID and Client Secret

1. On the **Google API Site** select the **Clients** tab on the left side of the screen
2. On the right side of the screen under **Additional Information** the **Client ID** appears
3. Below that under the **Client Secrets** the **Client Secret** appears

<p align="center">
  <img width="813" height="585" alt="Untitled Diagram-Page-49 drawio" src="https://github.com/user-attachments/assets/5a45b3e1-688d-4227-91a4-7b605b692d4d" />
</p>

---

### 24) Configure Microsoft Entra ID for Google Federation

1. On the **Microsoft Entra ID Admin Center** open the **External Identities** tab
2. Open the **All Identity Providers** page then select **Configure** next to **Google**

<p align="center">
  <img width="757" height="493" alt="Capture48" src="https://github.com/user-attachments/assets/97d60da4-bf38-4508-a136-bd9ee3cee451" />
</p>

3. Enter the **Client ID** and the **Client Secret** then select **Save**
4. Verify that on the **External Identities** page it says **Configured** next to **Google**

<p align="center">
  <img width="493" height="314" alt="Capture50" src="https://github.com/user-attachments/assets/7a495df2-f68e-418f-b054-71b6db6804f3" />
</p>

---

### 25) Invite the Test User Account

1. Open the **All Users** page in **Microsoft Entra ID Admin Center**
2. Select **New User** then **Invite External User**

<p align="center">
  <img width="277" height="181" alt="Capture51" src="https://github.com/user-attachments/assets/df7f5899-4f1d-44b4-b028-2836a9f35a01" />
</p>

3. Enter the email account that you were using while entering information on the Google API site
4. Select **Review and Invite** then **Invite**

---

### 26) Accept the Invitation and Login

1. Open the email account that you have been using during these previous steps
2. Open the **Microsoft Invitation on Behalf of** email then select **Accept Invitation**

<p align="center">
  <img width="703" height="325" alt="Capture52" src="https://github.com/user-attachments/assets/94585805-58e8-4345-9af0-d09498c531c3" />
</p>

3. After logging in it will take you to the **Apps Dashboard** with the correct user information on the left side of the screen

<p align="center">
  <img width="1383" height="403" alt="Navigation6" src="https://github.com/user-attachments/assets/ef7713b7-2638-4538-88c0-a217ab85ee9d" />
</p>

<br/>

-------------------------------------------------------------------------------------------------

## Group Policy Management and Implementation

GPOs (Group Policy Objects) are a feature of Windows Server operating systems that allow administrators to centrally manage and configure policies and settings for systems and users in an Active Directory domain network. 

Basically, GPOs are collections of policy settings that can be applied to users and computers in an Active Directory-based network environment. These policies can cover a wide range of settings, from system security to user environment customization.

<img width="519" height="327" alt="Image" src="https://github.com/user-attachments/assets/067de04b-3334-40a2-82be-51d1464ccff6" />

There are some basic concepts of GPOs that need to be understood before starting to configure them:

### GPO (Group Policy Objects) linking

GPOs must be linked to a container in Active Directory to be applied. 

Containers can be sites, domains or Organizational Units (OUs).

<img width="662" height="484" alt="Image" src="https://github.com/user-attachments/assets/383a9555-a473-48d7-ae33-d3a9684ec841" />

### GPO Inheritance / Precedence

GPO inheritance refers to how policies configured in GPOs are applied and inherited through the Active Directory hierarchy. 

The hierarchy follows this order:

1. **Local**: Local policies on the computer.
2. **Site**: Policies linked to a site in Active Directory.
3. **Domain**: Policies linked to the domain.
4. **Organizational Units (OUs)**: Policies linked to specific OUs.

Order of Application:

1. **Local**: Local policies are applied first.
2. **Site**: Site policies are applied after.
3. **Domain**: Domain policies are applied next.
4. **OUs**: OU policies are applied in order from the top-level OU to the most specific OU containing the object (user or equipment).

<img width="729" height="359" alt="Image" src="https://github.com/user-attachments/assets/78a8505e-496a-4721-8a20-2c82ed8fe4c6" />

### Inheritance Modifiers

Mechanisms exist to modify how GPOs are inherited.

1. **Inheritance Blocking**:

    You can block inheritance of GPOs into a specific OU. This means that higher level policies will not be applied to that OU.
    
    How to enable: In the GPMC, select the OU, right click and select "Block Inheritance".

2. **Enforced**:

    A GPO can be marked as "Enforced", which means that its policies cannot be blocked by any lower OU.
    
    How to enable: In the GPMC, select the GPO, right click and select "Enforced".

3. **Security Filtering**:

    GPOs can be filtered to only apply to specific users or computers by using Security Groups.
    
    How to configure: In the GPMC, select the GPO, go to the "Scope" tab and adjust the permissions in the Security Filtering section.

Now that all the basic concepts are understood, it is time to show the steps for the application of such group policies.

### Steps to configure and apply a GPO

   1. **Open the Group Policy Management Console (GPMC)**:
   
      Log on to a domain controller or machine with the administrative tools installed (RSAT). <br/>
      Open the Group Policy Management Console (GPMC). You can do this by searching for "gpmc.msc" in the Start menu or through Administrative Tools.

      <img width="1500" height="1051" alt="Image" src="https://github.com/user-attachments/assets/803cb9b6-6e76-490d-a3a3-e71bb9f9e6a6" />


   2. **Create a New GPO**:
   
      In the GPMC, navigate to the container in which you want to create the GPO. This can be a domain, site, or an organizational unit (OU). <br/>
      Right-click on the container and select "Create a GPO in this domain and link it here...". 

      In this case, I will create a GPO that applies to the entire domain. 

      <img width="1814" height="1350" alt="Image" src="https://github.com/user-attachments/assets/70997b8c-7bbe-485a-bcd7-5eb2d6ca013b" />

   3. **Name the GPO**:
   
      Assign a descriptive name to the new GPO. For example, "Password Policy" or "Desktop Wallpaper".  <br/>
      
      In this case, I will configure the GPO to apply a secure password policy. 

      <img width="1817" height="1348" alt="Image" src="https://github.com/user-attachments/assets/414b104d-c633-461c-ad81-a622bfdfe46b" />

   4. **Edit the GPO**:
      
      Once created, the new GPO will appear in the list of GPOs linked to the selected domain, site or OU. <br/>
      
      Right-click on the GPO and select "Edit" to open the Group Policy Management Editor.

      <img width="1804" height="1388" alt="Image" src="https://github.com/user-attachments/assets/8abfd2f3-44b1-40d8-9ca4-20207923724e" />

      Once this is done, the Group Policy Management Editor will open.

   5. **Configure Desired Policies**:
   
      In the Group Policy Management Editor, you can configure policies under two main categories:

        **Computer Settings**:

        - These policies are applied at the computer level. Examples include security settings, software installation, network policies, etc.

        **User Configuration**:

        - These policies are applied at the user level. Examples include desktop settings, folder redirection, software restrictions, etc.

      <img width="1891" height="1347" alt="Image" src="https://github.com/user-attachments/assets/60046ca6-6796-4a1a-85c8-0ac42d116c82" />

      In this case, I go to:

        **Computer Configuration > Windows Settings > Security Settings > Account Policies > Password Policy**.

      <img width="2049" height="1363" alt="Image" src="https://github.com/user-attachments/assets/d12222df-295e-404f-9ab1-3f81e4aec028" />

      Now, I double click on the policy to be modified and a window will open that will allow me to enable/disable this policy and also to modify the values.

      <img width="1006" height="1230" alt="Image" src="https://github.com/user-attachments/assets/4b2723a9-b8a6-4926-b113-8132f481e6d7" />

      Finally, I click on "Apply" and the password length policy is set to 8 characters long.

      <img width="2050" height="1350" alt="Image" src="https://github.com/user-attachments/assets/19f80737-3e9f-4ea6-8bf7-00d21fd49b40" />

   6. **Apply the GPO**:
   
      The GPO is already bound to the selected domain, site or OU, and the policies will be automatically applied to objects in that scope.

      The GPO will be processed on the next policy update cycle (typically every 90 minutes on computers and at user login). 

   7. **Force Policy Update**:

      To enforce policies immediately, you can force an update on affected computers and users:

        On the Domain Controller:

        - Run **gpupdate /force** at the command prompt to update policies on the domain controller.

        On Client Computers:

        - On each computer, **run gpupdate /force** at the command prompt to apply the new policies immediately. 

      <img width="2340" height="1216" alt="Image" src="https://github.com/user-attachments/assets/7f461708-e0ec-42a5-9418-2d773731441c" />


### Other examples of group policies that from my point of view would be good to apply

In addition to the password group policy that strengthens security, there are others that I believe are essential to implement in a business environment:

1. **Disable the use of USB devices**:

    **Policy**: Deny read and write access to removable storage devices.

    **Benefit**: Mitigates the risk of data loss and the introduction of malware through unauthorized USB devices.

    **Location**: Computer Configuration > Policies > Administrative Templates > System > Removable Storage Access

    <img width="2052" height="1349" alt="Image" src="https://github.com/user-attachments/assets/1d529b00-e48e-4d84-bb7a-b202a713edb9" />

2. **Disable the installation of unauthorized software**:

    **Policy**: Disable installation of devices matching any of these device IDs.

    **Benefit**: Ensures that only approved software is installed, reducing the risk of system vulnerabilities and conflicts.

    **Location**: Computer Configuration > Policies > Administrative Templates > System > Driver Installation

    <img width="2045" height="1351" alt="Image" src="https://github.com/user-attachments/assets/6a1c939e-91d4-4e8d-a936-2361c22c0222" />

3. **Disable access to the Control Panel and Settings**:

    **Policy**: Prohibit access to the Control Panel and Settings.

    **Benefit**: Prevents unauthorized changes to system settings and reduces the technical support burden due to inadvertent configuration.

    **Location**: User Configuration > Policies > Administrative Templates > Control Panel

    <img width="2046" height="1340" alt="Image" src="https://github.com/user-attachments/assets/f26905d5-f547-42f8-864d-417464a24ff5" />


-------------------------------------------------------------------------------------------------
## Password Reset
### 1. Locate User in Active Directory
- Open **Active Directory Users and Computers (ADUC)**.
- Search for the locked user (e.g., `wag.bewi`).
- <img width="1792" height="1120" alt="Image" src="https://github.com/user-attachments/assets/49f69223-deef-430e-babe-66df87cc449c" />

### 2. Reset Password and Unlock Account
- Right-click the user → Reset Password.
- Ensure **"Unlock the user's account"** is checked.
- <img width="1792" height="1120" alt="Image" src="https://github.com/user-attachments/assets/6539ba4c-a770-4a1c-91bd-b6c075c54817" />

### 3. Confirm Login on Client
- Log into the client machine using new credentials.
- Run `whoami` to verify domain and identity.
- <img width="1792" height="1120" alt="Image" src="https://github.com/user-attachments/assets/49a65bec-9a42-4d69-9643-6d2d514c3af4" />

---

## 🔍 Verify via Event Viewer

### 4. Search for Audit Events
- Open **Event Viewer** → Windows Logs → Security.
- Search for the username to track login events.
- <img width="1792" height="1120" alt="Image" src="https://github.com/user-attachments/assets/65e5c0a9-d04d-4891-8658-860a6890c172" />
- <img width="1792" height="1120" alt="Image" src="https://github.com/user-attachments/assets/7938147d-60fb-499a-a7d1-08ac9153a9cf" />

---
-------------------------------------------------------------------------------------------------

## Software Deployment

In this section, I will use a software deployment program, **PDQ Deploy**, to simplify and automate the installation and updating of applications across multiple machines in my homelab. The reasons for implementing a software deployment tool like PDQ Deploy are:

- **Efficiency and Time-Saving**: Manually installing or updating software on each client machine can be very time-consuming, especially in a larger environment. PDQ Deploy streamlines this process by allowing for the automated deployment of software packages across all connected devices, saving significant time and effort.
- **Consistency and Control**: Using PDQ Deploy ensures that all machines receive the same software version and configuration, reducing the risk of discrepancies and compatibility issues. This helps maintain a uniform and controlled IT environment.

- **Centralized Management**: PDQ Deploy provides a centralized interface to manage software installations, making it easier to track deployment status, manage software versions, and perform updates from a single point of control.

- **Scalability**: As my homelab grows, manually handling software installations becomes increasingly impractical. PDQ Deploy scales with the environment, allowing for efficient software management regardless of the number of machines involved.

In this case, I will do it from the secondary server (SV02) to balance the load and not overburden the domain controller (DC01).

This tool can be downloaded from the following website: [PDQ Deploy](https://www.pdq.com/pdq-deploy/)

Now, I proceed to download and install it on the secondary server (SV02).

To do this, I need to create an account, which will give me a free trial of the full program for 14 days, although the basic functionality (software deployment) can still be used after those 14 days:

<img width="856" height="481" alt="Image" src="https://github.com/user-attachments/assets/0c289ee4-6656-480c-9fa9-2ecc379bae60" />

Once registered, I am redirected to the following page where I get the links to download PDQ Deploy & PDQ Inventory, along with their licenses (which last for 14 days).

<img width="997" height="398" alt="Image" src="https://github.com/user-attachments/assets/2aaeaffc-fa6a-462c-9c17-0a1fe7123776" />

After installing (as a server, since SV02 will be in charge of deploying to client machines), the program interface will look like this:

<img width="933" height="530" alt="Image" src="https://github.com/user-attachments/assets/b0b42acb-de77-4906-9bab-f2133c043713" />

Now, to begin the deployment, first, I need to have the installer of the software I want to deploy downloaded and saved in a location.

In this case, for simplicity, I'll choose to install 7zip.

The installer will be located in a folder on the desktop named 'Software to Deploy':

<img width="728" height="370" alt="Image" src="https://github.com/user-attachments/assets/c5923d8f-ffb7-46d9-b60e-152f313e259d" />

Now, I open PDQ Deploy and select 'New Package'. Then, I fill in the fields.

<img width="800" height="432" alt="Image" src="https://github.com/user-attachments/assets/46f92119-889f-4be3-b960-aba0fae942ca" />
<img width="782" height="234" alt="Image" src="https://github.com/user-attachments/assets/2990c453-b4d4-45de-b626-cd38f98f4d64" />

Afterwards, I go to 'Steps' and then select 'Install':

<img width="772" height="432" alt="Image" src="https://github.com/user-attachments/assets/8a2f6f16-4eaa-456f-945c-2493f18931d1" />

Then, I select the installer, add the parameters for silent installation (in this case, the appropriate one for 7zip is /S) to avoid disturbing any user while they are working, and press save.

<img width="772" height="432" alt="Image" src="https://github.com/user-attachments/assets/8a2f6f16-4eaa-456f-945c-2493f18931d1" />

Once this is done, the package will appear already created. Now, all that's left is to right-click on it and press 'Deploy Once' to begin the deployment.

<img width="727" height="590" alt="Image" src="https://github.com/user-attachments/assets/7af5b61d-f843-4f17-aebf-031fd632f5d5" />

Finally, I must select the users/computers by clicking on 'Choose Targets', and that's it, I can start the deployment:

<img width="885" height="522" alt="Image" src="https://github.com/user-attachments/assets/05296fba-4f31-4c59-a4d8-b037fa235e8c" />

In this case, just for testing purposes, I'll select client machines 1 and 2 (JMFSOFT-PC01 and JMFSOFT-PC02):

<img width="894" height="396" alt="Image" src="https://github.com/user-attachments/assets/ac40e854-5c20-471a-a456-2a4348e33c83" />

After initiating the deployment, I get the following results:

<img width="794" height="434" alt="Image" src="https://github.com/user-attachments/assets/5e9f6d46-ee23-46f6-a372-a5801fe1dd7f" />

Now, I verify that 7zip was installed correctly on the client machines:

<img width="658" height="556" alt="Image" src="https://github.com/user-attachments/assets/648ae182-459e-44ab-900d-f9690a6c46c2" />

And, with this, I can successfully conclude the deployment.

-------------------------------------------------------------------------------------------------

## Remote Access and Troubleshooting

In this section of the project, remote access between company computers will be implemented for troubleshooting purposes.

This concept is fundamental in day-to-day IT support, since it is a required skill that is very useful because at times it may not be possible to physically access a computer (due to geographical issues, for example).

There are many tools on the market that facilitate remote access, such as TeamViewer or AnyDesk.
Even Windows comes with a tool built into the operating system itself, but for reasons of versatility I will use TeamViewer because it works on many operating systems and it is also my tool of choice, but I could easily use AnyDesk, which also has a very simple interface, less resource consumption and lower licensing costs.



<img width="256" height="256" alt="Image" src="https://github.com/user-attachments/assets/b481c98c-1df7-4e6a-af5d-521127c4205f" />

In this case, the connection will be made from the SV02 machine to the JMFSOFT-PC01 client machine.

To do this, I must perform the following steps:

1. First, I need to install TeamViewer on both computers.

    To download the executable I go to [https://www.teamviewer.com/](https://www.teamviewer.com/) and click on Free Download.

    <img width="1914" height="932" alt="Image" src="https://github.com/user-attachments/assets/17a3a3ba-1d2e-44a1-a631-d0f4f3462d2d" />

2. Once this is done, I select the desired plan. 
    
    In this case, I click on "Free", but in a business environment you may have to opt for the other licensing options.

    <img width="1912" height="932" alt="Image" src="https://github.com/user-attachments/assets/94cce050-6682-4caf-9208-cd4771389d65" />

    Now, I must choose which type of installation I want, each one with its respective functionalities and/or advantages as appropriate.

    Among the options are:

    - **TeamViewer QuickSupport**: No full installation is required; it is a lightweight, portable application. Ideal for quick and occasional support.

    - **TeamViewer Full Client**: Offers all TeamViewer functionalities for full remote access and control. Allows both inbound and outbound connection, which means you can control other devices or be controlled. Includes features such as file transfer, chat, and Wake-on-LAN.

    - **TeamViewer Host**: Provides continuous remote access to devices, ideal for long-term remote management. Requires installation on the device. Allows unlimited and permanent remote connections. Ideal for keeping computers and servers under constant surveillance and support.

    - **TeamViewer MSI Package**: Facilitates the distribution and management of TeamViewer installations through group policies in corporate networks. MSI format suitable for deploying and managing installation on multiple devices via Active Directory and GPOs. Supports custom configurations to suit specific needs. Allows automating installation and configuring pre-deployment settings.

    - **TeamViewer Meeting**: Dedicated platform for online meetings, video conferencing, and real-time collaboration. Provides tools for high-quality audio and visual communication. Includes screen sharing, chat, and meeting recording functionalities.

    Below is a comparative table showing the main features between each version:

    <img width="729" height="426" alt="Image" src="https://github.com/user-attachments/assets/4e5b5287-b65a-4e44-b4a9-623a29731313" />

    In this case I chose to install the "Full Client" version on the SV02, in its 64-Bits version.

3. Once downloaded and installed, the client is executed, which looks as follows:

    <img width="1192" height="916" alt="Image" src="https://github.com/user-attachments/assets/9831cfa1-8f9e-4d28-862e-6c46b6f40f48" />

    To make a connection, it is necessary to use the ID and password.

    These credentials should only be shared between the IT Support Technician and the person who wishes to receive help from the IT Support Technician, otherwise anyone could gain remote access to the company's equipment.

4. Now, I download and install the QuickSupport version on the JMFSOFT-PC01 computer (just for demonstration and simplicity, as I could have opted for the Full Client version).

    <img width="2044" height="1594" alt="Image" src="https://github.com/user-attachments/assets/5a4e1638-65cd-4b85-9eed-c88952c76209" />

5. Finally, just share the credentials of the client computer (JMFSOFT-PC01) with the IT Support manager so that he/she can enter them in the TeamViewer client to gain remote access and troubleshoot problems.
   
-------------------------------------------------------------------------------------------------
## VPN Configuration

In addition to using remote access to troubleshoot problems, a good practice is to use remote access via VPN.

A **Virtual Private Network (VPN)** is a technology that creates a secure, encrypted network connection over a public network, such as the Internet. It allows users to send and receive data securely over shared or public networks as if their devices were connected directly to a private network.

### Why is it considered a good practice?

- **Data Security:**

  - **Encryption:** VPNs use encryption protocols to protect the data transmitted between the user and the VPN server. This makes the information unreadable to anyone intercepting the connection.
  
  - **Authentication:** VPNs may require authentication, which ensures that only authorized users can access the network.
  
- **Privacy:**
  
  - **IP Hiding:** The VPN hides the user's IP address, which helps maintain online privacy by making user activity less traceable.
  
  - **Identity Protection:** By hiding the user's location and identity, VPNs protect against tracking and data collection by third parties.

- **Secure Remote Access:**
  
  - **Remote Work:** VPNs allow employees to securely access the corporate network from remote locations, which is crucial for teleworking and workforce mobility.
  
  - **Corporate Resources:** Users can access internal corporate resources, such as files, applications and databases, as if they were physically in the office.

- **Censorship and Geographical Restrictions Avoidance:**

  - **Accessing Blocked Content:** VPNs allow users to access websites and services that may be blocked or restricted in their geographic location.
  
  - **Censorship Avoidance:** They can be used to circumvent censorship in regions where Internet access is controlled or restricted.

- **Information Integrity:**

  - **Protection Against Tampering:** VPNs' encryption and security protocols protect data from alteration and tampering while in transit.


### Conclusion

Implementing VPNs is a best practice for its ability to provide a secure and private connection over public networks, protect sensitive information and allow secure remote access to network resources. This not only improves overall network security, but also ensures the integrity and privacy of transmitted data.

### Steps to set up a VPN

1. First, I must choose on which server I want to install the role.

    In this case I will choose to install the Remote Access role on the SV02 server.
    This helps distribute the workload and improves security by not directly exposing the domain controller to external connections.

    To start, I open the Server Manager:

    <img width="1536" height="912" alt="Image" src="https://github.com/user-attachments/assets/341a162a-1518-4d22-a8f8-0ae0d06192f4" />
 
2. Then, to add the role, I go to “Manage” and then “Add roles and features”:

    <img width="1134" height="652" alt="Image" src="https://github.com/user-attachments/assets/5cb24490-b060-4688-b1a4-197250b0b3b3" />

3. Now, I select the “Remote Access” role and install it:
   
   <img width="942" height="674" alt="Image" src="https://github.com/user-attachments/assets/9accfa4d-c9da-4724-8837-d3b7ae338d2e" />

4. Then, I select the “DirectAccess and VPN (RAS)” option:

    <img width="940" height="668" alt="Image" src="https://github.com/user-attachments/assets/84ae237d-34d2-4947-91f0-7e5c22a4bd67" />

    * **DirectAccess** is a remote access technology introduced in Windows Server 2008 R2. It allows remote users to access internal network resources without the need to manually initiate a VPN connection. DirectAccess automatically establishes a secure, persistent connection between the user's device and the corporate network, providing an experience similar to being in the office.
     
    * **VPN** is a technology that creates a secure, encrypted connection over a less secure network, such as the Internet. VPN allows remote users to access internal network resources as if they were directly connected to the network. 

5. Finally, I review the selected items and confirm that everything is correct to start with the installation of the Remote Access role, so I press “Install”:

    <img width="938" height="670" alt="Image" src="https://github.com/user-attachments/assets/e4d0bf32-c22d-417e-b740-1ee9bf118001" />

6. Once the installation is complete, a message will be displayed indicating that the installation was successful, but that configuration is required:

    <img width="938" height="670" alt="Image" src="https://github.com/user-attachments/assets/332c2ba1-e2fc-4ebe-b149-24bbb4cd3e4e" />

7. Now, it is time to configure the Virtual Private Network (VPN), which will allow remote access to employees in a distant geographical location.

    To do this, I must open the “Introduction Wizard”, which is displayed when I finish installing the “Remote Access” role:

    <img width="408" height="196" alt="Image" src="https://github.com/user-attachments/assets/cdade961-5dee-4b79-af8e-8bbabaf058a6" />

8. Once the wizard is opened, a window containing 3 options will be displayed:

    <img width="778" height="638" alt="Image" src="https://github.com/user-attachments/assets/1e398aae-e669-43c4-8792-e70c91ea541b" />

    * **Implement DirectAccess and VPN (Recommended)**: Implementing both VPN and DirectAccess offers flexibility, allowing users to choose between automatic (DirectAccess) or manual (VPN) connections based on their needs. This option provides compatibility across various devices and operating systems that do not support DirectAccess, such as macOS and Linux. DirectAccess ensures a seamless and continuous connection to the corporate network, ideal for users who require uninterrupted access. It also enhances security by utilizing multiple authentication and encryption methods. However, managing both technologies can be complex, requiring more time and resources, and DirectAccess requires IPv6, which might necessitate additional network configurations.

    * **Implement DirectAccess only**: Implementing DirectAccess Only provides a transparent connection experience, as users do not need to manually initiate the connection. It allows continuous remote management of devices, even when users are not logged in, and uses IPsec for secure communication with the option for multi-factor authentication. The downside is its limited compatibility, as it only works with Windows devices that support DirectAccess, and it requires IPv6 and additional network configurations.
  
    * **Implement VPN only**: Implementing VPN Only offers broad compatibility with various devices and operating systems, including Windows, macOS, Linux, Android, and iOS. It is generally simpler and more straightforward to configure compared to DirectAccess and allows for flexibility in choosing VPN protocols based on security and compatibility needs. However, users must manually initiate the VPN connection each time they need access to the corporate network, and administrators typically cannot manage remote devices when they are not connected to the VPN.
 
    In this case, I will select the option “**Implement VPN only**”.

9. Once this option is selected, a new window called “Routing and Remote Access” will open.
    
    <img width="744" height="530" alt="Image" src="https://github.com/user-attachments/assets/b72a1ed9-b0f3-438d-aa37-89139064c442" />

    In this case, you can see by the icon with the red arrow that the service is not currently configured and enabled. 

    To configure it, we right click on it and select the option “Configure and enable Remote Access”:

    <img width="746" height="528" alt="Image" src="https://github.com/user-attachments/assets/ba113cf5-bdf4-4911-91fe-096fde46b450" />

10. Once the wizard is opened, a configuration menu opens where I have to choose a combination of services to provide:

    - **Remote Access:** Configures the server to function as a Remote Access Service (RAS), allowing users to connect to the corporate network via dial-up lines or private networks.
  
    - **Network Address Translation (NAT):** Configures the server to act as a NAT device, enabling devices on a private network to access the Internet using a single public IP address. NAT hides internal IP addresses, enhancing security and conserving public IP addresses.
  
    - **Virtual Private Network (VPN) and NAT access:** Configures the server to provide both VPN and NAT services. This allows remote users to securely connect to the corporate network over the Internet (VPN) and also provides NAT services to manage network traffic between internal and external networks.
  
    - **Secure connection between two private networks:** Sets up a secure connection between two distinct networks, allowing them to communicate securely over the Internet. This is ideal for connecting different branches of a company, creating a private virtual network between them.
  
    - **Custom configuration:** Allows for detailed configuration of remote access services. You can manually select the specific services you want to configure, such as VPN, NAT, routing, dial-up remote access, etc. This option offers the most flexibility to tailor the setup to specific needs.

    In this case, I select the first option (Remote Access), since I am only interested in providing the VPN service:

    <img width="600" height="510" alt="Image" src="https://github.com/user-attachments/assets/ba04bc1f-e94b-4ae1-8c1a-7b5c0f9c48cd" />

    Then, I select the “VPN” option:

    <img width="598" height="504" alt="Image" src="https://github.com/user-attachments/assets/fcd3b612-a2a5-4d9b-a2a6-f8482b512780" />

    Now, I must select the interface that connects the server to the Internet. 

    In this case, it is the “Ethernet” interface, whose IP address is 192.168.1.6:

    <img width="600" height="512" alt="Image" src="https://github.com/user-attachments/assets/bb43cd11-ad6e-4445-80db-18bcad2fcab7" />

    Now, I must select whether I want the IP addresses of the computers connecting to the VPN to be assigned automatically (via DHCP) or whether I want an IP address to be chosen from a defined range.

    In this case, I will select the option “From a specified address range”:

    <img width="602" height="508" alt="Image" src="https://github.com/user-attachments/assets/3973d058-ee74-4e2a-a33f-db14a8abf8a0" />

    Next, I create the range of addresses that I will choose to be assigned to those computers that connect to VPN.

    As this is an example case, I will select an agreed range that will only cover 8 IP addresses, from 192.168.1.88 to 192.168.1.95:

    <img width="594" height="510" alt="Image" src="https://github.com/user-attachments/assets/6300817c-1677-467b-9581-d11536545554" />

    Finally, I check that everything is correct and press “Finish”:

    <img width="602" height="512" alt="Image" src="https://github.com/user-attachments/assets/579f8b4f-3d62-40e9-a511-369115da9d8b" />

    Once this is done, the VPN server will be running: 

    <img width="750" height="530" alt="Image" src="https://github.com/user-attachments/assets/91c366d1-cbc2-48dc-b164-02bec9d74c95" />

11. Now, to connect to a VPN from a client computer (in this case, JMFSOFT-PC04), I go to the search bar and type VPN.

    Then, I select the “VPN Configuration” option:

    <img width="942" height="766" alt="Image" src="https://github.com/user-attachments/assets/528075e7-9b3f-453d-a7b0-6d48018861c4" />

    Then, I select the “Add a VPN connection” option:

    <img width="962" height="762" alt="Image" src="https://github.com/user-attachments/assets/353b1194-7e29-40cd-b67f-4fd4213a35da" />

    Then, I fill in the following fields:

    <img width="962" height="762" alt="Image" src="https://github.com/user-attachments/assets/dc78bccf-2137-4642-9718-300621b58bc0" />
    <img width="962" height="380" alt="Image" src="https://github.com/user-attachments/assets/784bb16d-6c8c-4726-8a72-71da6ff22a05" />

    Once the fields have been filled in, the connection will be saved.

    All you have to do is click on connect:

    <img width="962" height="380" alt="Image" src="https://github.com/user-attachments/assets/784bb16d-6c8c-4726-8a72-71da6ff22a05" />
    <img width="962" height="380" alt="Image" src="https://github.com/user-attachments/assets/784bb16d-6c8c-4726-8a72-71da6ff22a05" />

    As shown in the image, the connection was successful.


12. Now, I can verify this as follows:

    From the Client side:

    * I go to CMD or Powershell and type ipconfig /all, and the connection should appear:

      <img width="653" height="604" alt="Image" src="https://github.com/user-attachments/assets/e18659b2-1fcc-4031-9a64-2e88cecb4987" />

    From the VPN server side:

    * I go to the “Routing and Remote Access” console, and then to the “Remote Access Clients” section.

      This section should list all the client computers connected to the VPN:

      This concludes the VPN server configuration.
  
-------------------------------------------------------------------------------------------------
## Monitoring and Visualization

In this section, I will install and integrate two programs, **Zabbix and Grafana**, to monitor client machines. This setup will enable real-time tracking and management of system performance and network activity. The primary reasons for including Zabbix, a robust monitoring tool, and Grafana, a powerful real-time data visualization program, in my homelab are:

- **Proactive Issue Detection**: Zabbix will help in identifying and addressing potential issues, such as hardware failures, software crashes, or network disruptions, before they impact the system's functionality. Grafana will provide a clear, visual representation of these issues, making them easier to understand and act upon.

- **Performance Optimization**: Continuous monitoring with Zabbix and real-time data analysis through Grafana will allow for fine-tuning system performance and ensuring efficient resource utilization.

- **Security Enhancement**: By using Zabbix to keep a close watch on system activities and Grafana to visualize security metrics, I can quickly detect any unauthorized access or unusual behavior, thereby strengthening the security of the environment.

- **Learning and Experimentation**: Implementing Zabbix and Grafana will provide hands-on experience in setting up, configuring, and utilizing monitoring and data visualization technologies, which are critical skills for IT management and cybersecurity.

<img width="200" height="200" alt="Image" src="https://github.com/user-attachments/assets/f06a5551-25f9-4ef2-ac1c-e175788213ca" /><img width="200" height="200" alt="Image" src="https://github.com/user-attachments/assets/a691c53d-62fb-436f-aefc-393b782986f1" />

To monitor the machines, I will use the Linux operating system, specifically the Debian 12.5 distro, on which I will install Zabbix and Grafana, tools for monitoring and data visualization respectively.

To start creating the Linux virtual machine, I first need to download the Debian distro, which can be downloaded from the official website: [debian.org](https://www.debian.org/index.es.html).

Once downloaded, I proceed to start creating the virtual machine:

First, I specify the name of the machine, which will be **MON01** as it is an abbreviation for 'Monitoring', clearly indicating that the machine is dedicated to monitoring.
As you can see in the image, the virtual machine was created correctly. Now, I proceed to start it to begin with the installation of the operating system.

<img width="360" height="324" alt="Image" src="https://github.com/user-attachments/assets/d6c1c304-b606-4c8f-b194-99f092266652" />

Once the virtual machine is started, the Debian 12.5 operating system will begin to be installed.
First, I must indicate what action I want to perform. 
In this case, I will perform a 'Graphical Install'.

<img width="640" height="478" alt="Image" src="https://github.com/user-attachments/assets/66f90e7b-c7cb-4816-94ee-6c613cf35cf0" />

Now, I must select the language.
In this case, I select "English", but I could have selected my native language (Spanish).

<img width="795" height="599" alt="Image" src="https://github.com/user-attachments/assets/3eecc7e1-2bea-4925-a2b1-3cae2dd702e6" />

The installer will then load the installation components from the downloaded .ISO file. <br/> After that, it will try to obtain an IP address via DHCP (Dynamic Host Configuration Protocol).

Once all this happens, I must define the hostname. 
In this case, I will choose to put "deb-mon01", which is a mix between the operating system (debian) and the main function of the machine (monitoring).

<img width="796" height="597" alt="Image" src="https://github.com/user-attachments/assets/76690671-4c10-460d-bd73-7289fdab67a5" />

Now, I must define the password of the root user, which is the most important user of the system. For this reason, this password cannot be forgotten.

<img width="796" height="595" alt="Image" src="https://github.com/user-attachments/assets/4487c22a-23a4-44b6-9cb7-a4ee4693195e" />

Now, I select a user name which will be created to perform the tasks that do not require elevated privileges. This is similar to creating a local user in Windows in addition to the Administrator user.
In this case, I will choose to create the user "juanma".

<img width="795" height="593" alt="Image" src="https://github.com/user-attachments/assets/81042a48-1360-4639-a77c-b5196233a56a" />

Now, it is time to partition the disks.
In this case, I chose a guided partition as I find no justification for partitioning manually on a homelab.
In a real environment, this should be configured in a more detailed way, creating a partition for the operating system and one for the swap.

<img width="798" height="592" alt="Image" src="https://github.com/user-attachments/assets/73d4a379-9dce-4f40-9302-0693216a5f7d" />

Finally, a summary of the automatic partitioning is shown.
Although the "swap" partition is created, which serves as an extension of the virtual memory when the physical RAM (Random Access Memory) is fully utilized, the amount allocated may not be the recommended amount, but for a test environment such as this homelab this is more than enough.

<img width="798" height="593" alt="Image" src="https://github.com/user-attachments/assets/82b95b43-af6d-4083-bffe-f19c14a5c00b" />

After unpacking and installing the base files, the system will query if a proxy is being used (in this case no) and which is the country from where I am using the operating system to recommend mirrors from where the desired packages will be installed.

Next, the system starts configuring **'apt' (Advanced Package Tool)**, which is a command line tool used to manage software packages. Its main function is to facilitate the installation, update and removal of applications and utilities in the operating system.

<img width="794" height="596" alt="Image" src="https://github.com/user-attachments/assets/5fb93d8e-c873-4b03-bd05-0448426c7f66" />

Then, it is asked about what software you want to install.
In this case, several desktop environments are shown (I will choose to use GNOME), the web server and the SSH Server (which serves to allow remote connections to the machine).
In this case, I won't install either (web or ssh).

<img width="796" height="595" alt="Image" src="https://github.com/user-attachments/assets/031ae30b-08d4-4fd4-8364-efe91330bfee" />

Finally, GRUB (the operating system boot loader) is installed on the primary partition and then the installation is completed.

<img width="791" height="593" alt="Image" src="https://github.com/user-attachments/assets/95b59ab7-a3b4-491a-933a-0343990152a3" />

Once the computer has restarted, I find the following screen in which I can log in to the system with the user (juanma) and the previously defined password:

<img width="1018" height="761" alt="Image" src="https://github.com/user-attachments/assets/84120722-b920-445e-961b-cbda4ed7d701" />

Once this is done (logged in), I am already using the Debian operating system.

<img width="1021" height="770" alt="Image" src="https://github.com/user-attachments/assets/325930ec-3996-41a7-ad79-4547c83a2bd5" />

Following this, I must install Zabbix and Grafana to begin monitoring the company's equipment.

### Zabbix Installation

To begin the installation of Zabbix, I go to their official website --> [https://www.zabbix.com/download](https://www.zabbix.com/download) and select the following options:

<img width="1234" height="588" alt="Image" src="https://github.com/user-attachments/assets/f091f443-baac-46b2-ac05-d6ed7550b210" />

**ZABBIX VERSION**: 6.4, the latest stable version <br/>
**OS DISTRIBUTION**: Debian <br/>
**OS VERSION**: 12 (Bookworm) <br/>
**ZABBIX COMPONENT**: Server, Frontend, Agent (since I want to install the Zabbix server on this machine to monitor other client devices) <br/>
**DATABASE**: MySQL (in this case, I choose MySQL because its configuration and management are simpler. I don't need to handle a large volume of data writes and I prioritize performance for read queries. However, in an enterprise environment, PostgreSQL might be more useful if complex transaction processing is needed) <br/>
**WEB SERVER**: Apache (I choose Apache because it has native compatibility with Zabbix, is easier to configure, and I don't require handling many concurrent connections. This doesn’t justify installing Nginx, which excels at efficient management of concurrent connections and scalability) <br/>

Once all these options are selected, I need to follow the installation manual on the website, which is tailored to the previously chosen options. <br/>
To run the following commands, you need to open a terminal, which allows you to interact with the operating system using text-based commands.

<img width="1185" height="249" alt="Image" src="https://github.com/user-attachments/assets/4d90399b-a8dc-4d15-a789-fcd9d64093c2" />

The first command (**wget**) downloads the Zabbix 6.4 .deb package, which is then installed with the next command (**dpkg -i**). <br/>
Finally, **apt update** is run, which updates the list of available packages and their versions from the configured repositories. This ensures that the system has the most up-to-date information about the packages available for installation or upgrade.

<img width="990" height="369" alt="Image" src="https://github.com/user-attachments/assets/20d8bfe9-4c06-458b-a02c-4900cb9b2a56" />

<img width="989" height="268" alt="Image" src="https://github.com/user-attachments/assets/f40ff6d5-2e45-4469-922f-9b43bc0ec99a" />

<img width="991" height="347" alt="Image" src="https://github.com/user-attachments/assets/1b729da1-e349-4a4c-b88d-4d9019ffb03f" />

Once these commands are executed, we proceed to install the Zabbix server, frontend, and agent:

<img width="1091" height="111" alt="Image" src="https://github.com/user-attachments/assets/ab412de2-439b-47a4-9312-6295025fff23" />

After executing the command, I will be prompted to confirm the installation of the listed packages. To confirm, type 'y' (for yes) and press enter. Alternatively, I could skip this prompt by adding the -y parameter to the original command.

<img width="987" height="553" alt="Image" src="https://github.com/user-attachments/assets/c2e757c8-5e94-44d8-a5fd-ad98484b310d" />

Once this is done, I proceed to create the initial database in MySQL:

<img width="1127" height="320" alt="Image" src="https://github.com/user-attachments/assets/956d5cab-9eca-4743-ada2-f632ca05623e" />

In this case, Debian 12.5 uses MariaDB (a MySQL fork compatible with most applications that use MySQL) instead of MySQL.
Because of this, I will use the equivalent commands for the MariaDB database.

<img width="910" height="450" alt="Image" src="https://github.com/user-attachments/assets/9e3e49c6-494f-46d0-973b-d6aad02a82c5" />

The commands executed in the image perform the following actions:

- **sudo mariadb -u root -p**: This command initiates an interactive session with the MariaDB database server as the root user. The -p parameter prompts for the password of the root user to authenticate.

- **create database zabbix character set utf8mb4 collate utf8mb4_bin;**: Creates a new database named zabbix on the MariaDB server. utf8mb4 specifies the UTF-8 character set for full Unicode support, capable of storing a wide range of characters. utf8mb4_bin specifies the binary collation setting, which is useful for binary string comparison without regard to specific regional settings.

- **create user 'zabbix'@'localhost' identified by 'Test.123';** Creates a new user in the MariaDB database named zabbix, restricted to connections from the same server (localhost). The password for this user is set to 'Test.123'.

- **grant all privileges on zabbix.\* to 'zabbix'@'localhost';**: Grants all privileges on the zabbix database to the zabbix user when connecting from localhost. This allows the zabbix user to perform all operations (create, modify, delete, select, etc.) on all tables and objects within the zabbix database.
  
- **set global log_bin_trust_function_creators = 1;**: Enables the safe execution of user-defined functions (UDF) in a binary log replication environment. This is necessary to allow Zabbix, which often uses user-defined functions, to function correctly in database replication environments.

<img width="1087" height="121" alt="Image" src="https://github.com/user-attachments/assets/0dc2ba00-d458-4d39-ba11-b06d80ea9ea6" />

<img width="909" height="143" alt="Image" src="https://github.com/user-attachments/assets/2fdec6ec-f97f-4f93-8b04-e21c406cb27b" />

The command "**zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | sudo mariadb -u zabbix -p zabbix --default-character-set=utf8mb4**" is used to import a gzip-compressed database file into MariaDB, specifically for the Zabbix monitoring system.

In this command, the file "/usr/share/zabbix-sql-scripts/mysql/server.sql.gz" is decompressed using zcat and then imported into MariaDB. The command is prefixed with "sudo" to execute it with superuser privileges, ensuring sufficient permissions to perform the import. "-u zabbix" specifies that the user "zabbix" will be used to connect to MariaDB, and "-p zabbix" indicates that the password for the user "zabbix" will be used. The option "--default-character-set=utf8mb4" ensures that UTF-8 character set encoding is used.

<img width="1101" height="191" alt="Image" src="https://github.com/user-attachments/assets/60973b64-03e6-420c-9106-48f104f5d0f0" />

<img width="909" height="346" alt="Image" src="https://github.com/user-attachments/assets/7897e08b-efb2-40fd-9ac0-9d01e59522df" />

Executing the command "**SET GLOBAL log_bin_trust_function_creators = 0;**" sets the parameter to 0. This parameter controls whether non-superuser accounts can create user-defined functions when binary replication (log_bin) is enabled. Setting it to 0 restricts this capability, ensuring tighter control over the functions created in the database.

After performing these steps, it is necessary to configure the database for the Zabbix server:

<img width="1102" height="154" alt="Image" src="https://github.com/user-attachments/assets/2d57dde2-b059-4673-9f8d-f0b65c8b5859" />

<img width="895" height="457" alt="Image" src="https://github.com/user-attachments/assets/446ad958-bf40-4987-97b6-7b745160a847" />

Now that everything is configured, I need to restart the 'zabbix-server', 'zabbix-agent', and 'apache2' services using **systemctl restart**. Additionally, I will enable them to start automatically at system boot using **systemctl enable**.

<img width="1104" height="165" alt="Image" src="https://github.com/user-attachments/assets/6c6a44b9-aff4-4472-bea9-33f98cdbb21b" />

<img width="911" height="305" alt="Image" src="https://github.com/user-attachments/assets/85dfa883-9d64-48cc-8132-55ddd6eb27d3" />

Once the services are restarted and enabled, I proceed to open the Zabbix web interface (Zabbix UI) by accessing the link http://[host]/zabbix.
In this case, the hostname is: deb-mon01, but if I don't know it, I can run the command 'hostname'.

<img width="724" height="81" alt="Image" src="https://github.com/user-attachments/assets/9208a52b-0190-4b42-9711-7125cad93af0" />

<img width="262" height="47" alt="Image" src="https://github.com/user-attachments/assets/fafec2ba-0d3c-4bae-a990-7be3620a31f8" />

<img width="1017" height="656" alt="Image" src="https://github.com/user-attachments/assets/fa093d97-3485-41a3-8c9d-8bab54ba361b" />

Once the language is selected, I click 'Next Step' and verify that all prerequisites are met (in this case, as shown in the image, all are satisfactorily met):

<img width="838" height="494" alt="Image" src="https://github.com/user-attachments/assets/caa0bd0d-c5d9-4147-b0ef-63b3b83bac4a" />

Now, I configure the database connection.
In this case, I just need to verify that the username is correct (zabbix), and I must fill in the password with the one used previously (Test.123):

<img width="828" height="502" alt="Image" src="https://github.com/user-attachments/assets/01735729-016a-4d1d-9855-cf0c1c08caf0" />

Now, I need to fill in the Zabbix web server name, set the time zone, and choose the web interface theme. In this case, I opted for the name "Zabbix Server - Buenos Aires Office" to identify the Zabbix server. It's a clear and descriptive name indicating the purpose and location of the server.

The time zone is (UTC-03:00) America/Argentina/Buenos Aires, and the chosen theme is 'Blue'.

<img width="833" height="494" alt="Image" src="https://github.com/user-attachments/assets/dca2ca91-3fb6-4c74-b432-aa34b4ed22f0" />

Finally, a summary of the selected configurations is displayed. I verify that everything is correct and then click 'Next Step'.

<img width="824" height="488" alt="Image" src="https://github.com/user-attachments/assets/5c2a7fc8-357b-493c-b1dc-df8dbee3927d" />

Then, a message appears indicating that the Zabbix server has been successfully configured and installed.

<img width="828" height="491" alt="Image" src="https://github.com/user-attachments/assets/5bf202ac-ecba-4012-8a08-19ae49e4c096" />

Once 'Finish' is pressed, I am redirected to a login where I must enter the following credentials:

**Username**: Admin <br/>
**Password**: zabbix

<img width="389" height="434" alt="Image" src="https://github.com/user-attachments/assets/298e6509-fbe2-4fd4-9ca5-2da264950336" />

Once this is done, I conclude with the configuration and installation of the Zabbix server. Now, the only thing left is to install the client on the machines I want to monitor or discover them automatically on the network.
<img width="1023" height="636" alt="Image" src="https://github.com/user-attachments/assets/d76ca9df-cdab-4564-9be3-4fd4626a5665" />

Once installed on Linux, I proceed to download the agent on Windows. <br/>
In this case, I will install and configure the agent manually on each machine, but I could do it using PDQ Deploy from SV02.

<img width="1234" height="626" alt="Image" src="https://github.com/user-attachments/assets/32b6a143-a3a6-46e8-8e17-9fe79ac23a88" />

Once downloaded and started, I must indicate the IP address of the Zabbix server (i.e. the Debian 12.5 machine where I installed the Zabbix server).
In this case, I can verify this information using the ip address show command (or ip a s for short).

<img width="735" height="410" alt="Image" src="https://github.com/user-attachments/assets/211a4740-fa22-4305-86a5-fc6101e8063b" />

<img width="510" height="427" alt="Image" src="https://github.com/user-attachments/assets/a65ca862-1b06-480f-9c0a-f47ce97ad518" />

Then, I press 'Next' and finish the agent installation. After that, I proceed to repeat this process on the other machines (DC01, JMFSOFT-PC01, JMFSOFT-PC02, JMFSOFT-PC03, JMFSOFT-PC04).

<img width="512" height="403" alt="Image" src="https://github.com/user-attachments/assets/161ecb42-553f-4f2c-ad21-c1417986e6d7" />

Before installing it on the other machines, I can verify that it has been installed and is working correctly by checking in the Windows Services:

Then, I proceed to add the host to which I have just installed the agent with its corresponding data (Name: SV02, IP: 192.168.1.6, etc.).

<img width="977" height="575" alt="Image" src="https://github.com/user-attachments/assets/616128bc-dc96-40bd-a0ff-6616e33c3b32" />

<img width="844" height="99" alt="Image" src="https://github.com/user-attachments/assets/4cf9e0c6-3d74-46bf-9354-29ee9fd17146" />

From now on, I can collect information from the SV02 server, and visualize it from MON01 (Linux Debian 12.5):

<img width="782" height="296" alt="Image" src="https://github.com/user-attachments/assets/c99ff706-11f9-4f2a-adf7-f51bfc1f1ac8" />

### Grafana Installation

To begin the installation of Grafana, I go to their official website --> [https://grafana.com/](https://grafana.com/).

In this case, I will choose to install from the apt package manager, for the sake of simplicity and complete integration with the operating system. Besides, it has the advantage of automatic updates when I run the "apt update" command.

Another option is to install by downloading and installing a ".deb" file, which has advantages such as access to the latest version and full control of the installation.

![Grafana 1](images/grf1.PNG)

To start with the installation, I perform the following steps:

![Grafana 2](images/grf2.PNG)

![Grafana 3](images/grf3.PNG)

The packages installed in the above image are required as they provide the following:

  - **apt-transport-https**: Enables support for HTTPS connections in apt.
  - **software-properties-common**: Provides additional tools to manage software repositories.
  - **wget**: Used to download files from the command line.

![Grafana 4](images/grf4.PNG)

![Grafana 5](images/grf5.PNG)

These commands are used to add the Grafana GPG (Gnu Privacy Guard) key to the system, allowing to verify the authenticity of the packages downloaded from the Grafana repository. 

  - **sudo mkdir -p /etc/apt/keyrings/**: mkdir -p creates the specified directory (/etc/apt/keyrings/). The -p option ensures that if the directory already exists, an error is not generated.
  
  - **wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null**: Downloads the Grafana GPG key and saves it in /etc/apt/keyrings/grafana.gpg

The last command (**ls -l /etc/apt/keyrings/**) was executed only to verify that the key was downloaded correctly.

![Grafana 6](images/grf6.PNG)

![Grafana 7](images/grf7.PNG)

The command
  - **echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list** 

is used to add a Grafana repository line to the apt source configuration file on Debian-based systems (such as Ubuntu).

![Grafana 8](images/grf8.PNG)

![Grafana 9](images/grf9.PNG)

Now, I ran the command   
  
  - **sudo apt-get update**  

to update the list of available packages.

When you run "sudo apt-get update", the system contacts each of the repositories listed in /etc/apt/sources.list and in the files inside the /etc/apt/sources.list.d/ directory (such as grafana.list that we configured earlier).

![Grafana 10](images/grf10.PNG)

![Grafana 11](images/grf11.PNG)

The 

  - **sudo apt-get install grafana** 
  
command is used to install Grafana on a Debian-based system, such as Ubuntu.

![Grafana 12](images/grf12.PNG)

Finally, I run the command

  - **apt list --installed | grep grafana**

to verify that grafana was installed correctly.

![Grafana 13](images/grf13.PNG)

Finally I run the commands  

  - **systemctl start grafana-server**
  - **systemctl enable grafana-server**

The systemctl start grafana-server command is used to manually start the Grafana service on the system. 

The systemctl enable grafana-server command is used to configure Grafana to start automatically at system startup.

### Zabbix and Grafana Integration

To access the Grafana web portal, I go to the address (localhost:3000) or simply use my hostname (deb-mon01:3000) or my ip address (192.168.0.7:3000):

![Grafana 14](images/grf14.PNG)

By default, I can login with the following credentials:

    - user: admin
    - password: admin

![Grafana 15](images/grf15.PNG)

Now, I must integrate Zabbix with Grafana. 

- **Zabbix** is a network and system monitoring platform that allows you to monitor and track the performance of IT infrastructures, networks, services and applications.

- **Grafana** is an open source data visualization and analysis platform designed to work with time series and metrics data.

Grafana can be integrated with Zabbix as a data source, allowing users to import data from Zabbix and create custom visualization dashboards that combine data from multiple sources.

To integrate Zabbix with Grafana, I perform the following steps:

1. First, I verify that all the devices are recognized in Zabbix. 
  To verify this, I go to deb-mon01/zabbix/, then inside the web interface I go to Data Collection and then Hosts.
  In this case, I have 2 virtual machines turned off (JMFSOFT-PC03 and JMFSOFT-PC04) for a lack of resources, but in a real environment they would all be on. <br/><br/>
  ![Grafana 16](images/grf16.PNG)

2. Then, once this is confirmed, I must log in to the Grafana web interface.
  To do this, I go to deb-mon01:3000: <br/><br/>
  ![Grafana 17](images/grf17.PNG)

3. Once this is done, in the left panel, I go to Administration --> Plugins: <br/><br/>
   ![Grafana 18](images/grf18.PNG) <br/><br/>
   Inside Plugins, I look for the "Zabbix" plugin and install it: <br/><br/>
   ![Grafana 19](images/grf19.PNG) <br/><br/>

4. Now, once the plugin is installed and enabled, I must restart the grafana-server service, with the following command:
    - **sudo systemctl restart grafana-server** <br/><br/> 
    ![Grafana20](images/grf20.PNG) <br/><br/> 

5. Now, I must link the Grafana service to Zabbix. <br/>
  Within the web interface, from the Connection console, I go to Data Sources and then select "Add Data Source".<br/> <br/> 
  Once there, I choose "Zabbix" and complete the following fields: <br/>

     - **Name**: Zabbix Server
     - **URL**: http://deb-mon01/zabbix/api_jsonrpc.php 
     - **Username**: Admin
     - **Password**: zabbix

    Although it is recommended to create a dedicated account especially for this instead of using the "Admin" account, for the sake of simplicity, I have decided to use the Admin account.

    ![Grafana21](images/grf21.PNG) <br/><br/> 
    ![Grafana22](images/grf22.PNG) <br/>

    Once this is saved, a green banner should be displayed indicating that you have successfully connected to the data source. <br/> If not, a red banner is displayed indicating an error.
    
    ![Grafana23](images/grf23.PNG) <br/>

6. Once the connection with the data source is made, I can proceed with the creation of Dashboards.
   
   To do this, fill in the "**Host**" field with the host name of the device to be monitored.
    For example: DC01.
    Then, I fill in the "**Item**" field with the item I want to monitor. For example, if I want to see the CPU utilization, I fill in "Windows: CPU Utilization":

    ![Grafana24](images/grf24.PNG) <br/>

    In this case, the usefulness of the graph cannot be fully appreciated since the server was inactive and therefore the CPU utilization is practically null, but in a real environment its use could be analyzed over time.

    I decided to use a "**Time Series**" type graph because it is the most appropriate for the following reason: 
    CPU utilization varies continuously as processes run on the server. A time series graph captures these changes in real time and allows you to see how the CPU load fluctuates over the course of the day, week, month or any other desired time period. In addition, patterns and trends can be analyzed.

    For demonstration purposes, I also decided to add the "**Disk Write Rate**" to the dashboard.
  
7. Finally, I created another panel and added it to the dashboard.
  This completes the installation of Zabbix, the installation of Grafana, and the subsequent integration of Zabbix with Grafana for visualization.

    ![Grafana25](images/grf25.PNG) <br/>

-------------------------------------------------------------------------------------------------
