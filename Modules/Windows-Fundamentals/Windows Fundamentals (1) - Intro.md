# The Windows Operating System
Introduced by Microsoft in 1985. Back then windows was just a GUI that ran on top of MS-DOS. Windows 95 was the first full integration of Windows and DOS and offered built-in Internet support for the first time. Over the years many versions of windows were released like Windows XP, Vista, 8, 10 and 11. Windows also offered various editions to address the needs of consumers and enterprises.

Windows Server was first released in 1993 with the release of Windows NT 3.1 Advanced Server. Over the years updates added many features to it like Internet Information Services (IIS), various networking protocols and Administrative Wizards. When Windows Server 2000 was released it introduced Active Directory, Microsoft Management Console and support for dynamic disk volumes.

Next was Windows Server 2003 which came with server roles, a built-in firewall and the Volume Shadow Copy Service. Windows Server 2008 included failover clustering, Hyper-V virtualization software, Server Core, Event Viewer, and major enhancements to Active Directory. After that many server versions were released like Windows Server 2012, 2016 and the recent 2019 version which added support for Kubernetes, Linux containers, and more advanced security features.

As new versions are released older ones stop receiving updates after some time. However, Microsoft has released out-of-band patches for earlier versions of Windows due to the discovery of the critical SMBv1 vulnerability (EternalBlue).

## Windows Versions
| Operating System Names               | Version Number |
| ------------------------------------ | -------------- |
| Windows NT 4                         | 4.0            |
| Windows 2000                         | 5.0            |
| Windows XP                           | 5.1            |
| Windows Server 2003, 2003 R2         | 5.2            |
| Windows Vista, Server 2008           | 6.0            |
| Windows 7, Server 2008 R2            | 6.1            |
| Windows 8, Server 2012               | 6.2            |
| Windows 8.1, Server 2012 R2          | 6.3            |
| Windows 10, Server 2016, Server 2019 | 10.0           |
The `Get-WmiObject` cmdlet is useful in finding information about the system. We can use it to get instances of WMI classes or information about available WMI classes. WMI (Windows Management Instrumentation) is a system in Windows for querying and managing computer info and settings. For example, by using the win32_OperatingSystem class we can get info about the version and build number. In the following example we are on Windows 10, build number 19041.
```powershell-session
PS C:\htb> Get-WmiObject -Class win32_OperatingSystem | select Version,BuildNumber

Version    BuildNumber
-------    -----------
10.0.19041 19041
```
Other useful classes:
- Win32_Process: to get a process listing
- Win32_Service: to get a listing of services
- Win32_Bios: to get BIOS info
The BIOS (Basic Input/Output System) is firmware installed on a computer's motherboard that controls the computer's essential functions, such as power management, input/output interfaces, and system configuration. We can also use the `ComputerName` parameter to get info about remote computers.

## Accessing Windows
The most common way to access a Windows system is by sitting in front of the computer running it. However this isn't the only way.

Remote access is another way which allows accessing a computer over the network which is very useful for industries like MSPs and MSSPs and even other software companies who have remote workers. Some of the most common technologies include:
- Virtual Private Networks (VPN)
- Secure Shell (SSH)
- File Transfer Protocol (FTP)
- Virtual Network Computing (VNC)
- Windows Remote Management (or PowerShell Remoting) (WinRM)
- Remote Desktop Protocol (RDP)
## Remote Desktop Protocol
In this protocol, the computer that we want to access remotely is considered the server and the one we will use to access it is considered the client. RDP listens by default on logical port `3389`. 

If the client is using windows we can use a built-in RDP application called Remote Desktop Connection ([mstsc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mstsc)). For this to work remote access must be allowed on the target windows system since it's disabled by default. Remote Desktop Connection also allows saving connection profiles which makes connecting to remote systems more convenient.

If we are using a Linux system to attack a windows system [xfreerdp](https://linux.die.net/man/1/xfreerdp) can help us access it remotely. It's a very common tool because of its ease of use, feature set, command line utility, and efficiency. We can use this command to connect to one of HTB's targets:
```shell-session
AnasSalhen@htb[/htb]$ xfreerdp /v:<targetIp> /u:htb-student /p:Password
```
Other tools exist like [Remmina](https://remmina.org/) and [rdesktop](http://www.rdesktop.org/).