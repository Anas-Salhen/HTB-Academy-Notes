# Some Useful Tools
Password Managers:
1. [1Password](https://1password.com/)
2. [LastPass](https://www.lastpass.com/)
3. [Keeper](https://www.keepersecurity.com/)
4. [Bitwarden](https://bitwarden.com/)

Documentation Tools:
1.  [GhostWriter](https://github.com/GhostManager/Ghostwriter) 
2. [Pwndoc](https://github.com/pwndoc/pwndoc)

## Logging
For Linux: script and date.Date
For Windows: [Start-Transcript](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.host/start-transcript?view=powershell-7.1)

To display the date and time, we can replace the `PS1` variable in our `.bashrc` file with the following content.
```bash
PS1="\[\033[1;32m\]\342\224\200\$([[ \$(/opt/vpnbash.sh) == *\"10.\"* ]] && echo \"[\[\033[1;34m\]\$(/opt/vpnserver.sh)\[\033[1;32m\]]\342\224\200[\[\033[1;37m\]\$(/opt/vpnbash.sh)\[\033[1;32m\]]\342\224\200\")[\[\033[1;37m\]\u\[\033[01;32m\]@\[\033[01;34m\]\h\[\033[1;32m\]]\342\224\200[\[\033[1;37m\]\w\[\033[1;32m\]]\n\[\033[1;32m\]\342\224\224\342\224\200\342\224\200\342\225\274 [\[\e[01;33m\]$(date +%D-%r)\[\e[01;32m\]]\\$ \[\e[0m\]"
```

Format for saving logs: (date)-(start time)-(name).log

Terminal emulators, such as [Tmux](https://github.com/tmux/tmux/wiki) and [Terminator](https://terminator-gtk3.readthedocs.io/en/latest/) allow, among other things, to log all commands and output automatically.

For screenshots and GIFs use [Flameshot](https://github.com/flameshot-org/flameshot) and [Peek](https://github.com/phw/peek)

# Virtualization
![Pasted image 20250402052237](https://github.com/user-attachments/assets/a20397ac-b468-42b7-8b16-7ae74db9fd7f)
Types: Hardware virtualization, Application virtualization, Storage virtualization, Data virtualization and Network virtualization

## Virtual Machines (VMs)
A virtual operating system that runs on a host system. For the application a VM is just a normal computer.

**Benefits** of virtualization:
1. Applications and services of a VM do not interfere with each other
2. Complete independence of the guest system from the host system's operating system and the underlying physical hardware
3. VMs can be moved or cloned to other systems by simple copying
4. Hardware resources can be dynamically allocated via the hypervisor
5. Better and more efficient utilization of existing hardware resources
6. Shorter provisioning times for systems and applications
7. Simplified management of virtual systems
8. Higher availability of VMs due to independence from physical resources

Existing VMs from VMware are deployed in **`Open Virtualization Format` (`OVF`).** OVF is an open standard to package and distribute software running in virtual machines. The `OVF` standard is not limited to specific hypervisors or processor architectures and can be used by many different platforms like VMware, VirtualBox, XenServer, Oracle VM, and others.

With VirtualBox, hard disks are emulated in container files, called **Virtual Disk Images (VDI)**. VirtualBox can also handle (.vmdk) and (.vhd) formats.
