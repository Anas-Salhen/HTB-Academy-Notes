# Operating System Structure
In Windows operating systems, the root directory is <drive_letter>:\ (commonly C drive). The root directory (also known as the boot partition) is where the operating system is installed. Other physical and virtual drives are assigned other letters, for example, Data (E:). The directory structure of the boot partition is as follows:

| Directory                  | Function                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Perflogs                   | Can hold Windows performance logs but is empty by default.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Program Files              | On 32-bit systems, all 16-bit and 32-bit programs are installed here. On 64-bit systems, only 64-bit programs are installed here.                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Program Files (x86)        | 32-bit and 16-bit programs are installed here on 64-bit editions of Windows.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ProgramData                | This is a hidden folder that contains data that is essential for certain installed programs to run. This data is accessible by the program no matter what user is running it.                                                                                                                                                                                                                                                                                                                                                                                  |
| Users                      | This folder contains user profiles for each user that logs onto the system and contains the two folders Public and Default.                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Default                    | This is the default user profile template for all created users. Whenever a new user is added to the system, their profile is based on the Default profile.                                                                                                                                                                                                                                                                                                                                                                                                    |
| Public                     | This folder is intended for computer users to share files and is accessible to all users by default. This folder is shared over the network by default but requires a valid network account to access.                                                                                                                                                                                                                                                                                                                                                         |
| AppData                    | Per user application data and settings are stored in a hidden user subfolder (i.e., cliff.moore\AppData). Each of these folders contains three subfolders. The Roaming folder contains machine-independent data that should follow the user's profile, such as custom dictionaries. The Local folder is specific to the computer itself and is never synchronized across the network. LocalLow is similar to the Local folder, but it has a lower data integrity level. Therefore it can be used, for example, by a web browser set to protected or safe mode. |
| Windows                    | The majority of the files required for the Windows operating system are contained here.                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| System, System32, SysWOW64 | Contains all DLLs required for the core features of Windows and the Windows API. The operating system searches these folders any time a program asks to load a DLL without specifying an absolute path.                                                                                                                                                                                                                                                                                                                                                        |
| WinSxS                     | The Windows Component Store contains a copy of all Windows components, updates, and service packs.                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
Useful commands:
- `dir`: lists files and directories
- `tree`: graphically displays the structure of a directory
```cmd-session
C:\htb> tree "c:\Program Files (x86)\VMware"
Folder PATH listing
Volume serial number is F416-77BE
C:\PROGRAM FILES (X86)\VMWARE
├───VMware VIX
│   ├───doc
│   │   ├───errors
│   │   ├───features
│   │   ├───lang
│   │   │   └───c
│   │   │       └───functions
│   │   └───types
│   ├───samples
│   └───Workstation-15.0.0
│       ├───32bit
│       └───64bit
└───VMware Workstation
    ├───env
    ├───hostd
    │   ├───coreLocale
    │   │   └───en
    │   ├───docroot
    │   │   ├───client
    │   │   └───sdk
    │   ├───extensions
    │   │   └───hostdiag
    │   │       └───locale
    │   │           └───en
    │   └───vimLocale
    │       └───en
    ├───ico
    ├───messages
    │   ├───ja
    │   └───zh_CN
    ├───OVFTool
    │   ├───env
    │   │   └───en
    │   └───schemas
    │       ├───DMTF
    │       └───vmware
    ├───Resources
    ├───tools-upgraders
    └───x64
```

# File System
There are 5 types of Windows file systems: FAT12, FAT16, FAT32, NTFS, and exFAT. FAT12 and FAT16 are no longer used in most systems.

FAT32 (File Allocation Table) is a widely used file system. The 32 in the name refers to the fact that it uses 32 bits of data for identifying data clusters on a storage device.
Pros of FAT32:
- Device compatibility - it can be used on computers, digital cameras, gaming consoles, smartphones, tablets, and more.
- Operating system cross-compatibility - It works on all Windows operating systems starting from Windows 95 and is also supported by MacOS and Linux.
Cons of FAT32:
- Can only be used with files that are less than 4GB.
- No built-in data protection or file compression features.
- Must use third-party tools for file encryption.

