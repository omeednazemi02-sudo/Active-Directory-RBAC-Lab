# Active-Directory-RBAC-Lab

## Objective
The Active Directory + RBAC Lab established a virtualized enterprise identity management environment designed to simulate and enforce role-based access control across a Windows domain. Using Proxmox VE running on a Dell PowerEdge T340, multiple virtual machines were deployed to replicate a Security Operations Center (SOC) and IT infrastructure environment. The lab focused on deploying a Windows Server 2022 Domain Controller, configuring Active Directory Domain Services, building an organizational unit structure, and enforcing access restrictions through Group Policy Objects. The goal was to demonstrate how identity and access management controls are implemented and verified in a real enterprise environment.



### Skills Learned

- Deployment and configuration of Windows Server 2022 as an Active Directory Domain Controller]
- Installation and promotion of Active Directory Domain Services (AD DS) on a virtualized server environment
- Design and implementation of an Organizational Unit (OU) structure reflecting real enterprise department hierarchies
- Creation and management of domain user accounts and security groups using Active Directory Users and Computers
- Implementation of Role-Based Access Control (RBAC) by assigning users to security groups based on their organizational role
- Configuration of Group Policy Objects (GPOs) to enforce access restrictions on domain-joined endpoints
- Deployment of a Windows 11 Enterprise client VM and joining it to an Active Directory domain
- Verification of GPO enforcement by testing access restrictions on a domain-joined endpoint using a standard user account
- Understanding of the difference between Domain Admin, SysAdmin, and standard user privilege levels
- Practical experience configuring DNS for Active Directory name resolution across virtual machines


### Tools Used

- Proxmox VE 9.1 for hypervisor and VM management on a Dell PowerEdge T340
- Windows Server 2022 Standard Evaluation as the Domain Controller operating system
- Active Directory Domain Services (AD DS) for identity and access management
- Group Policy Management Console (GPMC) for creating and linking Group Policy Objects
- Active Directory Users and Computers (ADUC) for managing users, groups, and OUs
- Windows 11 Enterprise Evaluation as the domain-joined client endpoint
- PowerShell for DNS configuration, domain joining, and GPO verification
- 
## Steps

### Step 1 – AD DS Role Installation

The Active Directory Domain Services role was installed on the Windows Server 2022 virtual machine using the Add Roles and Features Wizard in Server Manager. The installation included Group Policy Management, Remote Server Administration Tools, and the Active Directory module for Windows PowerShell.

<img width="1000" height="800" alt="AD install" src="https://github.com/user-attachments/assets/08a661dc-fbd4-4e39-b514-5ed1f9f3c00f" />




---

### Step 2 – Domain Controller Promotion
After the AD DS role installation completed, the server was promoted to a Domain Controller using the Active Directory Domain Services Configuration Wizard. A new forest was created with the root domain name lab.local. DNS was configured automatically during promotion and the server was set as the authoritative DNS server for the domain.

<img width="1000" height="800" alt="AD Domain Service Install" src="https://github.com/user-attachments/assets/22959c10-cbf8-494a-8b61-20fe94c9698f" />


---

### Step 3 – Organizational Unit Structure

An Organizational Unit hierarchy was designed and built within Active Directory Users and Computers to reflect a realistic enterprise department structure. OUs were created for IT Department (with sub-OUs for Helpdesk and SysAdmins), HR Department, Finance Department, Disabled Accounts, and Workstations.


<img width="400" height="300" alt="AD Structure" src="https://github.com/user-attachments/assets/7a26d144-bd45-40d8-b830-3c3400dfd7e8" />



---

### Step 4 – User and Group Creation

Domain user accounts and security groups were created within their respective OUs to implement RBAC. Each user was assigned to a security group matching their department role. This ensures access rights are granted based on role membership rather than individual account configuration.


<img width="1000" height="800" alt="AD Users Added" src="https://github.com/user-attachments/assets/d78fcb48-d3ce-440e-8c86-2241cac5fa1e" />


---

### Step 5 – Group Policy Configuration

Group Policy Objects were created and linked to the appropriate OUs to enforce role-based access restrictions across the domain. The Helpdesk Restrictions GPO prevents Helpdesk users from accessing Control Panel and Command Prompt. The SysAdmin Policy grants SysAdmins the right to log on locally to servers.

<img width="1000" height="800" alt="Group Policy Application" src="https://github.com/user-attachments/assets/e9b9b046-a442-4c56-a057-a8dca6be7852" />


---

### Step 6 – Windows 11 Client VM Domain Join

A Windows 11 Enterprise virtual machine (Client01) was deployed in Proxmox and joined to the lab.local domain. DNS was configured on the client to point to the Domain Controller at 192.168.1.143 to enable successful domain discovery and authentication.


<img width="1000" height="800" alt="VM added to lab local domain" src="https://github.com/user-attachments/assets/70177c0b-c2eb-4d41-9a4d-5e0c7d55d707" />


---

### Step 7 – RBAC Verification

After joining the domain, the client was logged into using the jsmith (Helpdesk) domain account. Group Policy was verified to be applying correctly — the Command Prompt displayed the message "The command prompt has been disabled by your administrator" confirming the Helpdesk Restrictions GPO was enforced successfully on the domain-joined endpoint.

<img width="1000" height="800" alt="VM connected to domain and checked GP" src="https://github.com/user-attachments/assets/e64b646b-e16c-4301-90de-f70d2a4119fd" />



## Challenges & Lessons Learned



- Network adapter driver issues — The VirtIO network adapter required manual driver installation from the VirtIO ISO before Windows Server could establish network connectivity. This highlighted the importance of driver compatibility when deploying VMs in a non-VMware environment.
- DNS configuration is critical for AD — Domain joining failed repeatedly until DNS on the client was explicitly pointed to the Domain Controller. Active Directory is entirely dependent on correct DNS resolution — without it, lab.local is undiscoverable.
- IP addressing across network changes — Migrating the lab from a 10.0.0.x network to 192.168.1.x after installing a UniFi UCG Ultra required updating static IPs on both the Domain Controller and client VM. This reinforced the importance of DHCP reservations for lab infrastructure.
- Domain Controllers restrict interactive logon — Standard domain users cannot log on directly to a Domain Controller by design. RBAC verification must be performed on a domain-joined client endpoint, not the DC itself — which reflects real enterprise security practice.
- Key takeaway: Building an AD environment from scratch demonstrates the full IAM workflow — identity creation, role assignment, policy enforcement, and verification — reinforcing why each layer of access control matters in a real enterprise.
