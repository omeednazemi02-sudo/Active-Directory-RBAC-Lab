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




---

### Step 2 – Wazuh Dashboard Access

After installation, the Wazuh dashboard was accessed through the browser interface. This dashboard provides visibility into alerts, system health, and threat detection across monitored endpoints.

<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/769df6e8-9f27-437f-845c-b89f695e9609" />



---

### Step 3 – SSH Service Configuration

SSH was enabled on the monitored Ubuntu endpoint to allow remote login attempts. This service generates authentication logs that can be monitored by the SIEM platform.

<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/d521457c-4b53-4b13-8ff2-534fe95b1ce2" />



---

### Step 4 – Attack Simulation

A third Ubuntu virtual machine was created to simulate an attacker system. Multiple SSH login attempts were performed using incorrect credentials to simulate a brute-force attack.

<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/52ac9c45-7fb9-46bc-a9a3-088a9690569f" />



---

### Step 5 – Threat Detection

The Wazuh SIEM server detected repeated authentication failures and generated alerts indicating attempts to log in using a non-existent user.

<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/3efdae13-2b99-4e75-b2fc-cbe22d03f87e" />



---

### Step 6 – Log Investigation

The Wazuh event details were analyzed to investigate the attack. By examining the log data, the source IP address of the attacker machine was identified.

<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/6febb340-5dd3-4108-b123-848f70d69cac" />



---

### Step 7 – Incident Response

After identifying the malicious source IP address, a firewall rule was implemented on the monitored endpoint using iptables to block the attacker.

<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/d391dcdf-6ea0-4517-b5ee-6ea6e2816cec" />


---

### Step 8 – Attack Mitigation Verification

After applying the firewall rule, the attacker machine was no longer able to establish an SSH connection to the monitored endpoint, confirming the successful mitigation of the attack.

<img width="1000" height="500" alt="image" src="https://github.com/user-attachments/assets/010eeb0d-1d81-41d4-b178-6ce65a887c31" />


## Challenges & Lessons Learned

- **Wazuh agent connectivity** required careful attention to the manager IP configuration in `ossec.conf` on the victim VM — an incorrect IP caused the agent to appear offline in the dashboard.
- **Alert tuning** — Wazuh's default rules triggered alerts at relatively low thresholds, which in a real environment could generate noise. This highlighted the importance of tuning detection rules to reduce false positives.
- **iptables persistence** — the firewall rule applied during incident response does not survive a reboot by default. In production, rules would need to be saved using `iptables-save` or managed through a persistent firewall tool like `ufw`.
- **Key takeaway:** Even a simple brute-force scenario demonstrates the full SOC workflow — log ingestion, alert triage, investigation, and containment — reinforcing why each step matters in a real incident.
