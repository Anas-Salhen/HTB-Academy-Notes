# Windows Sessions
## Interactive
An interactive or local logon session is when a user uses their username and password to access the system whether physically or remotely.

## Non-interactive
Non-interactive accounts aren't used by users to login. Instead, they are used by the OS to run background services, processes, or scheduled tasks automatically.

Important non-interactive accounts include:

|Account|Description|
|---|---|
|Local System Account|Also known as the `NT AUTHORITY\SYSTEM` account, this is the most powerful account in Windows systems. It is used for a variety of OS-related tasks, such as starting Windows services. This account is more powerful than accounts in the local administrators group.|
|Local Service Account|Known as the `NT AUTHORITY\LocalService` account, this is a less privileged version of the SYSTEM account and has similar privileges to a local user account. It is granted limited functionality and can start some services.|
|Network Service Account|This is known as the `NT AUTHORITY\NetworkService` account and is similar to a standard domain user account. It has similar privileges to the Local Service Account on the local machine. It can establish authenticated sessions for certain network services.|

# Interacting with the Windows Operating System
## Graphical User Interface
The concept of a GUI was introduced in the 1970s by the Xerox Palo Alto research laboratory. It was then added to Apple and Microsoft OSs to make everyday users able to access computers without having to interact with the command-line.

