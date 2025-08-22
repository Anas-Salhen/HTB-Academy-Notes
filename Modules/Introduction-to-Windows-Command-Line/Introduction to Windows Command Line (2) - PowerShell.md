Last Update: 2025-08-22 4:10 PM
# CMD Vs. PowerShell
| **Feature**         | **CMD**                                                                                                                                       | **PowerShell**                                                                                                                   |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| Language            | Batch and basic CMD commands only.                                                                                                            | PowerShell can interpret Batch, CMD, PS cmdlets, and aliases.                                                                    |
| Command utilization | The output from one command cannot be passed into another directly as a structured object, due to the limitation of handling the text output. | The output from one command can be passed into another directly as a structured object resulting in more sophisticated commands. |
| Command Output      | Text only.                                                                                                                                    | PowerShell outputs in object formatting.                                                                                         |
| Parallel Execution  | CMD must finish one command before running another.                                                                                           | PowerShell can multi-thread commands to run in parallel.                                                                         |
PowerShell is built to be extensible and it works with many different tools, it can even be used with Linux based systems. It's not just a CLI it's also a scripting language that is very useful in automation.

Since it comes with more capabilities it's usually the best option for most IT professionals. For a pentester, its import capability makes it easy to bring our tools to the environment and use them. However, from a stealth perspective its history and logging capabilities are very powerful which might be a reason to use CMD if you don't need PowerShell's features.

## Calling PowerShell
1. Using windows search.
2. Using the windows terminal app
3. Using windows PowerShell ISE: it's like an IDE, it can make it easier to develop, debug and test the scripts we create.
4. Using it in CMD: type `powershell.exe` in the command prompt.

## Help
`Get-Help <command>` is what you want to use if you need info about a command. It comes with useful options like `-online`, which will open a Microsoft docs webpage for the corresponding cmdlet if the host has Internet access.

There is also `Update-Help` which will update the help info for each command so when you run `Get-Help` next time you see the latest info.

## Basic Commands
To list a directory: `Get-ChildItem`, can also add a path to a directory you want to list.  
Move to a new directory: `Set-Location`, works with both relative and absolute paths.  
View file content: `Get-Content <file>`.  
Clear screen: `Clear-Host`, `clear` or `cls`.

## Get-Command
PowerShell commands are written in the form `Verb-Noun` (like `Get-Help`). If we have a specific command in mind but we forgot its exact name we can use `Get-Command` to help us find it. We can use it with the `-verb` and `-noun` options to show all commands that contain that specific verb or noun. For example, `Get-Command -noun windows*` shows all commands that start with windows in the noun portion and contain any or no letters after that (because of the `*` wildcard).

## History
History in PowerShell is stored in 2 ways. The first is the built-in session history (`Get-History`) which only stores the commands used in the current session and gets cleared if we end the session. 
```powershell-session
PS C:\htb> Get-History

 Id CommandLine
  -- -----------
   1 Get-Command
   2 clear
   3 get-command -verb set
   4 get-command set*
   5 clear
   6 get-command -verb get
   7 get-command -noun windows
   8 get-command -noun windows*
   9 get-module
  10 clear
  11 get-history
  12 clear
  13 ipconfig /all
  14 arp -a
  15 get-help
  16 get-help get-module
```
Each of these commands is numbered. We can run one of them if we want by typing `r` followed by the command number (like `r 2` for `clear`).

The other is through the `PSReadLine` module which tracks every command used across all sessions. It stores the history in a file called `$($host.Name)_history.txt` located at `$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine`. By default it stores the last 4096 commands but this can be changed by editing the `$MaximumHistoryCount` variable. 
```powershell-session
PS C:\htb> get-content C:\Users\DLarusso\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

get-module
Get-ChildItem Env: | ft Key,Value
Get-ExecutionPolicy
clear
ssh administrator@10.172.16.110.55
powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('https://download.sysinternals.com/files/PSTools.zip')"
Get-ExecutionPolicy

<SNIP>
```
One great feature of `PSReadline` from an admin perspective is that it will automatically attempt to filter any entries that include the strings:
- `password`
- `asplaintext`
- `token`
- `apikey`
- `secret`
This behavior is excellent for us as admins since it will help clear any entries from the `PSReadLine` history file that contain sensitive information.

## Hotkeys
| **HotKey**         | **Description**                                                                                                                                     |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `CTRL+R`           | It makes for a searchable history. We can start typing after, and it will show us results that match previous commands.                             |
| `CTRL+L`           | Quick screen clear.                                                                                                                                 |
| `CTRL+ALT+Shift+?` | This will print the entire list of keyboard shortcuts PowerShell will recognize.                                                                    |
| `Escape`           | When typing into the CLI, if you wish to clear the entire line, instead of holding backspace, you can just hit `escape`, which will erase the line. |
| `↑`                | Scroll up through our previous history.                                                                                                             |
| `↓`                | Scroll down through our previous history.                                                                                                           |
| `F7`               | Brings up a TUI with a scrollable interactive history from our session.                                                                             |
We can use `tab` and `SHIFT+tab` to move through options that can complete the command we are typing.

## Aliases
Most built-in aliases are shortened versions of the cmdlet, making it easier to remember and quick to use. To see a list of default aliases use: `Get-Alias` (btw its alias is `gal`). We can also set an alias for a specific cmdlet using `Set-Alias`.
```powershell-session
PS C:\Windows\system32> Set-Alias -Name gh -Value Get-Help
```
In this example we set the alias `gh` for the `Get-Help` command. Below is a list of helpful aliases:

| **Alias**   | **Description**                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------- |
| `pwd`       | `gl` can also be used. This alias can be used in place of `Get-Location`.                                 |
| `ls`        | `dir` and `gci` can also be used in place of ls. This is an alias for `Get-ChildItem`.                    |
| `cd`        | `sl` and `chdir` can be used in place of `cd`. This is an alias for `Set-Location`.                       |
| `cat`       | `type` and `gc` can also be used. This is an alias for `Get-Content`.                                     |
| `clear`     | Can be used in place of `Clear-Host`.                                                                     |
| `curl`      | `curl` is an alias for `Invoke-WebRequest`, which can be used to download files. `wget` can also be used. |
| `fl and ft` | These aliases can be used to format output into list and table outputs.                                   |
| `man`       | Can be used in place of `help`.                                                                           |
For those familiar with `BASH`, you may have noticed that many of the aliases match up to commands widely used within Linux distributions. This knowledge can be helpful and help ease the learning curve.

# All About Cmdlets and Modules
cmdlets are PowerShell commands that perform a specific action and follow a Verb-Noun format (like `Get-Help`) which makes it easy to figure out the purpose of each cmdlet. They are written in C# or another language then compiled for use.

