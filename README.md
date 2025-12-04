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
     
