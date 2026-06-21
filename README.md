# Active Directory Home Lab

A hands-on home lab where I deployed and configured a Windows Server 2022
Active Directory environment from scratch, practicing the day-to-day tasks
of an IT Support / Help Desk technician, including real connectivity and
DNS troubleshooting while joining a client to the domain.

## Objective

Build a functional Active Directory domain to practice the core skills used
daily in a Help Desk / Service Desk role: domain controller deployment,
organizational structure, user and group management, account administration,
and client domain join with network troubleshooting.

## Lab Environment

| Component  | Detail                              |
|------------|-------------------------------------|
| Hypervisor | Oracle VirtualBox                   |
| Server OS  | Windows Server 2022 (Desktop Experience) |
| Client OS  | Windows 10 Pro                      |
| Role       | Domain Controller + DNS             |
| Domain     | lab.local                           |
| Network    | Internal Network (isolated lab)     |
| Addressing | DC 192.168.10.10 / Client 192.168.10.20 |

## What I Implemented

1. Installed Windows Server 2022 on a virtual machine.
2. Installed the Active Directory Domain Services (AD DS) role.
3. Promoted the server to a Domain Controller, creating the forest lab.local.
4. Created Organizational Units (OUs) to structure the directory.
5. Created and managed domain user accounts.
6. Created security groups and assigned members.
7. Performed common Help Desk tasks: password resets and account enable/disable.
8. Configured static IP addressing and DNS for the domain.
9. Joined a Windows 10 client to the domain and validated authentication.

## PowerShell used for promotion

```powershell
# Install the AD DS role
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Promote the server to a Domain Controller (new forest)
Install-ADDSForest -DomainName "lab.local"
```

## Skills Demonstrated

- Windows Server installation and configuration
- Active Directory Domain Services (AD DS)
- Domain Controller promotion (GUI and PowerShell)
- User account creation and management
- Security group management
- Password resets and account lifecycle (enable / disable)
- Organizational Unit (OU) design
- Static IP and DNS configuration
- DNS troubleshooting and name resolution (nslookup, Resolve-DnsName)
- Network connectivity diagnostics (ping, ICMP / firewall)
- Joining a Windows client to the domain (GUI and Add-Computer)
- Virtualization with VirtualBox

## Walkthrough

### 1. Active Directory domain lab.local
Active Directory Users and Computers showing the lab.local domain, the custom
Organizational Unit Empleados, and the user and group objects.

![Active Directory domain lab.local](ad-lab-overview.png)

### 2. User account management
Properties of a domain user account (General tab) — viewing and editing
account attributes.

![User account management](user-properties.png)

### 3. AD DS role and services
Server Manager showing the Active Directory Domain Services role with the core
services running (NTDS, Netlogon, ADWS, DNS).

![AD DS role and services](ad-ds-role.png)

### 4. Security group management
The Ventas security group with assigned members — controlling access by group.

![Security group management](ventas.png)

### 5. Password reset
Resetting a user's password — one of the most common Help Desk tasks.

![Password reset](password.png)

### 6. Account administration (disable)
Disabling a user account — typical for employee offboarding or temporary
suspension.

![Account administration (disable)](userdeshabilitado.png)

## Troubleshooting

Real connectivity and DNS issues encountered while joining a Windows 10
client to the domain, with how each was diagnosed and resolved — the same
diagnostic approach used in day-to-day IT support.

### Case 1 — Domain Controller stuck on an APIPA address
- **Symptom:** The DC's adapter showed an automatic `169.254.x.x` (APIPA)
  address instead of a usable IP.
- **Diagnosis:** `ipconfig /all` showed DHCP enabled but no address served.
  The isolated internal network has no DHCP server, so Windows self-assigned
  an APIPA address, which cannot communicate with other hosts.
- **Resolution:** Assigned a static IP (`192.168.10.10 / 255.255.255.0`),
  no gateway (isolated lab), DNS pointing to itself (`127.0.0.1`).
  Verified with `ipconfig`.

![DC with APIPA address before the fix](apipa.png)
![Assigning a static IP to the Domain Controller](ipestatica.png)
![DC ipconfig before and after the fix](afterip.png)

### Case 2 — Ping to the DC failing / intermittent packet loss
- **Symptom:** From the client, `ping 192.168.10.10` returned timeouts and
  ~50% packet loss, and `nslookup` could not resolve the domain.
- **Diagnosis:** Confirmed both VMs were on the same internal network.
  Partial replies ruled out network isolation and pointed to ICMP being
  filtered — Windows Server blocks ICMP echo requests by default.
- **Resolution:** Allowed ICMP on the DC for testing; ping then returned
  100% success. Noted that ICMP is not required for domain join — DNS and
  LDAP/SMB are.

![Client side: nslookup timeout and 50% packet loss on ping](client-connectivity-fail.png)
![Client configured with static IP and DNS pointing to the DC](cliente01.png)

### Case 3 — Client could not resolve the domain (nslookup timeout)
- **Symptom:** `nslookup lab.local` returned `DNS request timed out` and
  `Server: UnKnown`, even when run locally on the DC — suggesting DNS was down.
- **Diagnosis:** Verified the DNS service was running and the `lab.local`
  zone existed (Primary, AD-integrated). Querying with
  `Resolve-DnsName lab.local -Server 127.0.0.1` returned the correct record
  (`192.168.10.10`), proving forward resolution actually worked. The
  `nslookup` timeout came from its initial reverse (PTR) lookup of the DNS
  server, for which no reverse lookup zone exists in this lab.
- **Resolution / lesson:** The DNS service was healthy; the misleading
  symptom was a tool artifact. Confirmed resolution with `Resolve-DnsName`
  instead of trusting nslookup's leading timeout. Key takeaway: verify a
  fault with more than one tool before assuming a service is down.

![DNS service restart and forward resolution working despite the nslookup timeout](rebootservice.png)

### Outcome
With name resolution confirmed, the Windows 10 client was joined to
`lab.local`. Verified with `whoami` (returns a `lab\<user>` domain account)
and `(Get-WmiObject Win32_ComputerSystem).Domain` (returns `lab.local`),
and confirmed the computer object appeared in the domain's Computers container.

![Windows 10 client successfully joined to lab.local](client-domain-join.png)

## Next Steps (in progress)

- Apply Group Policy Objects (GPOs)

## Notes

This lab was built for educational and portfolio purposes in an isolated
virtual environment. No production or sensitive data is involved.

---

**Author:** Leonel Orellana — IT Support · Networking · Cybersecurity · [LinkedIn](https://www.linkedin.com/in/leonel-orellana)
