# Windows Services & Processes
## Services
Services allow for the creation and management of long-running processes. Applications can also be created to install as a service like a network monitoring application installed on a server. Windows services are managed via the Service Control Manager (SCM) system, accessible via the `services.msc` MMC add-in. It provides a GUI for interacting with and managing services and displaying info about them. It's also possible to manage them via the command line using `sc.exe` or using PowerShell cmdlets like `Get-Service`.

Service statuses can appear as Running, Stopped, Paused, Starting or Stopping, and they can be set to start manually, automatically, or on a delay at system boot. Services can usually only be created, modified, and deleted by users with administrative privileges. Misconfigurations around service permissions are a common privilege escalation vector on Windows systems.

In Windows, we have some [critical system services](https://docs.microsoft.com/en-us/windows/win32/rstmgr/critical-system-services) that cannot be stopped and restarted without a system restart. If we update any file or resource in use by one of these services, we must restart the system.

| Service                   | Description                                                                                                                                                                                                                                             |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| smss.exe                  | Session Manager SubSystem. Responsible for handling sessions on the system.                                                                                                                                                                             |
| csrss.exe                 | Client Server Runtime Process. The user-mode portion of the Windows subsystem.                                                                                                                                                                          |
| wininit.exe               | Starts the Wininit file .ini file that lists all of the changes to be made to Windows when the computer is restarted after installing a program.                                                                                                        |
| logonui.exe               | Used for facilitating user login into a PC                                                                                                                                                                                                              |
| lsass.exe                 | The Local Security Authentication Server verifies the validity of user logons to a PC or server. It generates the process responsible for authenticating users for the Winlogon service.                                                                |
| services.exe              | Manages the operation of starting and stopping services.                                                                                                                                                                                                |
| winlogon.exe              | Responsible for handling the secure attention sequence, loading a user profile on logon, and locking the computer when a screensaver is running.                                                                                                        |
| System                    | A background system process that runs the Windows kernel.                                                                                                                                                                                               |
| svchost.exe with RPCSS    | Manages system services that run from dynamic-link libraries (files with the extension .dll) such as "Automatic Updates," "Windows Firewall," and "Plug and Play." Uses the Remote Procedure Call (RPC) Service (RPCSS).                                |
| svchost.exe with Dcom/PnP | Manages system services that run from dynamic-link libraries (files with the extension .dll) such as "Automatic Updates," "Windows Firewall," and "Plug and Play." Uses the Distributed Component Object Model (DCOM) and Plug and Play (PnP) services. |
This [link](https://en.wikipedia.org/wiki/List_of_Microsoft_Windows_components#Services) has a list of Windows components, including key services.

## Processes
They run in the background on Windows systems. Processes associated with installed application can be terminated without causing big problems unlike other critical processes which are associated with the OS. Those critical processes if terminated can mess with critical OS functions, examples include: Windows Logon Application, System, System Idle Process, Windows Start-Up Application, Client Server Runtime, Windows Session Manager, Service Host, and Local Security Authority Subsystem Service (LSASS) process.

## Local Security Authority Subsystem Service (LSASS)
`lsass.exe` is the process responsible for enforcing the security policy on Windows systems. When a user makes a log on attempt this process verifies it then makes an access token for them based on the user's permission levels. It's also responsible for user account password changes.  

All events associated with this process are logged within the Windows Security Log. LSASS is an extremely high value target and there are tools that can extract important info stored by this process.

## Sysinternals Tools
The [SysInternals Tools suite](https://docs.microsoft.com/en-us/sysinternals) is a set of portable Windows applications that can be used to administer Windows systems. These tools can either be downloaded from the Microsoft website or loaded directly from an internet-accessible file share by typing `\\live.sysinternals.com\tools` into a Windows Explorer window. Example:
```cmd-session
C:\htb> \\live.sysinternals.com\tools\procdump.exe -accepteula

ProcDump v9.0 - Sysinternals process dump utility
Copyright (C) 2009-2017 Mark Russinovich and Andrew Richards
Sysinternals - www.sysinternals.com

Monitors a process and writes a dump file when the process exceeds the
specified criteria or has an exception.

Capture Usage:
   procdump.exe [-mm] [-ma] [-mp] [-mc Mask] [-md Callback_DLL] [-mk]
                [-n Count]
                [-s Seconds]
                [-c|-cl CPU_Usage [-u]]
                [-m|-ml Commit_Usage]
                [-p|-pl Counter_Threshold]
                [-h]
                [-e [1 [-g] [-b]]]
                [-l]
                [-t]
                [-f  Include_Filter, ...]
                [-fx Exclude_Filter, ...]
                [-o]
                [-r [1..5] [-a]]
                [-wer]
                [-64]
                {
                 {{[-w] Process_Name | Service_Name | PID} [Dump_File | Dump_Folder]}
                |
                 {-x Dump_Folder Image_File [Argument, ...]}
                }
				
<SNIP>
```
This suite includes tools like Process Explorer, an enhanced version of Task Manager, Process Monitor, TCPView which is used to monitor internet activity and PSExec which can be used to manage/connect to systems via the SMB protocol remotely.

## Task Manager
Windows Task Manager is a powerful tool for managing Windows systems. It provides information about running processes, system performance, running services, startup programs, logged-in users/logged in user processes, and services. 

Task Manager can be opened by right-clicking on the taskbar and selecting `Task Manager`, pressing ctrl + shift + Esc, pressing ctrl + alt + del and selecting `Task Manager`, opening the start menu and typing `Task Manager`, or typing `taskmgr` from a CMD or PowerShell console.

| Tab             | Description                                                                                                                                                                                                                                                      |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Processes tab   | Shows a list of running applications and background processes along with the CPU, memory, disk, network, and power usage for each.                                                                                                                               |
| Performance tab | Shows graphs and data such as CPU utilization, system uptime, memory usage, disk and, networking, and GPU usage. We can also open the `Resource Monitor`, which gives us a much more in-depth view of the current CPU, Memory, Disk, and Network resource usage. |
| App history tab | Shows resource usage for the current user account for each application for a period of time.                                                                                                                                                                     |
| Startup tab     | Shows which applications are configured to start at boot as well as the impact on the startup process.                                                                                                                                                           |
| Users tab       | Shows logged in users and the processes/resource usage associated with their session.                                                                                                                                                                            |
| Details tab     | Shows the name, process ID (PID), status, associated username, CPU, and memory usage for each running application.                                                                                                                                               |
| Services tab    | Shows the name, PID, description, and status of each installed service. The Services add-in can be accessed from this tab as well.                                                                                                                               |

## Process Explorer
A part of the Sysinternals tool suite. It is a free tool from Microsoft that helps you see everything running on your Windows computer in more detail than Task Manager. It shows you all active programs, what files or system resources (called handles) they’re using, and what background files (called DLLs) they’ve loaded.  
You can even search to find out which program is using a certain file or library. It also shows which programs started other programs (parent-child relationships), which is helpful for spotting problems or strange behavior. If something gets stuck or doesn’t close properly.

# Service Permissions
Misconfigurations in service permissions can cause serious security vulnerabilities that can be used to load malicious DLLs, execute applications without access to an admin account, escalate privileges and even maintain persistence.

On server OSs it's common for critical network services like DHCP and Active Directory Domain Services to use the privileges of the admin who installed them. Now suppose that admin no longer works at the company and he doesn't have those permissions anymore those services would fail to work causing major issues. That's why it's recommended to create an account designated to run critical network services, these are called service accounts.

We should be aware of service permissions and the permissions of the directories they execute from. For example, if any user can edit those directories normal executable files could be replaced with malicious files.

## Examining Services using services.msc
`services.msc` is a very useful tool for viewing and managing services.
![[Pasted image 20250714134903.png]]
Most services run with LocalSystem privileges by default which is the highest level of access allowed on the OS. Not all applications need those kind of permissions so we need to identify the ones that can run with the least privileges possible.  
Notable built-in service accounts in Windows:
- LocalService
- NetworkService
- LocalSystem
Note: We can also create new accounts and use them for the sole purpose of running a service.

There is also the recovery tab that can be configured to do something when the service fails.

## Examining services using sc
`sc` is a command line tool for managing and querying services. It's more script friendly than GUI tools like `services.msc`.
`sc qc <ServiceName>`: queries the service and this is where it's useful to know the name of the service.  
`sc \\<RemoteComputerNameOrIP> query <ServiceName>`: queries a service over the network.  
```cmd-session
C:\Users\htb-student> sc stop wuauserv

[SC] OpenService FAILED 5:

Access is denied.
```
We couldn't stop the `wuauserv` service and access was denied. To solve this run command prompt as administrator.

```cmd-session
C:\WINDOWS\system32> sc config wuauserv binPath=C:\Windows\Perfectlylegitprogram.exe

[SC] ChangeServiceConfig SUCCESS

C:\WINDOWS\system32> sc qc wuauserv

[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: wuauserv
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Windows\Perfectlylegitprogram.exe
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Windows Update
        DEPENDENCIES       : rpcss
        SERVICE_START_NAME : LocalSystem
```
Here we replaced the original executable file of the service with a malicious file called `Perfectlylegitprogram.exe`. This is an example of what an attacker might do. The `sc qc` command shows the path to the executable which is useful in investigating these things.

We can examine service permissions through `sc sdshow <ServiceName>`:
```cmd-session
C:\WINDOWS\system32> sc sdshow wuauserv

D:(A;;CCLCSWRPLORC;;;AU)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;BA)(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;SY)S:(AU;FA;CCDCLCSWRPWPDTLOSDRCWDWO;;;WD)
```
This output looks unreadable but every part of it has a meaning. Every named object (and some unnamed objects) in windows is a securable object which means it has a security descriptor. The security descriptor tells windows who owns the object, what groups are associated with it, who is allowed or denied access (DACL), what access attempts should be logged (SACL).

The output above is in a format known as Security Descriptor Definition Language (SDDL). Each set of characters between parentheses is an Access Control Entry (ACE) and should be examined one by one. ACEs after D: are part of the Discretionary Access Control List (DACL) and the ones after S: are part of the System Access Control List (SACL).

`(A;;CCLCSWRPLORC;;;AU)`  
Take this ACE for example, to read it separate it into 3 parts.

Before the semi-colons `A;;`  
Specifies whether the actions in the next part will be allowed (`A`) or denied (`D`).

Between the semi-colons `;;CCLCSWRPLORC;;;`  
Each set of 2 characters is an action that is either allowed or denied depending on the previous part. Here is a list of the most common ones:

| Code   | Meaning                                                                 | Applies to    |
| ------ | ----------------------------------------------------------------------- | ------------- |
| **CC** | `SERVICE_QUERY_CONFIG` – View service config                            | Services      |
| **DC** | `SERVICE_CHANGE_CONFIG` – Change service config                         | Services      |
| **LC** | `SERVICE_QUERY_STATUS` – Query status                                   | Services      |
| **SW** | `SERVICE_ENUMERATE_DEPENDENTS` – Enumerate a list of dependent services | Services      |
| **RP** | `SERVICE_START` – Start the service                                     | Services      |
| **WP** | `SERVICE_STOP` – Stop the service                                       | Services      |
| **DT** | `DELETE` – Delete the service                                           | Services      |
| **LO** | `SERVICE_INTERROGATE` – Ask for service status                          | Services      |
| **CR** | `SERVICE_USER_DEFINED_CONTROL` – Send control code                      | Services      |
| **SD** | `WRITE_DAC` – Change permissions                                        | All objects   |
| **RC** | `READ_CONTROL` – Read security descriptor                               | All objects   |
| **WD** | `WRITE_DAC` – Change permissions (same as SD)                           | All objects   |
| **WO** | `WRITE_OWNER` – Take ownership                                          | All objects   |
| **FA** | `FULL_ACCESS` – All permissions (used in SACL)                          | All           |
| **KA** | `KEY_ALL_ACCESS` – Registry                                             | Registry keys |
| **GR** | `GENERIC_READ`                                                          | General       |
| **GW** | `GENERIC_WRITE`                                                         | General       |
| **GX** | `GENERIC_EXECUTE`                                                       | General       |

After the semi-colons `;;;AU`  
The security principle (user and/or group) that these permissions will be applied to. Common ones include:

|Code|Meaning|
|---|---|
|**AU**|Authenticated Users|
|**BA**|Built-in Administrators|
|**SY**|Local System|
|**WD**|Everyone (World)|
|**LA**|Local Administrator|
|**LS**|Local Service|
|**NS**|Network Service|
|**CO**|Creator Owner|

## Examine Service Permissions Using PowerShell
Use `Get-Acl` with the path of a specific service in the registry.
```powershell-session
PS C:\Users\htb-student> Get-ACL -Path HKLM:\System\CurrentControlSet\Services\wuauserv | Format-List

Path   : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\wuauserv
Owner  : NT AUTHORITY\SYSTEM
Group  : NT AUTHORITY\SYSTEM
Access : BUILTIN\Users Allow  ReadKey
         BUILTIN\Users Allow  -2147483648
         BUILTIN\Administrators Allow  FullControl
         BUILTIN\Administrators Allow  268435456
         NT AUTHORITY\SYSTEM Allow  FullControl
         NT AUTHORITY\SYSTEM Allow  268435456
         CREATOR OWNER Allow  268435456
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  ReadKey
         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES Allow  -2147483648
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow
         ReadKey
         S-1-15-3-1024-1065365936-1281604716-3511738428-1654721687-432734479-3232135806-4053264122-3456934681 Allow
         -2147483648
Audit  :
Sddl   : O:SYG:SYD:AI(A;ID;KR;;;BU)(A;CIIOID;GR;;;BU)(A;ID;KA;;;BA)(A;CIIOID;GA;;;BA)(A;ID;KA;;;SY)(A;CIIOID;GA;;;SY)(A
         ;CIIOID;GA;;;CO)(A;ID;KR;;;AC)(A;CIIOID;GR;;;AC)(A;ID;KR;;;S-1-15-3-1024-1065365936-1281604716-3511738428-1654
         721687-432734479-3232135806-4053264122-3456934681)(A;CIIOID;GR;;;S-1-15-3-1024-1065365936-1281604716-351173842
         8-1654721687-432734479-3232135806-4053264122-3456934681)
```
Notice that this SDDL is more complex than the one we viewed earlier. For instance, some security principles here are referred to using their SIDs not codes (like `AU`).