NTFS (New Technology File System) since Windows NT 3.1 has been the default file system. In addition to making up for the shortcomings of FAT32, NTFS also has better support for metadata and better performance due to improved data structuring.
Pros of NTFS:
- NTFS is reliable and can restore the consistency of the file system in the event of a system failure or power loss.
- Provides security by allowing us to set granular permissions on both files and folders.
- Supports very large-sized partitions.
- Has journaling built-in, meaning that file modifications (addition, modification, deletion) are logged.
Cons of NTFS:
- Most mobile devices do not support NTFS natively.
- Older media devices such as TVs and digital cameras do not offer support for NTFS storage devices.

## Permissions
| Permission Type      | Description                                                                                                                                                                                                                                                                                                                                                                                |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Full Control         | Allows reading, writing, changing, deleting of files/folders.                                                                                                                                                                                                                                                                                                                              |
| Modify               | Allows reading, writing, and deleting of files/folders.                                                                                                                                                                                                                                                                                                                                    |
| List Folder Contents | Allows for viewing and listing folders and subfolders as well as executing files. Folders only inherit this permission.                                                                                                                                                                                                                                                                    |
| Read and Execute     | Allows for viewing and listing files and subfolders as well as executing files. Files and folders inherit this permission.                                                                                                                                                                                                                                                                 |
| Write                | Allows for adding files to folders and subfolders and writing to a file.                                                                                                                                                                                                                                                                                                                   |
| Read                 | Allows for viewing and listing of folders and subfolders and viewing a file's contents.                                                                                                                                                                                                                                                                                                    |
| Traverse Folder      | This allows or denies the ability to move through folders to reach other files or folders. For example, a user may not have permission to list the directory contents or view files in the documents or web apps directory in this example c:\users\bsmith\documents\webapps\backups\backup_02042020.zip but with Traverse Folder permissions applied, they can access the backup archive. |
Files and folders inherit the NTFS permissions of their parent folder for ease of administration. If permissions do need to be set explicitly, an administrator can disable permissions inheritance for the necessary files and folders and then set the permissions directly on each.

## Integrity Control Access Control List (icacls)
NTFS permissions can be managed using the file explorer GUI under the security tab. Apart from that, it can also be managed from the command line using the `icacls` tool.
We can list permissions on a specific directory by running the `icacls` command from within that directory or specifying the path of the directory after the command.
```cmd-session
C:\htb> icacls c:\windows
c:\windows NT SERVICE\TrustedInstaller:(F)
           NT SERVICE\TrustedInstaller:(CI)(IO)(F)
           NT AUTHORITY\SYSTEM:(M)
           NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
           BUILTIN\Administrators:(M)
           BUILTIN\Administrators:(OI)(CI)(IO)(F)
           BUILTIN\Users:(RX)
           BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
           CREATOR OWNER:(OI)(CI)(IO)(F)
           APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(RX)
           APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(OI)(CI)(IO)(GR,GE)
           APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(RX)
           APPLICATION PACKAGE AUTHORITY\ALL RESTRICTED APPLICATION PACKAGES:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```
Inheritance settings:

| `(CI)` | **Container Inherit**    | This permission applies to child containers (e.g., subfolders).                          |
| ------ | ------------------------ | ---------------------------------------------------------------------------------------- |
| `(OI)` | **Object Inherit**       | This permission applies to child objects (e.g., files).                                  |
| `(IO)` | **Inherit Only**         | This permission does not apply to the object itself, only to its children.               |
| `(NP)` | **No Propagate Inherit** | Child objects can inherit this permission, but they won't pass it down further.          |
| `(I)`  | **Inherited**            | Indicates the permission was inherited from the parent folder (not explicitly set here). |
Basic access permissions:
- F : full access
- D :  delete access
- N :  no access
- M :  modify access
- RX :  read and execute access
- R :  read-only access
- W :  write-only access

