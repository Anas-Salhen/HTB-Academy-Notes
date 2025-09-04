# User and Machine Accounts
## Local Accounts
Accounts that are stored locally on a particular server or workstation. These accounts can be assigned rights on that host either individually or via group membership. Any rights assigned can only be granted to that specific host and will not work across the domain. There are several default local user accounts that are created on a Windows system:
- `Administrator`: this account has the SID `S-1-5-<domain>-500` and is the first account created with a new Windows installation. It cannot be deleted or locked, but it can be disabled or renamed. Windows 10 and Server 2016 hosts disable the built-in administrator account by default and create another local account in the local administrators group during setup.
    
- `Guest`: disabled by default. The purpose of this account is to allow users without an account on the computer to log in temporarily with limited access rights. By default, it has a blank password and is generally recommended to be left disabled because of the security risk of allowing anonymous access to a host.
    
- `SYSTEM`: The SYSTEM (or `NT AUTHORITY\SYSTEM`) account on a Windows host is the default account installed and used by the operating system to perform many of its internal functions. Unlike the Root account on Linux, `SYSTEM` is a service account and does not run entirely in the same context as a regular user. Many of the processes and services running on a host are run under the SYSTEM context. One thing to note with this account is that a profile for it does not exist, but it will have permissions over almost everything on the host. It does not appear in User Manager and cannot be added to any groups. A `SYSTEM` account is the highest permission level one can achieve on a Windows host and, by default, is granted Full Control permissions to all files on a Windows system.
    
- `Network Service`: This is a predefined local account used by the Service Control Manager (SCM) for running Windows services. When a service runs in the context of this particular account, it will present credentials to remote services.
    
- `Local Service`: This is another predefined local account used by the Service Control Manager (SCM) for running Windows services. It is configured with minimal privileges on the computer and presents anonymous credentials to the network.

It is important to note that a machine account (`NT AUTHORITY\SYSTEM` level access) in an AD environment will have most of the same rights as a standard domain user account. This can be useful if you want to gather info in a domain but you have access to a SYSTEM account not a standard user.

## Domain Users
Domain user accounts can log in to any host in the domain, unlike local users and their rights are given to them by the domain. 

