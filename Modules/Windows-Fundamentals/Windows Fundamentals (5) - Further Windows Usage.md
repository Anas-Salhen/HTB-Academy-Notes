# Microsoft Management Console (MMC)
Microsoft Management Console (MMC) is a built-in Windows application that lets system administrators and power users manage hardware, software, users, and network settings through snap-ins (mini admin tools). We can also use it to create custom windows tools and distribute them to users.

We can open MMC by just typing `mmc` in the Start menu. When we open MMC for the first time, it will be blank.  
![[Pasted image 20250715181156.png]]
From here, we can browse to File --> Add or Remove Snap-ins, and begin customizing our administrative console.  
![[Pasted image 20250715181225.png]]
As we begin adding snap-ins, we will be asked if we want to add the snap-in to manage just the local computer or if it will be used to manage another computer on the network. Once we have finished adding snap-ins, they will appear on the left-hand side of MMC. From here, we can save the set of snap-ins as a .msc file, so they will all be loaded the next time we open MMC. By default, they are saved in the Windows Administrative Tools directory under the Start menu. Next time we open MMC, we can choose to load any of the views that we have created.  
![[Pasted image 20250715181304.png]]

# Windows Subsystem for Linux (WSL)
It is a feature that allows Linux binaries to be run natively on Windows 10 and Windows Server 2019. It was originally intended for developers who needed to run Bash, Ruby and native Linux tools like `grep`. The second version released in May 2019 introduced a real Linux kernel.

It can be installed from PowerShell using: `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`. Once this feature is enabled, we can either download a Linux distro from the Microsoft Store and install it or manually download the Linux distro of our choice and unpack and install it from the command line.

WSL installs `Bash.exe`, which can be run by merely typing `bash` into a Windows console to spawn a Bash shell. We have the full look and feel of a Linux host from this shell, including the standard Linux directory structure.
```powershell-session
PS C:\htb> ls /

bin dev home lib lLib64 media opt root sbin srv tmp var
boot etc init 1lib32 Libx32 mnt proc run Snap sys usr
```
When inside WSL our windows drives are automatically mounted under `/mnt`. `C:\` would be in `/mnt/c`.