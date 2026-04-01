# Active Directory Administration Lab

This project demonstrates a fully configured **Active Directory Administration Lab** built on Windows Server 2019. The lab simulates a real enterprise IT environment, covering user and group management, Group Policy enforcement, DHCP/DNS configuration, and common help desk scenarios.

---

## Objective

Design and operate a Windows Server 2019 Active Directory environment to simulate real-world help desk and sysadmin tasks including user account management, Group Policy configuration, DHCP/DNS setup, and troubleshooting — aligned with enterprise IT workflows.

---

## Tools & Technologies

- Windows Server 2019
- Active Directory Domain Services (AD DS)
- Group Policy Management Console (GPMC)
- DHCP Server
- DNS Server
- Oracle VirtualBox
- Command Prompt / PowerShell

---

## Lab Environment

| Component | Details |
|---|---|
| Domain Name | corpnet.local |
| Domain Controller | WIN-FUTQ92VSQNF |
| IP Range (DHCP) | 192.168.1.100 – 192.168.1.200 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |

---

## Phase 1 — Active Directory Setup

### Installed Roles
Installed the following roles via Server Manager:
- **Active Directory Domain Services (AD DS)**
- **DHCP Server**
- **Group Policy Management**

![Role Installation](screenshots/01-roles-installation-success.png)

### Promoted to Domain Controller
Promoted the server to a Domain Controller by creating a new forest with the domain name `corpnet.local`. This made the server the central authentication authority for the network.

![Prerequisites Passed](screenshots/02-prerequisites-check-passed.png)

---

## Phase 2 — Organizational Structure

### Organizational Units
Created three OUs to simulate a departmental structure:
- **IT**
- **HR**
- **Finance**

### Users & Security Groups
Created 2 users and 1 security group per department — 6 users and 3 groups total. Users were assigned to their respective department security groups to implement role-based access control.

| Department | Users | Security Group |
|---|---|---|
| IT | John Smith, Sarah Connor | IT-Staff |
| HR | Lisa Johnson, Mark Davis | HR-Staff |
| Finance | Tom Wilson, Amy Chen | Finance-Staff |

![AD Structure IT](screenshots/05-it-ou.png)
![AD Structure HR](screenshots/06-hr-ou.png)
![AD Structure Finance](screenshots/07-finance-ou.png)

---

## Phase 3 — Group Policy Configuration

### Password Policy GPO
Applied domain-wide to enforce secure credential standards.

| Setting | Value |
|---|---|
| Minimum password length | 10 characters |
| Maximum password age | 90 days |
| Minimum password age | 1 day |
| Enforce password history | 5 passwords |
| Complexity requirements | Enabled |

![Password Policy](screenshots/08-password-policy-gpo.png)

### Account Lockout Policy GPO
Protects against brute force attacks by locking accounts after repeated failed login attempts.

| Setting | Value |
|---|---|
| Lockout threshold | 5 invalid attempts |
| Lockout duration | 30 minutes |
| Reset counter after | 30 minutes |

![Account Lockout Policy](screenshots/09-account-lockout-policy-gpo.png)

### Workstation Security Policy GPO
Restricts users from modifying system settings or bypassing security controls.

| Setting | State |
|---|---|
| Prohibit access to Control Panel | Enabled |
| Prevent access to Command Prompt | Enabled |

![Workstation Security Policy](screenshots/17-workstation-security-gpo.png)

---

## Phase 4 — DHCP Configuration

Configured a DHCP scope to automatically assign IP addresses to devices on the network.

| Setting | Value |
|---|---|
| Scope Name | CorpNet-Scope |
| IP Range | 192.168.1.100 – 192.168.1.200 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 |
| Exclusion | 192.168.1.100 (reserved for DC) |
| Lease Duration | 8 days |

![DHCP Scope](screenshots/10-dhcp-scope-ip-range.png)
![DHCP Active](screenshots/11-dhcp-scope-active.png)

---

## Phase 5 — DNS Verification

Verified DNS resolution using `nslookup` to confirm the domain controller is correctly resolving `corpnet.local`.

![DNS Verification](screenshots/12-dns-verification-nslookup.png)

---

## Phase 6 — Help Desk Scenarios

### Ticket 1 — Password Reset
**Issue:** User forgot their password and could not log in.
**Resolution:** Located the user account in Active Directory Users and Computers, performed a password reset, and enforced "User must change password at next logon" to maintain security compliance.

![Password Reset](screenshots/14-password-reset.png)

### Ticket 2 — Account Unlock
**Issue:** User account locked out after exceeding failed login attempt threshold.
**Resolution:** Located the locked account in Active Directory, confirmed lockout status under the Account tab, and unlocked the account to restore access.

![Account Unlock](screenshots/15-account-unlock.png)

### Ticket 3 — Account Disable (Employee Termination)
**Issue:** Employee termination requiring immediate revocation of domain access.
**Resolution:** Located the user account in Active Directory and disabled it to immediately revoke access while preserving the account for audit purposes.

![Account Disabled](screenshots/16-account-disabled.png)

### Ticket 4 — DNS/DHCP Troubleshooting
**Issue:** User reported inability to reach network resources.
**Resolution:** Ran the following commands to diagnose and resolve the connectivity issue:
```bash
ipconfig /release     # Released current IP lease
ipconfig /renew       # Requested new IP from DHCP server
ipconfig /flushdns    # Cleared stale DNS cache entries
nslookup corpnet.local  # Verified DNS resolution
```

![Troubleshooting](screenshots/18-dns-dhcp-troubleshooting.png)