One account to keep in mind is the `KRBTGT` account. This is a type of local account built into the AD infrastructure and acts as a service account for the Key Distribution service providing authentication and access for domain resources. This account is a common target of many attackers since gaining control or access will enable an attacker to have unconstrained access to the domain (see [Golden Ticket](https://attack.mitre.org/techniques/T1558/001/) attack).

## User Naming Attributes
| `UserPrincipalName` (UPN) | This is the primary logon name for the user. By convention, the UPN uses the email address of the user.                                                                                                                                                                          |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ObjectGUID`              | This is a unique identifier of the user. In AD, the ObjectGUID attribute name never changes and remains unique even if the user is removed.                                                                                                                                      |
| `SAMAccountName`          | This is a logon name that supports the previous version of Windows clients and servers.                                                                                                                                                                                          |
| `objectSID`               | The user's Security Identifier (SID). This attribute identifies a user and its group memberships during security interactions with the server.                                                                                                                                   |
| `sIDHistory`              | This contains previous SIDs for the user object if moved from another domain and is typically seen in migration scenarios from domain to domain. After a migration occurs, the last SID will be added to the `sIDHistory` property, and the new SID will become its `objectSID`. |

## Domain-joined vs. Non-Domain-joined Machines
### Domain Joined
Hosts joined to a domain have greater ease of information sharing within the enterprise and a central management point (the DC). The benefit here is that a user in the domain can log in and access resources from any host joined to the domain, not just the one they work on.

### Non-domain Joined
Not managed by domain policy. With that in mind, sharing resources outside your local network is much more complicated than it would be on a domain making this setup useful for homes or small businesses not large enterprises.

# Active Directory Groups
Groups in Active Directory have two fundamental characteristics: type and scope. The group type defines the group's purpose, while the group scope shows how the group can be used within the domain or forest.

## Group Type
Can be either security or distribution. The security groups type is primarily for ease of assigning permissions and rights to a collection of users instead of one at a time. All users added to a security group will inherit any permissions assigned to the group, making it easier to move users in and out of groups while leaving the group's permissions unchanged.

Distribution groups are used by email applications such as Microsoft Exchange to distribute messages to group members. They function much like mailing lists and allow for auto-adding emails in the "To" field when creating an email in Microsoft Outlook. This type of group cannot be used to assign permissions.

## Group Scope
Group scopes are: Domain Local Group, Global Group and Universal Group.

### Domain Local Group
Domain local groups can only be used to manage permissions to domain resources in the domain where it was created. DLGs cannot be used in other domains but CAN contain users from other domains. DLGs can be nested into (contained within) other DLGs but NOT within global groups.

### Global Group
Global groups can be used to grant access to resources in another domain. A global group can only contain accounts from the domain where it was created. Global groups can be added to both other global groups and local groups.

### Universal Group
The universal group scope can be used to manage resources distributed across multiple domains and can be given permissions to any object within the same forest. They are available to all domains within an organization and can contain users from any domain. Unlike domain local and global groups, universal groups are stored in the Global Catalog (GC), and adding or removing objects from a universal group triggers forest-wide replication.

It is recommended that administrators maintain other groups (such as global groups) as members of universal groups because global group membership within universal groups is less likely to change than individual user membership in global groups. Replication is only triggered at the individual domain level when a user is removed from a global group. If individual users and computers (instead of global groups) are maintained within universal groups, it will trigger forest-wide replication each time a change is made.

### AD Group Scope Examples
```powershell-session
PS C:\htb> Get-ADGroup  -Filter * |select samaccountname,groupscope

samaccountname                           groupscope
--------------                           ----------
Administrators                          DomainLocal
Users                                   DomainLocal
Guests                                  DomainLocal
Print Operators                         DomainLocal
Backup Operators                        DomainLocal
Replicator                              DomainLocal
Remote Desktop Users                    DomainLocal
Network Configuration Operators         DomainLocal
Distributed COM Users                   DomainLocal
IIS_IUSRS                               DomainLocal
Cryptographic Operators                 DomainLocal
Event Log Readers                       DomainLocal
Certificate Service DCOM Access         DomainLocal
RDS Remote Access Servers               DomainLocal
RDS Endpoint Servers                    DomainLocal
RDS Management Servers                  DomainLocal
Hyper-V Administrators                  DomainLocal
Access Control Assistance Operators     DomainLocal
Remote Management Users                 DomainLocal
Storage Replica Administrators          DomainLocal
Domain Computers                             Global
Domain Controllers                           Global
Schema Admins                             Universal
Enterprise Admins                         Universal
Cert Publishers                         DomainLocal
Domain Admins                                Global
Domain Users                                 Global
Domain Guests                                Global

<SNIP>
```
Group scopes can be changed, but there are a few caveats:
- A Global Group can only be converted to a Universal Group if it is NOT part of another Global Group.
- A Domain Local Group can only be converted to a Universal Group if the Domain Local Group does NOT contain any other Domain Local Groups as members.
- A Universal Group can be converted to a Domain Local Group without any restrictions.
- A Universal Group can only be converted to a Global Group if it does NOT contain any other Universal Groups as members.

## Built-in vs. Custom Groups
Several built-in security groups are created with a Domain Local Group scope when a domain is created. It is important to note that only user accounts can be added to these built-in groups as they do not allow for group nesting (groups within groups). 

Some examples of built-in groups included `Domain Admins`, which is a `Global` security group and can only contain accounts from its own domain. If an organization wants to allow an account from domain B to perform administrative functions on a domain controller in domain A, the account would have to be added to the built-in Administrators group, which is a `Domain Local` group. 

Though Active Directory comes prepopulated with many groups, it is common for most organizations to create additional groups (both security and distribution) for their own purposes. Changes/additions to an AD environment can also trigger the creation of additional groups. For example, when Microsoft Exchange is added to a domain, it adds various different security groups to the domain, some of which are highly privileged and, if not managed properly, can be used to gain privileged access within the domain.

## Nested Group Membership
Through this membership, a user may inherit privileges not assigned directly to their account or even the group they are directly a member of, but rather the group that their group is a member of. Tools such as [BloodHound](https://github.com/BloodHoundAD/BloodHound) are particularly useful in uncovering privileges that a user may inherit through one or more nestings of groups.

## Important Group Attributes
- `cn`: The `cn` or Common-Name is the name of the group in Active Directory Domain Services.
- `member`: Which user, group, and contact objects are members of the group.
- `groupType`: An integer that specifies the group type and scope.
- `memberOf`: A listing of any groups that contain the group as a member (nested group membership).
- `objectSid`: This is the security identifier or SID of the group, which is the unique value used to identify the group as a security principal.

# Active Directory Rights and Privileges
Access rights and privileges are two important topics in AD (and infosec in general), and we must understand the difference. `Rights` are typically assigned to users or groups and deal with permissions to `access` an object such as a file, while `privileges` grant a user permission to `perform an action` such as run a program, shut down a system, reset passwords, etc.

## Built-in AD Groups
Below are some of the most common built-in groups:

| Group Name                           | Description                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account Operators`                  | Members can create and modify most types of accounts, including those of users, local groups, and global groups, and members can log in locally to domain controllers. They cannot manage the Administrator account, administrative user accounts, or members of the Administrators, Server Operators, Account Operators, Backup Operators, or Print Operators groups.            |
| `Administrators`                     | Members have full and unrestricted access to a computer or an entire domain if they are in this group on a Domain Controller.                                                                                                                                                                                                                                                     |
| `Backup Operators`                   | Members can back up and restore all files on a computer, regardless of the permissions set on the files. Backup Operators can also log on to and shut down the computer. Members can log onto DCs locally and should be considered Domain Admins. They can make shadow copies of the SAM/NTDS database, which, if taken, can be used to extract credentials and other juicy info. |
| `DnsAdmins`                          | Members have access to network DNS information. The group will only be created if the DNS server role is or was at one time installed on a domain controller in the domain.                                                                                                                                                                                                       |
| `Domain Admins`                      | Members have full access to administer the domain and are members of the local administrators group on all domain-joined machines.                                                                                                                                                                                                                                                |
| `Domain Computers`                   | Any computers created in the domain (aside from domain controllers) are added to this group.                                                                                                                                                                                                                                                                                      |
| `Domain Controllers`                 | Contains all DCs within a domain. New DCs are added to this group automatically.                                                                                                                                                                                                                                                                                                  |
| `Domain Guests`                      | This group includes the domain's built-in Guest account. Members of this group have a domain profile created when signing onto a domain-joined computer as a local guest.                                                                                                                                                                                                         |
| `Domain Users`                       | This group contains all user accounts in a domain. A new user account created in the domain is automatically added to this group.                                                                                                                                                                                                                                                 |
| `Enterprise Admins`                  | Membership in this group provides complete configuration access within the domain. The group only exists in the root domain of an AD forest. Members in this group are granted the ability to make forest-wide changes such as adding a child domain or creating a trust. The Administrator account for the forest root domain is the only member of this group by default.       |
| `Event Log Readers`                  | Members can read event logs on local computers. The group is only created when a host is promoted to a domain controller.                                                                                                                                                                                                                                                         |
| `Group Policy Creator Owners`        | Members create, edit, or delete Group Policy Objects in the domain.                                                                                                                                                                                                                                                                                                               |
| `Hyper-V Administrators`             | Members have complete and unrestricted access to all the features in Hyper-V. If there are virtual DCs in the domain, any virtualization admins, such as members of Hyper-V Administrators, should be considered Domain Admins.                                                                                                                                                   |
| `IIS_IUSRS`                          | This is a built-in group used by Internet Information Services (IIS), beginning with IIS 7.0.                                                                                                                                                                                                                                                                                     |
| `Pre–Windows 2000 Compatible Access` | This group exists for backward compatibility for computers running Windows NT 4.0 and earlier. Membership in this group is often a leftover legacy configuration. It can lead to flaws where anyone on the network can read information from AD without requiring a valid AD username and password.                                                                               |
| `Print Operators`                    | Members can manage, create, share, and delete printers that are connected to domain controllers in the domain along with any printer objects in AD. Members are allowed to log on to DCs locally and may be used to load a malicious printer driver and escalate privileges within the domain.                                                                                    |
| `Protected Users`                    | Members of this [group](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#protected-users) are provided additional protections against credential theft and tactics such as Kerberos abuse.                                                                                                                   |
| `Read-only Domain Controllers`       | Contains all Read-only domain controllers in the domain.                                                                                                                                                                                                                                                                                                                          |
| `Remote Desktop Users`               | This group is used to grant users and groups permission to connect to a host via Remote Desktop (RDP). This group cannot be renamed, deleted, or moved.                                                                                                                                                                                                                           |
| `Remote Management Users`            | This group can be used to grant users remote access to computers via [Windows Remote Management (WinRM)](https://docs.microsoft.com/en-us/windows/win32/winrm/portal)                                                                                                                                                                                                             |
| `Schema Admins`                      | Members can modify the Active Directory schema, which is the way all objects with AD are defined. This group only exists in the root domain of an AD forest. The Administrator account for the forest root domain is the only member of this group by default.                                                                                                                    |
| `Server Operators`                   | This group only exists on domain controllers. Members can modify services, access SMB shares, and backup files on domain controllers. By default, this group has no members.                                                                                                                                                                                                      |

## User Rights Assignment
Depending on their current group membership, and other factors such as privileges that administrators can assign via Group Policy (GPO), users can have various rights assigned to their account. A few examples include:

| **Privilege**                   | **Description**                                                                                                                                                                                                                                                                                   |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SeRemoteInteractiveLogonRight` | This privilege could give our target user the right to log onto a host via Remote Desktop (RDP), which could potentially be used to obtain sensitive data or escalate privileges.                                                                                                                 |
| `SeBackupPrivilege`             | This grants a user the ability to create system backups and could be used to obtain copies of sensitive system files that can be used to retrieve passwords such as the SAM and SYSTEM Registry hives and the NTDS.dit Active Directory database file.                                            |
| `SeDebugPrivilege`              | This allows a user to debug and adjust the memory of a process. With this privilege, attackers could utilize a tool such as [Mimikatz](https://github.com/ParrotSec/mimikatz) to read the memory space of the Local System Authority (LSASS) process and obtain any credentials stored in memory. |
| `SeImpersonatePrivilege`        | This privilege allows us to impersonate a token of a privileged account such as `NT AUTHORITY\SYSTEM`. This could be leveraged with a tool such as JuicyPotato, RogueWinRM, PrintSpoofer, etc., to escalate privileges on a target system.                                                        |
| `SeLoadDriverPrivilege`         | A user with this privilege can load and unload device drivers that could potentially be used to escalate privileges or compromise a system.                                                                                                                                                       |
| `SeTakeOwnershipPrivilege`      | This allows a process to take ownership of an object. At its most basic level, we could use this privilege to gain access to a file share or a file on a share that was otherwise not accessible to us.                                                                                           |
## UAC
Administrators have more privileges than the standard user but those privileges aren't always active. That's because of elevated rights and [User Account Control (UAC)](https://docs.microsoft.com/en-us/windows/security/identity-protection/user-account-control/how-user-account-control-works) which are security features introduced with Windows Vista that default to restricting applications from running with full permissions unless absolutely necessary.

For example, if you open a terminal and use `whoami /priv` as an admin you will only see a subset of the privileges assigned to you since not all of them are active, so you are treated like a normal user. To view them in full open an elevated terminal and use the same command.
