Last Update: 2025-07-10 2:00 PM
# Prompt Description
The prompt looks like this:
```shell-session
<username>@<hostname><current working directory>$
```
The home directory of a user is the default and it's marked with `~`.
```shell-session
<username>@<hostname>[~]$
```
The dollar sign, in this case, stands for a user. As soon as we log in as `root`, the character changes to a `hash` `#` and looks like this:
```shell-session
root@htb[/htb]#
```
The `PS1` variable in Linux systems controls how your command prompt looks in the terminal. It allows adding useful info like date and time. This helps when using tools like `script` or reviewing the `.bash_history` file to see commands entered with time and date which helps later in documentation.
In the shell’s configuration file (`.bashrc` for the Bash shell) we can use some characters to display certain info like:

|**Special Character**|**Description**|
|---|---|
|`\d`|Date (Mon Feb 6)|
|`\D{%Y-%m-%d}`|Date (YYYY-MM-DD)|
|`\H`|Full hostname|
|`\j`|Number of jobs managed by the shell|
|`\n`|Newline|
|`\r`|Carriage return|
|`\s`|Name of the shell|
|`\t`|Current time 24-hour (HH:MM:SS)|
|`\T`|Current time 12-hour (HH:MM:SS)|
|`\@`|Current time|
|`\u`|Current username|
|`\w`|Full path of the current working directory|
[Bash-prompt-generator](https://bash-prompt-generator.org/) and [powerline](https://github.com/powerline/powerline) give us the ability to adapt our prompt to our needs.

# Getting Help
To write a comment use `# (comment here)`.

`ls` is a command to list files and directories within the current folder or any specified directory. `man (tool/command)` displays the manual page for the given command (i.e. `man ls`).

`(tool/command) --help` shows arguments for that command (i.e. `ls --help`). Some commands like `curl` use a short version (`curl -h`).

Each manual page has a short description, `apropos (keyword)` searches these descriptions for instances of the given keyword.

[https://explainshell.com/](https://explainshell.com/) is helpful for understanding commands.

# Gathering System Info
| **Command** | **Description**                                                                                                                    |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `whoami`    | Displays current username.                                                                                                         |
| `id`        | Returns users identity                                                                                                             |
| `hostname`  | Sets or prints the name of current host system.                                                                                    |
| `uname`     | Prints basic information about the operating system name and system hardware.                                                      |
| `pwd`       | Returns working directory name.                                                                                                    |
| `ifconfig`  | The ifconfig utility is used to assign or to view an address to a network interface and/or configure network interface parameters. |
| `ip`        | Ip is a utility to show or manipulate routing, network devices, interfaces and tunnels.                                            |
| `netstat`   | Shows network status.                                                                                                              |
| `ss`        | Another utility to investigate sockets.                                                                                            |
| `ps`        | Shows process status.                                                                                                              |
| `who`       | Displays who is logged in.                                                                                                         |
| `env`       | Prints environment or sets and executes command.                                                                                   |
| `lsblk`     | Lists block devices.                                                                                                               |
| `lsusb`     | Lists USB devices                                                                                                                  |
| `lsof`      | Lists opened files.                                                                                                                |
| `lspci`     | Lists PCI devices.                                                                                                                 |

Secure Shell (SSH) is a protocol that allows executing commands on remote computers. To log in, use: 
````shell-session
ssh username@[IP address]
````

## id
When using the `id` command the result may look like:
```shell-session
username@htb[/htb]$ id

uid=1000(username) gid=1000(username) groups=1000(username),1337(hackthebox),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),116(lpadmin),126(sambashare)
```
This output has an identifier for the user (uid), his primary group (gid) and the groups he is a member of. These groups determine access and permissions. 
For example, `adm` group means the user can read files in `/var/log` and access sensitive info.
`sudo` means user can run some or all commands as the all-powerful root user which is helpful in privilege escalation.

There is also the `uname` command which provides valuable info about the system itself.