### granting/removing permissions
In this example we have the C:\users directory where the "joe" user doesn't have permissions.
```cmd-session
C:\htb> icacls c:\Users
c:\Users NT AUTHORITY\SYSTEM:(OI)(CI)(F)
         BUILTIN\Administrators:(OI)(CI)(F)
         BUILTIN\Users:(RX)
         BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
         Everyone:(RX)
         Everyone:(OI)(CI)(IO)(GR,GE)

Successfully processed 1 files; Failed processing 0 files
```
Using the command `icacls c:\users /grant joe:f` we can grant him full control over this directory but since we didn't include OI or CI his rights only apply to this directory and not the files and subdirectories within.
```cmd-session
C:\htb> icacls c:\users /grant joe:f
processed file: c:\users
Successfully processed 1 files; Failed processing 0 files

C:\htb> >icacls c:\users
c:\users WS01\joe:(F)
         NT AUTHORITY\SYSTEM:(OI)(CI)(F)
         BUILTIN\Administrators:(OI)(CI)(F)
         BUILTIN\Users:(RX)
         BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
         Everyone:(RX)
         Everyone:(OI)(CI)(IO)(GR,GE)
```
Revoke these permissions using `icacls c:\users /remove joe`.
A full listing of `icacls` command-line arguments and detailed permission settings can be found [here](https://ss64.com/nt/icacls.html).

# NTFS vs Share Permissions
Microsoft's Windows owns over 70% of the global market share on desktop OSs which is why many hackers choose to develop malware for it as it's considered a high value target resulting in windows being viewed as a less secure OS.

One of the most famous vulnerabilities is EternalBlue which to this day haunts unpatched windows systems running SMBv1 and paves the way for ransomware attacks. The Server Message Block protocol (SMB) is used to connect shared resources like files and printers, see the image below:  
<img width="1020" height="612" alt="Pasted image 20250711123047" src="https://github.com/user-attachments/assets/0c3189cb-7d19-48bf-b138-41f731376773" />


NTFS permissions and share permissions aren't the same thing even though they apply to the same shared resource.
Share permissions:

|Permission|Description|
|---|---|
|`Full Control`|Users are permitted to perform all actions given by Change and Read permissions as well as change permissions for NTFS files and subfolders|
|`Change`|Users are permitted to read, edit, delete and add files and subfolders|
|`Read`|Users are allowed to view file & subfolder contents|
Reminder of NTFS basic permissions:

|Permission|Description|
|---|---|
|`Full Control`|Users are permitted to add, edit, move, delete files & folders as well as change NTFS permissions that apply to all allowed folders|
|`Modify`|Users are permitted or denied permissions to view and modify files and folders. This includes adding or deleting files|
|`Read & Execute`|Users are permitted or denied permissions to read the contents of files and execute programs|
|`List folder contents`|Users are permitted or denied permissions to view a listing of files and subfolders|
|`Read`|Users are permitted or denied permissions to read the contents of files|
|`Write`|Users are permitted or denied permissions to write changes to a file and add new files to a folder|
|`Special Permissions`|A variety of advanced permissions options|
NTFS special permissions:

| Permission                       | Description                                                                                                                                                                                                                                  |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Full control`                   | Users are permitted or denied permissions to add, edit, move, delete files & folders as well as change NTFS permissions that apply to all permitted folders                                                                                  |
| `Traverse folder / execute file` | Users are permitted or denied permissions to access a subfolder within a directory structure even if the user is denied access to contents at the parent folder level. Users may also be permitted or denied permissions to execute programs |
| `List folder/read data`          | Users are permitted or denied permissions to view files and folders contained in the parent folder. Users can also be permitted to open and view files                                                                                       |
| `Read attributes`                | Users are permitted or denied permissions to view basic attributes of a file or folder. Examples of basic attributes: system, archive, read-only, and hidden                                                                                 |
| `Read extended attributes`       | Users are permitted or denied permissions to view extended attributes of a file or folder. Attributes differ depending on the program                                                                                                        |
| `Create files/write data`        | Users are permitted or denied permissions to create files within a folder and make changes to a file                                                                                                                                         |
| `Create folders/append data`     | Users are permitted or denied permissions to create subfolders within a folder. Data can be added to files but pre-existing content cannot be overwritten                                                                                    |
| `Write attributes`               | Users are permitted or denied to change file attributes. This permission does not grant access to creating files or folders                                                                                                                  |
| `Write extended attributes`      | Users are permitted or denied permissions to change extended attributes on a file or folder. Attributes differ depending on the program                                                                                                      |
| `Delete subfolders and files`    | Users are permitted or denied permissions to delete subfolders and files. Parent folders will not be deleted                                                                                                                                 |
| `Delete`                         | Users are permitted or denied permissions to delete parent folders, subfolders and files.                                                                                                                                                    |
| `Read permissions`               | Users are permitted or denied permissions to read permissions of a folder                                                                                                                                                                    |
| `Change permissions`             | Users are permitted or denied permissions to change permissions of a file or folder                                                                                                                                                          |
| `Take ownership`                 | Users are permitted or denied permission to take ownership of a file or folder. The owner of a file has full permissions to change any permissions                                                                                           |
Share permissions and NTFS apply when the target is being accessed through SMB. However when sitting in front of the computer or using RDP we only need to consider NTFS permissions.

## Creating a Network Share
Keep in mind that in large environments shares are created on a Storage Area Network (SAN), Network Attached Storage device (NAS), or a separate partition on drives accessed via a server OS. If we come across shares on a desktop operating system, it will either be a small business or it could be a beachhead system used by a penetration tester or malicious attacker to gather and exfiltrate data.

Similar to NTFS permissions, there is an `access control list` (`ACL`) for shared resources. We can consider this the SMB permissions list. Keep in mind that with shared resources, both the SMB and NTFS permissions lists apply to every resource that gets shared in Windows. The ACL contains `access control entries` (`ACEs`). Typically these ACEs are made up of `users` & `groups` (also called security principals) as they are a suitable mechanism for managing and tracking access to shared resources.

An example using `smbclient` to list available shares:
```shell-session
AnasSalhen@htb[/htb]$ smbclient -L SERVER_IP -U htb-student
Enter WORKGROUP\htb-student's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	Company Data    Disk      
	IPC$            IPC       Remote IPC
```
Connecting to the company data share:
```shell-session
AnasSalhen@htb[/htb]$ smbclient '\\SERVER_IP\Company Data' -U htb-student
Password for [WORKGROUP\htb-student]:
Try "help" to get a list of possible commands.

smb: \> 
```
## Windows Defender Firewall Considerations
If you notice that access from a Linux system to the share is blocked it's because the firewall of windows blocks access from devices in a different workgroup. When a windows system is part of a workgroup authentication happens against that system's SAM database. When a windows system is part of a Windows Domain environment authentication happens against a centralized network-based database (Active Directory).

We can test firewall blocking connections by completely deactivating each firewall profile or by enabling specific inbound firewall rules.
Windows Defender Firewall Profiles:
- Public
- Private
- Domain
It is a best practice to enable predefined rules or add custom exceptions rather than deactivating the firewall altogether. Firewall rules on desktop systems can be centrally managed when joined to a Windows Domain environment through the use of Group Policy although that's out of the scope of this module.

## Viewing and Managing Shares
Once a connection is established we can create a mount point from our Linux system to the target system. To do that use: `sudo mount -t cifs //<server_ip_or_hostname>/<share_name> <mount_point> -o username=<user>,password=<pass>` if it doesn't work you might need to install `cifs-utils`: `sudo apt install cifs-utils`.

The `net share` command on windows allows us to see all shared folders. Notice that some folders (including C:\) are automatically shared via SMB. Even though this type of shares is hidden from casual users it still raises a security concern if not handled properly.

"Computer Management" is a useful windows tool to identify and monitor shared resources on the system. In case of a breach related to SMB this would be a great place to start and assess the situation.  
<img width="1024" height="713" alt="Pasted image 20250713021304" src="https://github.com/user-attachments/assets/c36c97ea-3a9e-414b-829c-247ede155940" />


We can also use "Event Viewer" to view share access logs as well as other actions.  
<img width="1024" height="729" alt="Pasted image 20250713044019" src="https://github.com/user-attachments/assets/7bc6a5d8-a3f9-446c-8a59-722373bf56c4" />