## Remote Desktop Protocol 
[RDP](https://support.microsoft.com/en-us/help/186607/understanding-the-remote-desktop-protocol-rdp) is a proprietary Microsoft protocol which allows a user to connect to a remote system over a network connection and obtain a graphical user interface. The user connects using RDP client software to a target system running RDP server software. It uses port 3389.

## Windows Command Line
It gives users greater control over their systems and can be used to automate some tasks. In windows the 2 main ways to interact with the command line are via the Command Prompt (CMD) and PowerShell.

The [Windows Command Reference](https://download.microsoft.com/download/5/8/9/58911986-D4AD-4695-BF63-F734CD4DF8F2/ws-commands.pdf) from Microsoft is a comprehensive A-Z command reference which includes an overview, usage examples, and command syntax for most Windows commands, and familiarity with it is recommended.

## CMD
`cmd.exe` is used to execute commands. It can be opened from the Start Menu, by typing `cmd` in the run dialogue box, or by directly launching the binary from `C:\Windows\system32\cmd.exe`. After launching it we can type `help` to see a list of available commands. For more info about a specific command type: `help <command>`. Some commands have their own help menus, accessed by typing `<command> /?`.

## PowerShell
It's a command shell designed by Microsoft to be more geared towards system admins. It's built on top of the `.NET` framework.  

PowerShell has a help system for cmdlets, functions, scripts, and concepts. This is not installed by default, but we can either run the `Get-Help <cmdlet-name> -Online` command to open the online help for a cmdlet or function in our web browser. We can type `Update-Help` to download and install help files locally. Typing a command such as `Get-Help Get-AppPackage` will return just the partial help unless the Help files are installed.

## cmdlets
PowerShell utilizes cmdlets, which are small single-function tools built into the shell. They are in the form `Verb-Noun`. For example, `Get-ChildItem` can be used to list items in our current directory. We can type `Get-ChildItem -` and hit the tab key to iterate through its arguments. We can also combine more than one argument to do more than one thing.

## Aliases
View all available aliases using `Get-Alias` and view which command is linked to a specific alias using `Get-Alias -Name <alias>`. We can also set up new aliases with `New-Alias -Name <alias> <command>`.

## Running Scripts
The PowerShell ISE (Integrated Scripting Environment) allows users to write PowerShell scripts on the fly. It also has an autocomplete/lookup function for PowerShell commands. The PowerShell ISE allows us to write and run scripts in the same console, which allows for quick debugging. We can run PowerShell scripts in a variety of ways. If we know the functions, we can run the script either locally or after loading into memory with a download cradle.

One common way to work with a script in PowerShell is to import it so that all functions are then available within our current PowerShell console session: `Import-Module <script>`. We can then either start a command and cycle through the options or type `Get-Module` to list all loaded modules and their associated commands.

## Execution Policy
| **Policy**     | **Description**                                                                                                                                                                                                                                                  |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `AllSigned`    | All scripts can run, but a trusted publisher must sign scripts and configuration files. This includes both remote and local scripts. We receive a prompt before running scripts signed by publishers that we have not yet listed as either trusted or untrusted. |
| `Bypass`       | No scripts or configuration files are blocked, and the user receives no warnings or prompts.                                                                                                                                                                     |
| `Default`      | This sets the default execution policy, `Restricted` for Windows desktop machines and `RemoteSigned` for Windows servers.                                                                                                                                        |
| `RemoteSigned` | Scripts can run but requires a digital signature on scripts that are downloaded from the internet. Digital signatures are not required for scripts that are written locally.                                                                                     |
| `Restricted`   | This allows individual commands but does not allow scripts to be run. All script file types, including configuration files (`.ps1xml`), module script files (`.psm1`), and PowerShell profiles (`.ps1`) are blocked.                                             |
| `Undefined`    | No execution policy is set for the current scope. If the execution policy for ALL scopes is set to undefined, then the default execution policy of `Restricted` will be used.                                                                                    |
| `Unrestricted` | This is the default execution policy for non-Windows computers, and it cannot be changed. This policy allows for unsigned scripts to be run but warns the user before running scripts that are not from the local intranet zone.                                 |
The execution policy is not meant to be a security control that restricts user actions. A user can easily bypass the policy by either typing the script contents directly into the PowerShell window, downloading and invoking the script, or specifying the script as an encoded command. It can also be bypassed by adjusting the execution policy (if the user has the proper rights) or setting the execution policy for the current process scope (which can be done by almost any user as it does not require a configuration change and will only be set for the duration of the user's session).

An example of changing the execution policy for the current process (session):
```powershell-session
PS C:\htb> Set-ExecutionPolicy Bypass -Scope Process

Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https://go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): Y
```
To view execution policy:
```powershell-session
PS C:\htb>  Get-ExecutionPolicy -List

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process          Bypass
  CurrentUser       Undefined
 LocalMachine    RemoteSigned
```

# Windows Management Instrumentation (WMI)
WMI is a subsystem of PowerShell that provides sysadmins with powerful tools for system monitoring. It is made up of the following components:

|**Component Name**|**Description**|
|---|---|
|WMI service|The Windows Management Instrumentation process, which runs automatically at boot and acts as an intermediary between WMI providers, the WMI repository, and managing applications.|
|Managed objects|Any logical or physical components that can be managed by WMI.|
|WMI providers|Objects that monitor events/data related to a specific object.|
|Classes|These are used by the WMI providers to pass data to the WMI service.|
|Methods|These are attached to classes and allow actions to be performed. For example, methods can be used to start/stop processes on remote machines.|
|WMI repository|A database that stores all static data related to WMI.|
|CIM Object Manager|The system that requests data from WMI providers and returns it to the application requesting it.|
|WMI API|Enables applications to access the WMI infrastructure.|
|WMI Consumer|Sends queries to objects via the CIM Object Manager.|
Some of WMI uses:
- Status information for local/remote systems
- Configuring security settings on remote machines/applications
- Setting and changing user and group permissions
- Setting/modifying system properties
- Code execution
- Scheduling processes
- Setting up logging

## WMIC
These tasks can all be performed using a combination of PowerShell and the WMI Command-Line Interface (WMIC). To use WMIC type `wmic` in the command prompt to open an interactive shell or run the command you want directly via `wmic <command>`. You can view a list of commands using `wmic /?`.

### Syntax
`wmic <Alias> <Verb> <Adverb> <Switches>`

| Part         | Meaning                    | Example                        |
| ------------ | -------------------------- | ------------------------------ |
| **Alias**    | Shortcut for a WMI class   | `os`, `useraccount`, `process` |
| **Verb**     | What you want to do        | `get`, `list`, `call`          |
| **Adverb**   | Formatting or detail level | `brief`, `full`, `status`      |
| **Switches** | Optional settings          | `/format`, `/node`, etc.       |
Verbs:
- `get`: Retrieve specific properties
- `list`: Retrieve full or formatted listings
- `call`: Call methods (e.g. start, terminate, shutdown)
Adverbs:
- `brief`: Minimal fields
- `full`: All available fields
- `status`: Status-related info

An in-depth listing of verbs, switches, and adverbs is available [here](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmic).  
WMI can be used with PowerShell by using the `Get-WmiObject` [module](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-wmiobject?view=powershell-5.1). This module is used to get instances of WMI classes or information about available classes. We can also use the `Invoke-WmiMethod` [module](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/invoke-wmimethod?view=powershell-5.1), which is used to call the methods of WMI objects.