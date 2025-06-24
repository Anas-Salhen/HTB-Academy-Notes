# Linux
Distributions for pentesting:
- ParrotOS
- Kali Linux
- BlackArch
- BackBox
## LVM
 Logical Volume Manager (LVM) is a partitioning scheme used in Unix and Linux to manage storage using Logical Volumes. It supports organizing logical volumes into what's called a RAID array to protect computers from individual hard disk failure.
 
Windows or macOS also have the concept of LVM but use different names for it like [Storage Spaces](https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/overview) (Windows) or [CoreStorage](https://en.wikipedia.org/wiki/Core_Storage) (macOS).

We can use LVM to create snapshots to save configuration in case something goes wrong.

## APT
Advanced Packaging Tool (APT) that uses dpkg. APT uses repositories which are online software that serve as the source for these packages (to view: 
`cat /etc/apt/sources.list.d/parrot.list`). APT makes managing program packages and updating them easier. 

## Terminal Adjustment
Popular terminal emulators: 
- [Terminator](https://terminator-gtk3.readthedocs.io/en/latest/)
- [Guake](http://guake-project.org/)
- [iTerm2](https://iterm2.com/)
- [Terminology](https://www.enlightenment.org/docs/apps/terminology.md)
Tmux is a an extension for managing shell sessions. We can also use [Bash Prompt Generator](https://bash-prompt-generator.org/) to customize our bash prompt (the text that appears before commands) making it display time for example is useful.

We can make scripts that do changes like this quickly if we need them frequently and save them on a VPS. To test the result, take a snapshot before and after the script.

# Windows
Linux users can use Windows Subsystem for Linux (WSL). There is also WSL2 which allows the usage of Linux tools from the windows host.

Another useful tool is Chocolatey Package Manager which is useful for package installation and automation with Powershell and Ansible.
When scripting with Chocolatey, the developers recommend a few rules to follow:
- always use `choco` or `choco.exe` as the command in your scripts. `cup` or `cinst` tends to misbehave when used in a script.
- when utilizing options like `-n` it is recommended that we use the extended option like `--name`.
- Do not use `--force` in scripts. It overrides Chocolatey's behavior.

Windows Defender can block some of the scripts we make, to prevent that make exclusions for files where you store scripts to do this use:
`Add-MpPreference -ExclusionPath "C:\filePath"`
Also to run scripts we need to change the execution policy for windows.

To simulate what would happen to a victim we need to have different versions and patches of windows to see how they would behave. This can be achieved with snapshots.
