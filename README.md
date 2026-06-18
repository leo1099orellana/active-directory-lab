# Active Directory Home Lab

A hands-on home lab where I deployed and configured a **Windows Server 2022 Active Directory** environment from scratch, practicing the day-to-day tasks of an **IT Support / Help Desk** technician.

---

## Objective

Build a functional Active Directory domain to practice the core skills used daily in a Help Desk / Service Desk role: domain controller deployment, organizational structure, user and group management, and account administration.

---

## Lab Environment

| Component | Detail |
|-----------|--------|
| Hypervisor | Oracle VirtualBox |
| Server OS | Windows Server 2022 (Desktop Experience) |
| Role | Domain Controller |
| Domain | `lab.local` |
| Network | Internal Network (isolated lab) |

---

## What I Implemented

1. Installed **Windows Server 2022** on a virtual machine.
2. Installed the **Active Directory Domain Services (AD DS)** role.
3. Promoted the server to a **Domain Controller**, creating the forest `lab.local`.
4. Created **Organizational Units (OUs)** to structure the directory.
5. Created and managed **domain user accounts**.
6. Created **security groups** and assigned members.
7. Performed common **Help Desk tasks**: password resets and account enable/disable.

### PowerShell used for promotion

```powershell
# Install the AD DS role
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Promote the server to a Domain Controller (new forest)
Install-ADDSForest -DomainName "lab.local"
```

---

## Skills Demonstrated

- Windows Server installation and configuration
- Active Directory Domain Services (AD DS)
- Domain Controller promotion (GUI and PowerShell)
- User account creation and management
- Security group management
- Password resets and account lifecycle (enable / disable)
- Organizational Unit (OU) design
- Virtualization with VirtualBox

---

## Walkthrough

### 1. Active Directory domain `lab.local`
Active Directory Users and Computers showing the `lab.local` domain, the custom Organizational Unit **Empleados**, and the user and group objects.

![Domain, OU and users](ad-lab-overview.png)

### 2. User account management
Properties of a domain user account (General tab) — viewing and editing account attributes.

![User properties](user-properties.png)

### 3. AD DS role and services
Server Manager showing the Active Directory Domain Services role with the core services running (NTDS, Netlogon, ADWS, DNS).

![AD DS role](ad-ds-role.png)

### 4. Security group management
The **Ventas** security group with assigned members — controlling access by group.

![Group management](ventas.png)

### 5. Password reset
Resetting a user's password — one of the most common Help Desk tasks.

![Password reset](password.png)

### 6. Account administration (disable)
Disabling a user account — typical for employee offboarding or temporary suspension.

![Account disabled](userdeshabilitado.png)

---

## Next Steps (in progress)

- [ ] Configure a static IP for the Domain Controller
- [ ] Join a Windows 10/11 client to the domain
- [ ] Apply Group Policy Objects (GPOs)

---

## Notes

This lab was built for educational and portfolio purposes in an isolated virtual environment. No production or sensitive data is involved.

---

**Author:** Leonel Orellana — IT Support · Networking · Cybersecurity
[LinkedIn](https://www.linkedin.com/in/leonel-orellana)