## PowerShell Modules
A [PowerShell module](https://docs.microsoft.com/en-us/powershell/scripting/developer/module/understanding-a-windows-powershell-module?view=powershell-7.2) is structured PowerShell code that is made easy to use & share. As mentioned in the official Microsoft docs, a module can be made up of the following:
- Cmdlets
- Script files
- Functions
- Assemblies
- Related resources (manifests and help files)
We are going to use the [PowerSploit](https://github.com/PowerShellMafia/PowerSploit) project created by the [PowerShellMafia](https://github.com/PowerShellMafia/PowerSploit) which aims to provide useful tools to pentesters when testing Windows Domain/Active Directory environments. Though we may notice this project has been archived, many of the included tools are still relevant in pentesting today (written in August 2022).  
<img width="1024" height="949" alt="Pasted image 20250725204747" src="https://github.com/user-attachments/assets/fb7c6994-3fab-4528-98c9-0f4afd851b0d" />

A PowerShell data file (`.psd1`) is a [Module manifest file](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_module_manifests?view=powershell-7.2). Within it we can find:
- Reference to the module that will be processed
- Version numbers to keep track of major changes
- The GUID
- The Author of the module
- Copyright
- PowerShell compatibility information
- Modules & cmdlets included
- Metadata

A PowerShell script module file (`.psm1`) is simply a script containing PowerShell code. Think of this as the meat of a module.

## Using PowerShell Modules
Once we decide which module to use we can use `Get-Module` to know all loaded modules.  
`Get-Module -ListAvailable` shows all modules we have installed but not loaded into our session. By using `Import-Module` we can load a module into our current PowerShell session.
```powershell-session
PS C:\Users\htb-student\Desktop\PowerSploit> Import-Module .\PowerSploit.psd1
PS C:\Users\htb-student\Desktop\PowerSploit> Get-NetLocalgroup

ComputerName GroupName                           Comment
------------ ---------                           -------
WS01         Access Control Assistance Operators Members of this group can...
WS01         Administrators                      Administrators have complete...
WS01         Backup Operators                    Backup Operators can override...
WS01         Cryptographic Operators             Members are authorized to perf...
WS01         Distributed COM Users               Members are allowed to launch...
WS01         Event Log Readers                   Members of this group can read...
WS01         Guests                              Guests have the same access as...
WS01         Hyper-V Administrators              Members of this group have comp...
WS01         IIS_IUSRS                           Built-in group used by Internet...
WS01         Network Configuration Operators     Members in this group can have...
WS01         Performance Log Users               Members of this group may sched...
WS01         Performance Monitor Users           Members of this group can acces...
WS01         Power Users                         Power Users are included for ba...
WS01         Remote Desktop Users                Members in this group are grant...
WS01         Remote Management Users             Members of this group can acces...
WS01         Replicator                          Supports file replication in a...
WS01         System Managed Accounts Group       Members of this group are manag...
WS01         Users                               Users are prevented from making...
```
In this example we were able to use `Get-NetLocalgroup` which is a PowerSploit command only because we imported `.\PowerSploit.psd1`.

## Execution Policy
This is something that we need to consider when running PowerShell scripts or modules. As outlined in Microsoft's official documentation, an execution policy is not a security control. It is designed to give IT admins a tool to set parameters and safeguards for themselves.
```powershell-session
PS C:\htb> Get-ExecutionPolicy 

Restricted  
```
Since our execution policy is restricted, if we attempt to run `Import-Module .\PowerSploit.psd1` it will fail. We need to change it using `Set-ExecutionPolicy undefined` and then import the module. We can also change the execution policy at the process level so when we close our session the change is reverted.
```powershell-session
PS C:\htb> Set-ExecutionPolicy -scope Process 
PS C:\htb> Get-ExecutionPolicy -list

Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process          Bypass
  CurrentUser       Undefined
 LocalMachine          Bypass  
```

## Viewing Module cmdlets
`Get-Command -Module <modulename>` outputs all commands within that module.

## Finding and Installing Modules
[PowerShell Gallery](https://www.powershellgallery.com/) is a repository that contains PowerShell scripts, modules, and more created by Microsoft and other users. Conveniently for us, `PowerShellGet` is a built-in PowerShell module that helps us interact with it. This module contains many useful commands like `Find-Module` and `Install-Module`
```powershell-session
PS C:\htb> Find-Module -Name AdminToolbox 

Version    Name                                Repository           Description
-------    ----                                ----------           -----------
11.0.8     AdminToolbox                        PSGallery            Master modul...
```
In this example we found the `AdminToolbox` module which is within PowerShell Gallery. Now we need to install it, one way to do it is: `Find-Module -Name AdminToolbox | Install-Module`.

## Tools to Know
- [AdminToolbox](https://www.powershellgallery.com/packages/AdminToolbox/11.0.8): AdminToolbox is a collection of helpful modules that allow system administrators to perform any number of actions dealing with things like Active Directory, Exchange, Network management, file and storage issues, and more.

- [ActiveDirectory](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps): This module is a collection of local and remote administration tools for all things Active Directory. We can manage users, groups, permissions, and much more with it.

- [Empire / Situational Awareness](https://github.com/BC-SECURITY/Empire/tree/master/empire/server/data/module_source/situational_awareness): Is a collection of PowerShell modules and scripts that can provide us with situational awareness on a host and the domain they are apart of. This project is being maintained by [BC Security](https://github.com/BC-SECURITY) as a part of their Empire Framework.

- [Inveigh](https://github.com/Kevin-Robertson/Inveigh): Inveigh is a tool built to perform network spoofing and Man-in-the-middle attacks.

- [BloodHound / SharpHound](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors): Bloodhound/Sharphound allows us to visually map out an Active Directory Environment using graphical analysis tools and data collectors written in C# and PowerShell.

# User and Group Management
Types of user accounts:
- Service Accounts
- Built-in accounts
- Local users
- Domain users

## Default Local User Accounts
| **Account**           | **Description**                                                                                                                                                  |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Administrator`       | This account is used to accomplish administrative tasks on the local host.                                                                                       |
| `Default Account`     | The default account is used by the system for running multi-user auth apps like the Xbox utility.                                                                |
| `Guest Account`       | This account is a limited rights account that allows users without a normal user account to access the host. It is disabled by default and should stay that way. |
| `WDAGUtility Account` | This account is in place for the Defender Application Guard, which can sandbox application sessions.                                                             |

## Brief Intro to Active Directory
In a nutshell, `Active Directory` (AD) is a directory service for Windows environments that provides a central point of management for `users`, computers, `groups`, network devices, `file shares`, group policies, `devices`, and trusts with other organizations.

In this module we won't dive deeply into AD. We are only concerned with AD in the context of users and groups, we can administer them from PowerShell on `any domain joined host` utilizing the `ActiveDirectory` Module.

## Local vs Domain Joined Users
`Domain` users differ from `local` users in that they are granted rights from the domain to access resources such as file servers, printers, intranet hosts, and other objects based on user and group membership. Domain user accounts can log in to any host in the domain, while the local user only has permission to access the specific host they were created on.

## What Are User Groups?
Groups are a way to sort user accounts logically and, in doing so, provide granular permissions and access to resources without having to manage each user manually. We can get local groups on our host using `Get-LocalGroup`.

## Adding/Removing/Editing Local Users and Groups
### Users
`Get-LocalUser`: Displays local users on this host
`New-LocalUser -Name "JLawrence" -NoPassword`: Creates a user called JLawrence and doesn't specify a password.
```powershell-session
PS C:\htb> $Password = Read-Host -AsSecureString
****************
PS C:\htb> Set-LocalUser -Name "JLawrence" -Password $Password -Description "CEO EagleFang"
```
In this example we store the input that we type in the `$Password` variable as a secure (encrypted) string Then we use `Set-LocalUser` to change the password to the one inside the variable and add a description.

### Groups
`Get-LocalGroup`: Displays local groups on this host  
`Get-LocalGroupMember -Name "<GroupName>"`: Displays users that are members of that group
`Add-LocalGroupMember -Group "<GroupName>" -Member "<UserName>"`: Adds this user to the specified group  

## Managing Domain Users and Groups
### Installing The Module
To access Active Directory cmdlets we must install the ActiveDirectory module. If you installed AdminToolBox it will have a repackaged version of it though it's better to use the official version. To install this version we need to have an optional feature called `Remote System Administration Tools` (RSAT).
```powershell-session
PS C:\htb> Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability -Online

Path          :  
Online        : True  
RestartNeeded : False  
```
The above command will install `ALL` RSAT features in the Microsoft Catalog. If we wish to stay lightweight, we can install the package named `Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0`. Now that we have the ActiveDirectory module we can start using the commands.  

### Managing Users
To get all users: `Get-ADUser -Filter *`  
To get a user with a specific name: `Get-ADUser -Identity <name>`

```powershell-session
PS C:\htb>  Get-ADUser -Identity TSilver


DistinguishedName : CN=TSilver,CN=Users,DC=greenhorn,DC=corp
Enabled           : True
GivenName         :
Name              : TSilver
ObjectClass       : user
ObjectGUID        : a19a6c8a-000a-4cbf-aa14-0c7fca643c37
SamAccountName    : TSilver
SID               : S-1-5-21-1480833693-1324064541-2711030367-1602
Surname           :
UserPrincipalName :
  
```
Info about this user includes:
- `Object Class`: Specifies if the object is a user, computer, or another type of object.
- `DistinguishedName`: Specifies the object's relative path within the AD schema.
- `Enabled`: Tells us if the user is active and can log in.
- `SamAccountName`: The representation of the username used to log into the ActiveDirectory hosts.
- `ObjectGUID`: Is the unique identifier of the user object.

We can also get a user by filtering for attributes other than the name like the email address:  
`Get-ADUser -Filter {EmailAddress -like '*greenhorn.corp'}`  
This command gets the user whose email address ends with `greenhorn.corp`.
```powershell-session
PS C:\htb> New-ADUser -Name "MTanaka" -Surname "Tanaka" -GivenName "Mori" -Office "Security" -OtherAttributes @{'title'="Sensei";'mail'="MTanaka@greenhorn.corp"} -Accountpassword (Read-Host -AsSecureString "AccountPassword") -Enabled $true 

AccountPassword: ****************
```
This very long command creates a new user called Mori Tanaka and assigns him his attributes.
```powershell-session
Get-ADUser -Identity MTanaka -Properties * | Format-Table Name,Enabled,GivenName,Surname,Title,Office,Mail

Name    Enabled GivenName Surname Title  Office   Mail
----    ------- --------- ------- -----  ------   ----
MTanaka    True Mori      Tanaka  Sensei Security MTanaka@greenhorn.corp
```
Here we got the user we just created then formatted the info we need in a table.
```powershell-session
PS C:\htb> Set-ADUser -Identity MTanaka -Description "Sensei to Security Analyst's Rocky, Colt, and Tum-Tum"
```
Here we changed one of his attribute (description) using `Set-ADUser`.

Enumerating users and groups is extremely important. Things like misconfigurations, additional unnecessary privileges are useful in pentesting. This info can be easily found by tools like Bloodhound.

# Working with Files and Directories - PowerShell

| **Command**      | **Alias**        | **Description**                                                                                                                            |
| ---------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `Get-Location`   | gl / pwd         | Outputs your current working directory.                                                                                                    |
| `Set-Location`   | sl / cd / chdir  | Changes your current working directory.                                                                                                    |
| `Get-Item`       | gi               | Retrieve an object (could be a file, folder, registry object, etc.)                                                                        |
| `Get-ChildItem`  | ls / dir / gci   | Lists out the content of a folder or registry hive.                                                                                        |
| `New-Item`       | md / mkdir / ni  | Create new objects. ( can be files, folders, symlinks, registry entries, and more) Important parameters: `-Name`, `-ItemType` and `-Path`. |
| `Set-Item`       | si               | Modify the property values of an object.                                                                                                   |
| `Copy-Item`      | copy / cp / ci   | Make a duplicate of the item.                                                                                                              |
| `Rename-Item`    | ren / rni        | Changes the object name.                                                                                                                   |
| `Remove-Item`    | rm / del / rmdir | Deletes the object.                                                                                                                        |
| `Get-Content`    | cat / type       | Displays the content within a file or object.                                                                                              |
| `Add-Content`    | ac               | Append content to a file.                                                                                                                  |
| `Set-Content`    | sc               | Overwrite any content in a file with new data.                                                                                             |
| `Clear-Content`  | clc              | Clear the content of the files without deleting the file itself.                                                                           |
| `Compare-Object` | diff / compare   | Compare two or more objects against each other. This includes the object itself and the content within.                                    |
 ```powershell-session
PS C:\Users\MTanaka\Desktop> ls

    Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/13/2022   1:05 PM              0 file-1.txt
-a----        10/13/2022   1:05 PM              0 file-2.txt
-a----        10/13/2022   1:06 PM              0 file-3.txt
-a----        10/13/2022   1:06 PM              0 file-4.txt
-a----        10/13/2022   1:06 PM              0 file-5.txt

PS C:\Users\MTanaka\Desktop> get-childitem -Path *.txt | rename-item -NewName {$_.name -replace ".txt",".md"}
PS C:\Users\MTanaka\Desktop> ls

    Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/13/2022   1:05 PM              0 file-1.md
-a----        10/13/2022   1:05 PM              0 file-2.md
-a----        10/13/2022   1:06 PM              0 file-3.md
-a----        10/13/2022   1:06 PM              0 file-4.md
-a----        10/13/2022   1:06 PM              0 file-5.md
```
This example is a good way to save time. Instead of renaming files one by one we used `Get-Childitem` and filtered for paths that end with .txt then piped that as input to another command that renames everything from its original name ($\_.name refers to the original name) and changes the `.txt` from that name to `.md`.

## Permission Types
- `Full Control`: Full Control allows for the user or group specified the ability to interact with the file as they see fit. This includes everything below, changing the permissions, and taking ownership of the file.
- `Modify`: Allows reading, writing, and deleting files and folders.
- `List Folder Contents`: This makes viewing and listing folders and subfolders possible along with executing files. This only applies to `folders`.
- `Read and Execute`: Allows users to view the contents within files and run executables (.ps1, .exe, .bat, etc.)
- `Write`: Allows a user the ability to create new files and subfolders along with being able to add content to files.
- `Read`: Allows for viewing and listing folders and subfolders and viewing a file's contents.
- `Traverse Folder`: Traverse allows us to give a user the ability to access files or subfolders within a tree but not have access to the higher-level folder's contents. This is a way to provide selective access from a security perspective.

Playing with permissions is a complex task which is explained in the Windows Fundamentals module.

# Finding & Filtering Content
## Terminology
In PowerShell unlike CMD or Bash the things we interact with aren't just text or strings, we interact with objects.

An **object** = a specific thing that exists in memory. It's the unit or structure that we interact with when playing with commands.

A **class** = the blueprint for objects. The class defines what information an object has (properties) and what it can do (methods). Example: if "object = your computer," then "class = the design/spec sheet of computers in general."

## Working With Objects
```powershell-session
PS C:\htb> Get-LocalUser administrator | get-member

   TypeName: Microsoft.PowerShell.Commands.LocalUser

Name                   MemberType Definition
----                   ---------- ----------
Clone                  Method     Microsoft.PowerShell.Commands.LocalUser Clone()
Equals                 Method     bool Equals(System.Object obj)
GetHashCode            Method     int GetHashCode()
GetType                Method     type GetType()
ToString               Method     string ToString()
AccountExpires         Property   System.Nullable[datetime] AccountExpires...
Description            Property   string Description {get;set;}
Enabled                Property   bool Enabled {get;set;}
FullName               Property   string FullName {get;set;}
LastLogon              Property   System.Nullable[datetime] LastLogon {get;set;}
Name                   Property   string Name {get;set;}
ObjectClass            Property   string ObjectClass {get;set;}
PasswordChangeableDate Property   System.Nullable[datetime] PasswordChangeableDa...
PasswordExpires        Property   System.Nullable[datetime] PasswordExpires...
PasswordLastSet        Property   System.Nullable[datetime] PasswordLastSet...
PasswordRequired       Property   bool PasswordRequired {get;set;}
PrincipalSource        Property   System.Nullable[Microsoft.PowerShell.Commands...
SID                    Property   System.Security.Principal.SecurityIdentifier S...
UserMayChangePassword  Property   bool UserMayChangePassword {get;set;}
```
In this example the first command gets a user named administrator which is piped to the `get-member` command. `Get-Member` receives this input (an object) and displays its properties and methods.

If we attempt to run `Get-LocalUser administrator` we will see some properties of this object but not all of them. To see them all use `Select-Object`.
```powershell-session
PS C:\htb> Get-LocalUser administrator | Select-Object -Property *


AccountExpires         :
Description            : Built-in account for administering the computer/domain
Enabled                : False
FullName               :
PasswordChangeableDate :
PasswordExpires        :
UserMayChangePassword  : True
PasswordRequired       : True
PasswordLastSet        :
LastLogon              : 1/20/2021 5:39:14 PM
Name                   : Administrator
SID                    : S-1-5-21-3916821513-3027319641-390562114-500
PrincipalSource        : Local
ObjectClass            : User
```
Instead of `-Property *` we can use `-Property Name,PasswordLastSet` to show us the properties we care about only.

```powershell-session
PS C:\htb> Get-LocalUser * | Group-Object -property Enabled

Count Name                      Group
----- ----                      -----
    4 False                     {Administrator, DefaultAccount, Guest, WDAGUtilityAccount}
    1 True                      {MTanaka}
```
In this example the first command got all users and then handed them (as objects) to the second command. `Group-Object` received these objects and grouped them based on whether their Enabled property is True or False.

Another useful command is `Sort-Object` which sorts objects based on the property we specify in ascending or descending order.

`Where-Object` (`where` as an alias) is helpful in matching specific properties to narrow down results. It uses comparison operators to match values, here is a list of some useful ones:

| **Expression** | **Description**                                                                                                                                         |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Like`         | Like utilizes wildcard expressions to perform matching. For example, `'*Defender*'` would match anything with the word Defender somewhere in the value. |
| `Contains`     | Contains will get the object if any item in the property value matches exactly as specified.                                                            |
| `Equal` to     | Specifies an exact match (case sensitive) to the property value supplied.                                                                               |
| `Match`        | Is a regular expression match to the value supplied.                                                                                                    |
| `Not`          | specifies a match if the property is `blank` or does not exist. It will also match `$False`.                                                            |
```powershell-session
PS C:\htb> Get-Service | where DisplayName -like '*Defender*' | Select-Object -Property *

Name                : mpssvc
RequiredServices    : {mpsdrv, bfe}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : False
DisplayName         : Windows Defender Firewall
DependentServices   :
MachineName         : .
ServiceName         : mpssvc
ServicesDependedOn  : {mpsdrv, bfe}
ServiceHandle       :
Status              : Running
ServiceType         : Win32ShareProcess
StartType           : Automatic
Site                :
Container           :

Name                : Sense
RequiredServices    : {}
CanPauseAndContinue : False
CanShutdown         : False
CanStop             : False
DisplayName         : Windows Defender Advanced Threat Protection Service
<SNIP>
```
This is a good example of using where and other cmdlets to get useful info about services related to windows defender.

## Pipeline Chain Operators
We already know how a pipe (`|`) is used from CMD.  

_Currently, Windows PowerShell 5.1 and older do not support Pipeline Chain Operators used in this fashion. If you see errors, you must install PowerShell 7 alongside Windows PowerShell. They are not the same thing._
`<command1> && <command2>` 2nd command only executes if 1st command is successful (like CMD).  
`<command1> || <command2>` execute 2nd command if 1st command fails (unlike CMD)

## Finding Data Within Content
`Select-String` is a tool to find a match for a regex expression in a string input or a file, it works in a similar way to `grep` in Linux and `findstr.exe` in CMD.

```powershell-session
PS C:\htb> Get-Childitem –Path C:\Users\MTanaka\ -File -Recurse -ErrorAction SilentlyContinue | where {($_.Name -like "*.txt" -or $_.Name -like "*.md")}

 Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          10/11/2022  3:32 PM            183 demo-notes.txt
-a---          10/11/2022 10:22 AM           1286 github-creds.txt
-a---            4/4/2022  9:37 AM            188 q2-to-do.txt
-a---           9/18/2022 12:35 PM             30 notes.txt
-a---          10/12/2022 11:26 AM             14 test.txt
-a---            1/4/2022 11:23 PM            310 Untitled-1.txt

    Directory: C:\Users\MTanaka\Desktop\notedump\NoteDump

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---           4/26/2022  1:47 PM           1092 demo.md
-a---           4/22/2022  2:20 PM            375 README.md
```
The command above utilized tools we already know about to perform a recursive search for files ending in `.txt` or `.md`. We can take this output and feed it to `Select-Object` to search these files for important info.

```powershell-session
PS C:\htb> Get-Childitem –Path C:\Users\MTanaka\ -File -Recurse -ErrorAction SilentlyContinue | where {($_. Name -like "*.txt" -or $_. Name -like "*.md")} | sls "Password","credential","key","UserName"

New-PC-Setup.md:56:  - getting your vpn key
CFP-Notes.txt:99:Lazzaro, N. (2004). Why we play games: Four keys to more emotion without story. Retrieved from:
notes.txt:3:- Password: F@ll2022!
wmic.txt:54:  wmic computersystem get username
wmic.txt:67:  wmic netlogin get name,badpasswordcount
wmic.txt:69:Are the screensavers password protected? What is the timeout? good use: see that all systems are
complying with policy evil use: find systems to walk up and use (assuming physical access is an option)
wmic.txt:83:  wmic netuse get Name,username,connectiontype,localname
```
Here the output tells us the file name and line number where a match was found.

## Helpful Directories to Check
- Looking in a Users `\AppData\` folder is a great place to start. Many applications store `configuration files`, `temp saves` of documents, and more.
- A Users home folder `C:\Users\User\` is a common storage place; things like VPN keys, SSH keys, and more are stored. Typically in `hidden` folders. (`Get-ChildItem -Hidden`)
- The Console History files kept by the host are an endless well of information, especially if you land on an administrator's host. You can check two different points:
    - `C:\Users\<USERNAME>\AppData\Roaming\Microsoft\Windows\Powershell\PSReadline\ConsoleHost_history.txt`
    - `Get-Content (Get-PSReadlineOption).HistorySavePath`
- Checking a user's clipboard may also yield useful information. You can do so with `Get-Clipboard`
- Looking at Scheduled tasks can be helpful as well.

# Working with Services
_Scenario: Mr. Tanaka mentioned an issue with windows defender so we will investigate it._

`Get-Service`: displays all services that exist on the system, use arguments or other cmdlets to filter them.
```powershell-session
PS C:\htb> Get-Service | where DisplayName -like '*Defender*' | ft DisplayName,ServiceName,Status

DisplayName                                             ServiceName  Status
-----------                                             -----------  ------
Windows Defender Firewall                               mpssvc      Running
Windows Defender Advanced Threat Protection Service     Sense       Stopped
Microsoft Defender Antivirus Network Inspection Service WdNisSvc    Running
Microsoft Defender Antivirus Service                    WinDefend   Stopped
```
Here we can see that Microsoft Defender Antivirus Service is turned off. We need to turn it on using `Start-Service WinDefend` then check if it's running with `get-service WinDefend`.

While looking through services we noticed a service with an odd name called spooler so we need to stop it: 
```powershell-session
PS C:\htb> Stop-Service Spooler 

PS C:\htb> get-service spooler | Select-Object -Property Name, StartType, Status, DisplayName

Name    StartType  Status DisplayName
----    ---------  ------ -----------
spooler Automatic Stopped Totally still used for Print Spooling...


PS C:\htb> Set-Service -Name Spooler -StartType Disabled

PS C:\htb> Get-Service -Name Spooler | Select-Object -Property StartType 

StartType
---------
 Disabled
```
Here we stopped the service but to ensure it doesn't automatically start again we changed StartType to disabled.

Removing services in PowerShell is difficult right now. The cmdlet `Remove-Service` only works if you are using PowerShell version 7. For now, if you wish to remove a service and its entries, use the `sc.exe` tool.

```powershell-session
PS C:\htb> Get-Service -ComputerName ACADEMY-ICL-DC | Where-Object {$_.Status -eq "Running"}

Status   Name               DisplayName
------   ----               -----------
Running  ADWS               Active Directory Web Services
Running  BFE                Base Filtering Engine
Running  COMSysApp          COM+ System Application
Running  CoreMessagingRe... CoreMessaging
Running  CryptSvc           Cryptographic Services
Running  DcomLaunch         DCOM Server Process Launcher
Running  Dfs                DFS Namespace
Running  DFSR               DFS Replication
```
In this example we query running services on a remote host called `ACADEMY-ICL-DC`. In case we need to query to hosts `Invoke-Command` is useful:
```powershell-session
PS C:\htb> invoke-command -ComputerName ACADEMY-ICL-DC,LOCALHOST -ScriptBlock {Get-Service -Name 'windefend'}

Status   Name               DisplayName                            PSComputerName
------   ----               -----------                            --------------
Running  windefend          Microsoft Defender Antivirus Service   LOCALHOST
Running  windefend          Windows Defender Antivirus Service     ACADEMY-ICL-DC
```
- `Invoke-Command`: We are telling PowerShell that we want to run a command on a local or remote computer.
- `Computername`: We provide a comma-defined list of computer names to query.
- `ScriptBlock {commands to run}`: This portion is the enclosed command we want to run on the computer.

# Working with the Registry
[MITRE](https://attack.mitre.org/techniques/T1112/) provides many great examples of what a threat actor can do with access (locally or remotely) to a host's registry hive.  
For a detailed list of all Registry Hives and their supporting files within the OS, we can look [HERE](https://learn.microsoft.com/en-us/windows/win32/sysinfo/registry-hives).
## Registry Hives Examples

|**Name**|**Abbreviation**|**Description**|
|---|---|---|
|HKEY_LOCAL_MACHINE|`HKLM`|This subtree contains information about the computer's `physical state`, such as hardware and operating system data, bus types, memory, device drivers, and more.|
|HKEY_CURRENT_CONFIG|`HKCC`|This section contains records for the host's `current hardware profile`. (shows the variance between current and default setups) Think of this as a redirection of the [HKLM](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc739525\(v=ws.10\)) CurrentControlSet profile key.|
|HKEY_CLASSES_ROOT|`HKCR`|Filetype information, UI extensions, and backward compatibility settings are defined here.|
|HKEY_CURRENT_USER|`HKCU`|Value entries here define the specific OS and software settings for each specific user. `Roaming profile` settings, including user preferences, are stored under HKCU.|
|HKEY_USERS|`HKU`|The `default` User profile and current user configuration settings for the local computer are defined under HKU.|
## Searching through the registry
```powershell-session
PS C:\htb> Get-Item -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run | Select-Object -ExpandProperty Property  

SecurityHealth
RtkAudUService
WavesSvc
DisplayLinkTrayApp
LogiOptions
Acrobat Assistant 8.0
(default)
Focusrite Notifier
AdobeGCInvoker-1.0
```

```powershell-session
PS C:\htb> Get-ChildItem -Path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion -Recurse

Hive: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\App Paths
<SNIP>
Name                           Property
----                           --------
7zFM.exe                       (default) : C:\Program Files\7-Zip\7zFM.exe
                               Path      : C:\Program Files\7-Zip\
Acrobat.exe                    (default) : C:\Program Files\Adobe\Acrobat DC\Acrobat\Acrobat.exe
                               Path      : C:\Program Files\Adobe\Acrobat DC\Acrobat\
AcrobatInfo.exe                (default) : C:\Program Files\Adobe\Acrobat DC\Acrobat\AcrobatInfo.exe
                               Path      : C:\Program Files\Adobe\Acrobat DC\Acrobat\
AcroDist.exe                   Path      : C:\Program Files\Adobe\Acrobat DC\Acrobat\
                               (default) : C:\Program Files\Adobe\Acrobat DC\Acrobat\Acrodist.exe
Ahk2Exe.exe                    (default) : C:\Program Files\AutoHotkey\Compiler\Ahk2Exe.exe
AutoHotkey.exe                 (default) : C:\Program 
<SNIP>  
```

```powershell-session
PS C:\htb> Get-ItemProperty -Path Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run

SecurityHealth        : C:\Windows\system32\SecurityHealthSystray.exe
RtkAudUService        : "C:\Windows\System32\DriverStore\FileRepository\realtekservice.inf_amd64_85cff5320735903
                        d\RtkAudUService64.exe" -background
WavesSvc              : "C:\Windows\System32\DriverStore\FileRepository\wavesapo9de.inf_amd64_d350b8504310bbf5\W
                        avesSvc64.exe" -Jack
DisplayLinkTrayApp    : "C:\Program Files\DisplayLink Core Software\DisplayLinkTrayApp.exe" -basicMode
LogiOptions           : C:\Program Files\Logitech\LogiOptions\LogiOptions.exe /noui
Acrobat Assistant 8.0 : "C:\Program Files\Adobe\Acrobat DC\Acrobat\Acrotray.exe"
(default)             :
Focusrite Notifier    : "C:\Program Files\Focusriteusb\Focusrite Notifier.exe"
AdobeGCInvoker-1.0    : "C:\Program Files (x86)\Common Files\Adobe\AdobeGCClient\AGCInvokerUtility.exe"
PSPath                : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Curren
                        tVersion\Run
PSParentPath          : Microsoft.PowerShell.Core\Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Curren
                        tVersion
PSChildName           : Run
PSProvider            : Microsoft.PowerShell.Core\Registry
```
These are examples of commands used to search through the registry.

We can also query with a tool called `reg.exe`:
```powershell-session
PS C:\htb>  REG QUERY HKCU /F "Password" /t REG_SZ /S /K

HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\Winlogon\PasswordExpiryNotification
    NotShownErrorTime    REG_SZ    08::23::24, 2022/10/19
    NotShownErrorReason    REG_SZ    GetPwdResetInfoFailed

End of search: 2 match(es) found.
```
- `Reg query`: We are calling on Reg.exe and specifying that we want to query data.
- `HKCU`: This portion is setting the path to search. In this instance, we are looking in all of HKey_Current_User.
- `/f "password"`: /f sets the pattern we are searching for. In this instance, we are looking for "Password".
- `/t REG_SZ`: /t is setting the value type to search. If we do not specify, reg query will search through every type.
- `/s`: /s says to search through all subkeys and values recursively.
- `/k`: /k narrows it down to only searching through Key names.

## Creating, Modifying and Deleting Keys/Values
Here we can use `New-Item`, `Set-Item`, `New-ItemProperty`, and `Set-ItemProperty` or utilize `Reg.exe`.
Now imagine we gained access to the host and want an executable file to run next time the user logs in for persistence.
```powershell-session
PS C:\htb> New-Item -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\ -Name TestKey

    Hive: HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce

Name                           Property
----                           --------
TestKey   

PS C:\htb>  New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name  "access" -PropertyType String -Value "C:\Users\htb-student\Downloads\payload.exe"

access       : C:\Users\htb-student\Downloads\payload.exe
PSPath       : Microsoft.PowerShell.Core\Registry::HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\
               TestKey
PSParentPath : Microsoft.PowerShell.Core\Registry::HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
PSChildName  : TestKey
PSDrive      : HKCU
PSProvider   : Microsoft.PowerShell.Core\Registry
```
Using `New-Item` we created the key we want, then by using `New-ItemProperty` we created the value. We can verify this from the GUI here:  
<img width="952" height="570" alt="Pasted image 20250819183114" src="https://github.com/user-attachments/assets/a76321f7-5960-49d8-a064-94c469729f35" />

The same can be done in one move with `reg.exe`:
```powershell
reg add "HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce\TestKey" /v access /t REG_SZ /d "C:\Users\htb-student\Downloads\payload.exe"  
```
To remove:
```powershell-session
Remove-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce\TestKey -Name  "access"
```

# Working with the Windows Event Log
| Log Category     | Log Description                                                                                                                                                                                                     |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| System Log       | The system log contains events related to the Windows system and its components. A system-level event could be a service failing at startup.                                                                        |
| Security Log     | Self-explanatory; these include security-related events such as failed and successful logins, and file creation/deletion. These can be used to detect various types of attacks that we will cover in later modules. |
| Application Log  | This stores events related to any software/application installed on the system. For example, if Slack has trouble starting it will be recorded in this log.                                                         |
| Setup Log        | This log holds any events that are generated when the Windows operating system is installed. In a domain environment, events related to Active Directory will be recorded in this log on domain controller hosts.   |
| Forwarded Events | Logs that are forwarded from other hosts within the same network.                                                                                                                                                   |

| Type of Event | Event Description                                                                                                                                                                                                                                                                                                  |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Error         | Indicates a major problem, such as a service failing to load during startup, has occurred.                                                                                                                                                                                                                         |
| Warning       | A less significant log but one that may indicate a possible problem in the future. One example is low disk space. A Warning event will be logged to note that a problem may occur down the road. A Warning event is typically when an application can recover from the event without losing functionality or data. |
| Information   | Recorded upon the successful operation of an application, driver, or service, such as when a network driver loads successfully. Typically not every desktop application will log an event each time they start, as this could lead to a considerable amount of extra "noise" in the logs.                          |
| Success Audit | Recorded when an audited security access attempt is successful, such as when a user logs on to a system.                                                                                                                                                                                                           |
| Failure Audit | Recorded when an audited security access attempt fails, such as when a user attempts to log in but types their password in wrong. Many audit failure events could indicate an attack, such as Password Spraying.                                                                                                   |

| Event Severity Level | Level # | Description                                                                                                                                                                                    |
| -------------------- | ------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Verbose              | 5       | Progress or success messages.                                                                                                                                                                  |
| Information          | 4       | An event that occurred on the system but did not cause any issues.                                                                                                                             |
| Warning              | 3       | A potential problem that a sysadmin should dig into.                                                                                                                                           |
| Error                | 2       | An issue related to the system or service that does not require immediate attention.                                                                                                           |
| Critical             | 1       | This indicates a significant issue related to an application or a system that requires urgent attention by a sysadmin that, if not addressed, could lead to system or application instability. |
All event logs are stored in a standard format and include the following elements:
- `Log name`: As discussed above, the name of the event log where the events will be written. By default, events are logged for `system`, `security`, and `applications`.
- `Event date/time`: Date and time when the event occurred
- `Task Category`: The type of recorded event log
- `Event ID`: A unique identifier for sysadmins to identify a specific logged event
- `Source`: Where the log originated from, typically the name of a program or software application
- `Level`: Severity level of the event. This can be information, error, verbose, warning, critical
- `User`: Username of who logged onto the host when the event occurred
- `Computer`: Name of the computer where the event is logged

The Windows Event Log is handled by the `EventLog` services, managed by `svchost.exe`. By default, Windows Event Logs are stored in `C:\Windows\System32\winevt\logs` with the file extension `.evtx`.

## Interacting With Event Log - wevtutil
```cmd-session
C:\htb> wevtutil /?

<SNIP>
Commands:

el | enum-logs          List log names.
gl | get-log            Get log configuration information.
sl | set-log            Modify configuration of a log.
ep | enum-publishers    List event publishers.
gp | get-publisher      Get publisher configuration information.
im | install-manifest   Install event publishers and logs from manifest.
um | uninstall-manifest Uninstall event publishers and logs from manifest.
qe | query-events       Query events from a log or log file.
gli | get-log-info      Get log status information.
epl | export-log        Export a log.
al | archive-log        Archive an exported log.
cl | clear-log          Clear a log.

<SNIP>
```

```cmd-session
C:\htb> wevtutil gl "Windows PowerShell"

name: Windows PowerShell
enabled: true
type: Admin
owningPublisher:
isolation: Application
channelAccess: O:BAG:SYD:(A;;0x2;;;S-1-15-2-1)(A;;0x2;;;S-1-15-3-1024-3153509613-960666767-3724611135-2725662640-12138253-543910227-1950414635-4190290187)(A;;0xf0007;;;SY)(A;;0x7;;;BA)(A;;0x7;;;SO)(A;;0x3;;;IU)(A;;0x3;;;SU)(A;;0x3;;;S-1-5-3)(A;;0x3;;;S-1-5-33)(A;;0x1;;;S-1-5-32-573)
logging:
  logFileName: %SystemRoot%\System32\Winevt\Logs\Windows PowerShell.evtx
  retention: false
  autoBackup: false
  maxSize: 15728640
publishing:
  fileMax: 1
```
We can also use `wevtutil gli <LogName>` to display status info like creation time, last access and write times, file size and number of log records.

We can also query events within a log, example: `wevtutil qe Security /c:5 /rd:true /f:text`
- `Security` is the name of the log
- `/c:5` display 5 events
- `/rd:true` reverse direction is true which means show the most recent. if it was false it would show the oldest
- `/f: text` instead of the default which is XML
If we want to export a specific event we can do it like: `wevtutil epl System C:\system_export.evtx`
## Interacting with the Windows Event Log - Get-WinEvent
Get all logs: `Get-WinEvent -ListLog *`  
Get info about a log: `Get-WinEvent -ListLog <LogName>`  
Get last 5 logs: `Get-WinEvent -LogName <LogName> -MaxEvents 5 | Select-Object -ExpandProperty Message`. To get the oldest 5 add `-oldest` to the first cmdlet  

Using `-FilterHashTable` like `Get-WinEvent -FilterHashTable @{LogName='Security';ID='4625 '}` we can filter logs based on what interests us like name, id or severity level.

# Networking Management from The CLI
Standard protocols to know:

| **Protocol** | **Description**                                                                                                                                                                                                                                                                                                                                                                    |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SMB`        | [SMB](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-smb2/4287490c-602c-41c0-a23e-140a1f137832) provides Windows hosts with the capability to share resources, files, and a standard way of authenticating between hosts to determine if access to resources is allowed. For other distros, SAMBA is the open-source option.                                     |
| `Netbios`    | [NetBios](https://www.ietf.org/rfc/rfc1001.txt) itself isn't directly a service or protocol but a connection and conversation mechanism widely used in networks. It was the original transport mechanism for SMB, but that has since changed. Now it serves as an alternate identification mechanism when DNS fails. Can also be known as NBT-NS (NetBIOS name service).           |
| `LDAP`       | [LDAP](https://www.rfc-editor.org/rfc/rfc4511) is an `open-source` cross-platform protocol used for `authentication` and `authorization` with various directory services. This is how many different devices in modern networks can communicate with large directory structure services such as `Active Directory`.                                                                |
| `LLMNR`      | [LLMNR](https://www.rfc-editor.org/rfc/rfc4795) provides a name resolution service based on DNS and works if DNS is not available or functioning. This protocol is a multicast protocol and, as such, works only on local links (within a normal broadcast domain, not across layer three links).                                                                                  |
| `DNS`        | [DNS](https://datatracker.ietf.org/doc/html/rfc1034) is a common naming standard used across the Internet and in most modern network types. DNS allows us to reference hosts by a unique name instead of their IP address. This is how we can reference a website by "WWW.google.com" instead of "8.8.8.8". Internally this is how we request resources and access from a network. |
| `HTTP/HTTPS` | [HTTP/S](https://www.rfc-editor.org/rfc/rfc2818) HTTP and HTTPS are the insecure and secure way we request and utilize resources over the Internet. These protocols are used to access and utilize resources such as web servers, send and receive data from remote sources, and much more.                                                                                        |
| `Kerberos`   | [Kerberos](https://web.mit.edu/kerberos/) is a network level authentication protocol. In modern times, we are most likely to see it when dealing with Active Directory authentication when clients request tickets for authorization to use domain resources.                                                                                                                      |
| `WinRM`      | [WinRM](https://learn.microsoft.com/en-us/windows/win32/winrm/portal) Is an implementation of the WS-Management protocol. It can be used to manage the hardware and software functionalities of hosts. It is mainly used in IT administration but can also be used for host enumeration and as a scripting engine.                                                                 |
| `RDP`        | [RDP](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/rds-plan-access-from-anywhere) is a Windows implementation of a network UI services protocol that provides users with a Graphical interface to access hosts over a network connection. This allows for full UI use to include the passing of keyboard and mouse input to the remote host.    |
| `SSH`        | [SSH](https://datatracker.ietf.org/doc/html/rfc4251) is a secure protocol that can be used for secure host access, transfer of files, and general communication between network hosts. It provides a way to securely access hosts and services over insecure networks.                                                                                                             |
## Querying Networking Settings
`ipconfig` gives us basic network information like Domain Name, IPv4 Address, Subnet Mask, and Default Gateway. To get additional info use `ipconfig /all`.

The `arp -a` command displays current entries within the Address Resolution Protocol (ARP) cache. This information is useful in determining what hosts our target was communicating with. 

```powershell-session
PS C:\htb> nslookup ACADEMY-ICL-DC

DNS request timed out.
    timeout was 2 seconds.
Server:  UnKnown
Address:  172.16.5.155

Name:    ACADEMY-ICL-DC.greenhorn.corp
Address:  172.16.5.155
```
Here we used `nslookup` to get the ip address of the domain name `ACADEMY-ICL-DC`, this varifies that DNS is working properly.

To look through ports on our host use: `netstat`. You can add `-a` to view them all and `-n` to use numerical form instead of names.

## PowerShell Network cmdlets
Examples:

|**Cmdlet**|**Description**|
|---|---|
|`Get-NetIPInterface`|Retrieve all `visible` network adapter `properties`.|
|`Get-NetIPAddress`|Retrieves the `IP configurations` of each adapter. Similar to `IPConfig`.|
|`Get-NetNeighbor`|Retrieves the `neighbor entries` from the cache. Similar to `arp -a`.|
|`Get-Netroute`|Will print the current `route table`. Similar to `IPRoute`.|
|`Set-NetAdapter`|Set basic adapter properties at the `Layer-2` level such as VLAN id, description, and MAC-Address.|
|`Set-NetIPInterface`|Modifies the `settings` of an `interface` to include DHCP status, MTU, and other metrics.|
|`New-NetIPAddress`|Creates and configures an `IP address`.|
|`Set-NetIPAddress`|Modifies the `configuration` of a network adapter.|
|`Disable-NetAdapter`|Used to `disable` network adapter interfaces.|
|`Enable-NetAdapter`|Used to turn network adapters back on and `allow` network connections.|
|`Restart-NetAdapter`|Used to restart an adapter. It can be useful to help push `changes` made to adapter `settings`.|
|`test-NetConnection`|Allows for `diagnostic` checks to be ran on a connection. It supports ping, tcp, route tracing, and more.|
`Get-NetIPInterface` displays all interfaces and some info about each of them like ifIndex, InterfaceAlias, AddressFamily (IPv4 or IPv6), DHCP, etc. ifIndex and InterfaceAlias are useful since we can use them with other commands to identify the interface. For example:
```powershell-session
PS C:\htb> Get-NetIPAddress -ifIndex 25

IPAddress         : fe80::a0fc:2e3d:c92a:48df%25
InterfaceIndex    : 25
InterfaceAlias    : Wi-Fi
AddressFamily     : IPv6
Type              : Unicast
PrefixLength      : 64
PrefixOrigin      : WellKnown
SuffixOrigin      : Link
AddressState      : Preferred
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 192.168.86.211
InterfaceIndex    : 25
InterfaceAlias    : Wi-Fi
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Dhcp
SuffixOrigin      : Dhcp
AddressState      : Preferred
ValidLifetime     : 21:35:36
PreferredLifetime : 21:35:36
SkipAsSource      : False
PolicyStore       : ActiveStore
```

To edit the settings of an interface we can use `Set-NetIPInterface` like: 
```powershell-session
PS C:\htb> Set-NetIPInterface -InterfaceIndex 25 -Dhcp Disabled
```
After disabling DHCP we can set our own IP address using:
```powershell-session
PS C:\htb> Set-NetIPAddress -InterfaceIndex 25 -IPAddress 10.10.100.54 -PrefixLength 24
```
Other useful networking commands include `Restart-NetAdapter` and `Test-NetConnection`

## Remote Access - SSH
First we need to verify it's enabled:
```powershell-session
PS C:\Users\htb-student> Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : NotPresent
```
We need to install the server:
```powershell-session
PS C:\Users\htb-student> Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

Path          :
Online        : True
RestartNeeded : False

PS C:\Users\htb-student> Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : Installed
```
Once it's installed we start the service with `Start-Service sshd` and set its start type to automatic with `Set-Service`.

## Remote Access - WinRM
Enable Windows Remote Management (WinRM): `winrm quickconfig`  
Recommended Security Hardening options:
- Configure TrustedHosts to include just IP addresses/hostnames that will be used for remote management
- Configure HTTPS for transport
- Join Windows systems to an Active Directory Domain Environment and Enforce Kerberos Authentication
To test access: `Test-WSMan -ComputerName "<RemoteComputerIPAddress>"` we can also test if WinRM is authenticated by adding `-Authentication Negotiate`.

## Remote Access - Remote PowerShell Session
`Enter-PSSession` establishes a PowerShell session on a remote windows target. Command: `Enter-PSSession -ComputerName <RemoteHostIPAddress> -Credential <UserName> -Authentication Negotiate`

This also works from Linux machines since we can install PS on them and run `Enter-PSSession` from there.

# Interacting With The Web
`Invoke-WebRequest -Uri "<URI>" -Method GET` Allows us to get the web page that has the specified URI. After that we can filter for raw content or images using:
```powershell-session
PS C:\htb> Invoke-WebRequest -Uri "https://web.ics.purdue.edu/~gchopra/class/public/pages/webdesign/05_simple.html" -Method GET | fl Images

Images : {@{innerHTML=; innerText=; outerHTML=<IMG alt="Pretty Picture"
         src="example/prettypicture.jpg">; outerText=; tagName=IMG; alt=Pretty Picture;
         src=example/prettypicture.jpg}, @{innerHTML=; innerText=; outerHTML=<IMG alt="Pretty
         Picture" src="example/prettypicture.jpg" align=top>; outerText=; tagName=IMG; alt=Pretty
         Picture; src=example/prettypicture.jpg; align=top}}
```

We can also use it to download files from the web like:
```powershell-session
PS C:\> Invoke-WebRequest -Uri "https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1" -OutFile "C:\PowerView.ps1"
```

## Transferring Files Through Network
If we have a file on our attack host (assume its IP address is: 10.10.14.169) that we want to transfer to the target we can first set up a python web server on the attack host:
```shell-session
AnasSalhen@htb[/htb]$ python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```
On the target host run:
```powershell-session
Invoke-WebRequest -Uri "http://10.10.14.169:8000/PowerView.ps1" -OutFile "C:\PowerView.ps1"
```

## Other Methods
If we need to download something and `Invoke-WebRequest` can't be used for some reason we can use [.Net.WebClient](https://learn.microsoft.com/en-us/dotnet/api/system.net.webclient?view=net-7.0). Here is an example:
```powershell-session
PS C:\htb> (New-Object Net.WebClient).DownloadFile("https://github.com/BloodHoundAD/BloodHound/releases/download/4.2.0/BloodHound-win32-x64.zip", "Bloodhound.zip")
```

# PowerShell Scripting and Automation
File extensions related to modules:

|**Extension**|**Description**|
|---|---|
|ps1|The `*.ps1` file extension represents executable PowerShell scripts.|
|psm1|The `*.psm1` file extension represents a PowerShell module file. It defines what the module is and what is contained within it.|
|psd1|The `*.psd1` is a PowerShell data file detailing the contents of a PowerShell module in a table of key/value pairs.|
## Module Components
1. A `directory` containing all the required files and content, saved somewhere within `$env:PSModulePath`.
	- This is done so that when you attempt to import it into your PowerShell session or Profile, it can be automatically found instead of having to specify where it is.
2. A `manifest` file listing all files and pertinent information about the module and its function.
	- This could include associated scripts, dependencies, the author, example usage, etc.
3. Some code file - usually either a PowerShell script (`.ps1`) or a (`.psm1`) module file that contains our script functions and other information.
4. Other resources the module needs, such as help files, scripts, and other supporting documents.

## Preparing the Module
We will create a module that performs some routine tasks. First the directory:
```powershell-session
PS C:\htb> mkdir quick-recon  

    Directory: C:\Users\MTanaka\Documents\WindowsPowerShell\Modules


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/31/2022   7:38 AM                quick-recon
```

Then the manifest which describes:
- `Metadata` about the module, such as the module version number, the author, and the description.
- `Prerequisites` needed to import the module, such as the Windows PowerShell version, the common language runtime (CLR) version, and the required modules.
- `Processing` directives, such as the scripts, formats, and types to process.
- `Restrictions` on the module members to export, such as the aliases, functions, variables, and cmdlets to export.
In our case we can use:
```powershell-session
PS C:\htb> New-ModuleManifest -Path C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon\quick-recon.psd1 -PassThru

# Module manifest for module 'quick-recon'
#
# Generated by: MTanaka
#
# Generated on: 10/31/2022
#

@{

# Script module or binary module file associated with this manifest.
# RootModule = ''

# Version number of this module.
ModuleVersion = '1.0'

<SNIP>
```
This command creates the manifest at the path we specified and populates the file with default data. `-PassThru` allows us to see what is being printed in the file and on the console. Most lines in the manifest files are optional except for the `ModuleVersion` line. If we were to complete the file it would look something like this:
```powershell
# Module manifest for module 'quick-recon'
#
# Generated by: MTanaka
#
# Generated on: 10/31/2022
#

@{

# Script module or binary module file associated with this manifest.
# RootModule = 'C:\Users\MTanaka\WindowsPowerShell\Modules\quick-recon\quick-recon.psm1'

# Version number of this module.
ModuleVersion = '1.0'

# ID used to uniquely identify this module
GUID = '0a062bb1-8a1b-4bdb-86ed-5adbe1071d2f'

# Author of this module
Author = 'MTanaka'

# Company or vendor of this module
CompanyName = 'Greenhorn.Corp.'

# Copyright statement for this module
Copyright = '(c) 2022 Greenhorn.Corp. All rights reserved.'

# Description of the functionality provided by this module
Description = 'This module will perform several quick checks against the host for Reconnaissance of key information.'

# Functions to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no functions to export.
FunctionsToExport = @()

# Cmdlets to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no cmdlets to export.
CmdletsToExport = @()

# Variables to export from this module
VariablesToExport = '*'

# Aliases to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no aliases to export.
AliasesToExport = @()

# List of all modules packaged with this module
# ModuleList = @()

# List of all files packaged with this module
# FileList = @()  
}
```

## Creating the Script
First we create the file: `New-Item quick-recon.psm1 -ItemType File`  
Now we start writing the script. First we need to ensure that any module we will use is imported. Since most tools are already built-in into PowerShell we just need to import the AD module.
```powershell
Import-Module ActiveDirectory 
```
We need this module to achieve 4 main things:
- Retrieve the host ComputerName
- Retrieve the hosts IP configuration
- Retrieve basic domain information
- Retrieve an output of the "C:\Users" directory
So we make 4 variables to store each one. We get the hostname from `$env:ComputerName` and store it in this `$Hostname` variable. We can get basic ip info from the command `ipconfig` and store it in `$IP`. Basic domain info from `Get-ADDomain` will be stored in `$Domain`. Finally store the output from `Get-ChildItem C:\Users\` in `$Users`.
```powershell
$Hostname = $env:ComputerName
$IP = ipconfig 
$Domain = Get-ADDomain  
$Users = Get-ChildItem C:\Users\ 
```
Now we will take this output and put it in a file using `New-Item` and `Add-Content`. To make things easier we will move variables and commands to a function and call it `Get-Recon`.
```powershell
function Get-Recon {  

    $Hostname = $env:ComputerName  

    $IP = ipconfig

    $Domain = Get-ADDomain 

    $Users = Get-ChildItem C:\Users\

    new-Item ~\Desktop\recon.txt -ItemType File 

    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users

    Add-Content ~\Desktop\recon.txt $Vars
  } 
```
We need to add comments to our script. Use `#` for single line comments and use `<#  #>` to wrap several lines in one large comment. 
```powershell
function Get-Recon {  
    # Collect the hostname of our PC.
    $Hostname = $env:ComputerName  
    # Collect the IP configuration.
    $IP = ipconfig
    # Collect basic domain information.
    $Domain = Get-ADDomain 
    # Output the users who have logged in and built out a basic directory structure in "C:\Users\".
    $Users = Get-ChildItem C:\Users\
    # Create a new file to place our recon results in.
    new-Item ~\Desktop\recon.txt -ItemType File 
    # A variable to hold the results of our other variables. 
    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users
    # It does the thing 
    Add-Content ~\Desktop\recon.txt $Vars
  } 
```
We now need to build the help section that will be displayed when calling `Get-Help`. When it comes to placement, we have two options here. If we wish to place it within the function, it must be at the beginning of the function, right after the opening line for the function, or at the end of the function, one line after the last action of the function. If we place it within the script but outside of the function itself, we must place it above our function with no more than one line between the help and function.
```powershell
<# 
.Description  
This function performs some simple recon tasks for the user. We import the module and issue the 'Get-Recon' command to retrieve our output. Each variable and line within the function and script are commented for our understanding. Right now, this module will only work on the local host from which you run it, and the output will be sent to a file named 'recon.txt' on the Desktop of the user who opened the shell. Remote Recon functions are coming soon!  

.Example  
After importing the module run "Get-Recon"
'Get-Recon


    Directory: C:\Users\MTanaka\Desktop


Mode                 LastWriteTime         Length Name                                                                                                                                        
----                 -------------         ------ ----                                                                                                                                        
-a----         11/3/2022  12:46 PM              0 recon.txt '

.Notes  
Remote Recon functions coming soon! This script serves as our initial introduction to writing functions and scripts and making PowerShell modules.  

#>

function Get-Recon {  
<SNIP>  
```
Here we used keywords which are helpful in organizing text blocks. The syntax for a keyword is `.<keyword>`. We can view all available keywords from [here](https://learn.microsoft.com/en-us/powershell/scripting/developer/help/comment-based-help-keywords?view=powershell-7.2).  

We need to control what gets exported (can be called) from this script. By default all functions, aliases and variables are exported. Once we write `Export-ModuleMember` in the script nothing is exported. Then if we want to export something we add it to the command.
```powershell
Export-ModuleMember -Function Get-Recon -Variable Hostname  
```
So here we exported the `Get-Recon` function and the `Hostname` variable. We can export everything using `*` (like `-Function *` for all functions).
### Scopes

| **Scope** | **Description**                                                                                                                                                                                                                                                   |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Global    | This is the default scope level for PowerShell. It affects all objects that exist when PowerShell starts, or a new session is opened. Any variables, aliases, functions, and anything you specify in your PowerShell profile will be created in the Global scope. |
| Local     | This is the current scope you are operating in. This could be any of the default scopes or child scopes that are made.                                                                                                                                            |
| Script    | This is a temporary scope that applies to any scripts being run. It only applies to the script and its contents. Other scripts and anything outside of it will not know it exists. To the script, Its scope is the local scope.                                   |
This is a brief explanation for scope levels, more details can be found [here](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_scopes?view=powershell-7.2).

## Final Script
```powershell
import-module ActiveDirectory

<# 
.Description  
This function performs some simple recon tasks for the user. We import the module and then issue the 'Get-Recon' command to retrieve our output. Each variable and line within the function and script are commented for your understanding. Right now, this only works on the local host from which you run it, and the output will be sent to a file named 'recon.txt' on the Desktop of the user who opened the shell. Remote Recon functions coming soon!  

.Example  
After importing the module run "Get-Recon"
'Get-Recon


    Directory: C:\Users\MTanaka\Desktop


Mode                 LastWriteTime         Length Name                                                                                                                                        
----                 -------------         ------ ----                                                                                                                                        
-a----         11/3/2022  12:46 PM              0 recon.txt '

.Notes  
Remote Recon functions coming soon! This script serves as our initial introduction to writing functions and scripts and making PowerShell modules.  

#>
function Get-Recon {  
    # Collect the hostname of our PC
    $Hostname = $env:ComputerName  
    # Collect the IP configuration
    $IP = ipconfig
    # Collect basic domain information
    $Domain = Get-ADDomain 
    # Output the users who have logged in and built out a basic directory structure in "C:\Users"
    $Users = Get-ChildItem C:\Users\
    # Create a new file to place our recon results in
    new-Item ~\Desktop\recon.txt -ItemType File 
    # A variable to hold the results of our other variables 
    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users
    # It does the thing 
    Add-Content ~\Desktop\recon.txt $Vars
  } 

Export-ModuleMember -Function Get-Recon -Variable Hostname 
```
Now we have completed our module and we can use the command `Get-Recon` after importing the module `quick-recon`.

# More Sources
- [Starting Point](https://app.hackthebox.com/starting-point)
- [Ippsec's site](https://ippsec.rocks/?#)
- [APT's Love PowerShell, You Should Too](https://youtu.be/GhfiNTsxqxA)
- [PowerShell For Pentesting](https://youtu.be/jU1Pz641zjM)
- [PowerShell & Under The Wire](https://youtu.be/864S16g_SQs)
- [Blue](https://www.youtube.com/watch?v=YRsfX6DW10E&t=38s)
- [Support](https://app.hackthebox.com/machines/Support)
- [Return](https://0xdf.gitlab.io/2022/05/05/htb-return.html)
- [Starting Point](https://app.hackthebox.com/starting-point)
- [0xdf's walkthroughs](https://0xdf.gitlab.io/tags.html#active-directory)
- [Microsofts Training Documentation](https://docs.microsoft.com/en-us/training/modules/introduction-to-powershell/)
- [Black Hills Information Security](https://www.blackhillsinfosec.com/?s=Powershell)
- [SANS](https://www.sans.org/blog/getting-started-with-powershell/)
	- [PowerShell in Pentesting](https://www.sans.org/webcasts/powershell-pentesting-108305/
