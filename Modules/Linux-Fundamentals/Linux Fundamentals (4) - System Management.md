<style>
p { white-space: pre-wrap; }
</style>
# User Management
| **Command** | **Description**                                                                                                                                            |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sudo`      | Execute command as a different user.                                                                                                                       |
| `su`        | The `su` utility requests appropriate user credentials via PAM and switches to that user ID (the default user is the superuser). A shell is then executed. |
| `useradd`   | Creates a new user account or updates default configuration for new users.                                                                                 |
| `userdel`   | Deletes a user account and related files.                                                                                                                  |
| `usermod`   | Modifies a user account.                                                                                                                                   |
| `addgroup`  | Adds a group to the system.                                                                                                                                |
| `delgroup`  | Removes a group from the system.                                                                                                                           |
| `passwd`    | Changes user password.                                                                                                                                     |

# Package Management
Packages are archives that contain binaries of software, configuration files, information about dependencies and keep track of updates and upgrades. 

Most package management systems provide these features:
- Package downloading
- Dependency resolution
- A standard binary package format
- Common installation and configuration locations
- Additional system-related configuration and functionality
- Quality control

Package managers read the necessary changes for installation (like setting permissions) from the package itself and applies them. 
If the package requires dependencies to be installed from a repository it either:
- warns the admin about missing dependencies
- automatically downloads them

When uninstalling, they remove files and configurations.

List of package programs (managers):

| **Command** | **Description**                                                                                                                                                                                                                                                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `dpkg`      | It's a low-level tool to install, remove, and manage `.deb` packages. It does not resolve dependencies. Higher-level front-ends like `apt` and `aptitude` handle those tasks.                                                                                                                                                                           |
| `apt`       | Apt provides a high-level command-line interface for the package management system.                                                                                                                                                                                                                                                                     |
| `aptitude`  | Aptitude is an alternative to apt and is a high-level interface to the package manager.                                                                                                                                                                                                                                                                 |
| `snap`      | Install, configure, refresh, and remove snap packages. Snaps enable the secure distribution of the latest apps and utilities for the cloud, servers, desktops, and the internet of things.                                                                                                                                                              |
| `gem`       | Gem is the front-end to RubyGems, the standard package manager for Ruby.                                                                                                                                                                                                                                                                                |
| `pip`       | Pip is a Python package installer recommended for installing Python packages that are not available in the Debian archive. It can work with version control repositories (currently only Git, Mercurial, and Bazaar repositories), logs output extensively, and prevents partial installs by downloading all requirements before starting installation. |
| `git`       | `git` is not a package manager, but a distributed version control system used to manage source code repositories. It's commonly used to clone software projects.                                                                                                                                                                                        |
The repository your system utilizes can be found on `/etc/apt/sources.list`

## APT
```shell-session
AnasSalhen@htb[/htb]$ apt-cache search impacket

impacket-scripts - Links to useful impacket scripts examples
polenum - Extracts the password policy from a Windows system
python-pcapy - Python interface to the libpcap packet capture library (Python 2)
python3-impacket - Python3 module to easily build and dissect network protocols
python3-pcapy - Python interface to the libpcap packet capture library (Python 3)
```
This allows searching for packages that are related to something (i.e. impacket).

To view additional info about a package use `apt-cache show (package)`. 
To list all installed packages use `apt list --installed`.
To install a package use `sudo apt install (package) -y`.

## Git
This command makes a directory then downloads a project from github there.
```shell-session
AnasSalhen@htb[/htb]$ mkdir ~/nishang/ && git clone https://github.com/samratashok/nishang.git ~/nishang
```

## DPKG
```shell-session
wget http://archive.ubuntu.com/ubuntu/pool/main/s/strace/strace_4.21-1ubuntu1_amd64.deb
```
This downloaded a tool. Then the following command installs it:
```shell-session
sudo dpkg -i strace_4.21-1ubuntu1_amd64.deb 
```

# Services and Process Management
Services, also known as daemons, run silently in the background "without direct user interaction".
They are categorized into:
- System Services: services required during startup, they allow the OS to function properly.
- User-Installed Services: programs/services added by the user.

Daemons are identified by 'd' at the end of their name like `sshd` and `systemd`.
When dealing with a service/process we have the following goals:
1. Start/Restart a service/process
2. Stop a service/process
3. See what is/was happening with a service/process
4. Enable/Disable a service/process on boot
5. Find a service/process

`systemd` is the initialization system of most linux distros. It's the first process after boot and is assigned the Process ID 1 (PID 1). All processes are assigned a PID and can be viewed under `/proc/` which contains info about each process. A Parent Process ID (PPID) indicates that the process was started by another one.

## systemctl
To start a service: `systemctl start (service)`
To check if it runs without errors: `systemctl status ssh`
To add the service to the SysV to run it after startup: `systemctl enable (service)`
To check if it automatically runs after booting: `ps -aux | grep (service)`
To list all services: `systemctl list-units --type=service`
To view logs (investigate errors): `journalctl -u (service) --no-pager`
	`--no-pager` prevents paging output through `less`, showing everything directly in the terminal.

## Killing a Process
A process can be in these states:
- Running
- Waiting (for an event or system resource)
- Stopped
- Zombie (stopped but still has an entry in the process table)

Processes can be controlled using `kill`, `pkill`, `pgrep`, and `killall`. To interact with a process we must send a signal to it. The following command shows all signals:
```shell-session
AnasSalhen@htb[/htb]$ kill -l

 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 2) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
3) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
4) IGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
5) IGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
6) IGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
7) IGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
8) IGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
9) IGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
10) IGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
11) IGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
12) IGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
13) IGRTMAX-1  64) SIGRTMAX
```
The most commonly used ones:

|**Signal**|**Description**|
|---|---|
|`1`|`SIGHUP` - This is sent to a process when the terminal that controls it is closed.|
|`2`|`SIGINT` - Sent when a user presses `[Ctrl] + C` in the controlling terminal to interrupt a process.|
|`3`|`SIGQUIT` - Sent when a user presses `[Ctrl] + D` to quit.|
|`9`|`SIGKILL` - Immediately kill a process with no clean-up operations.|
|`15`|`SIGTERM` - Program termination.|
|`19`|`SIGSTOP` - Stop the program. It cannot be handled anymore.|
|`20`|`SIGTSTP` - Sent when a user presses `[Ctrl] + Z` to request for a service to suspend. The user can handle it afterward.|
For example if a program freezes we can force kill it with `kill 9 (PID)`

## Foreground/Background a Process
We can use Ctrl + Z to suspend a process then we use `bg` to put it in the background. 
By using `&` at the end of a command (like `ping`) we send it to the background freeing up the terminal for us to use it and when the process is done output is displayed.
`jobs` shows background processes.
To bring a background process to the foreground we can use `fg (job ID)`

## Executing Multiple Commands
Can be achieved by separating commands with:
- Semicolon (`;`): executes the commands ignoring the previous command's results and errors
- Double `ampersand` characters (`&&`): if there is an error the following command won't be executed
- Pipes (`|`): not only the previous command should be correct and error free it also depends on the output.

# Task Scheduling
Helps in automation by making tasks run automatically at a certain time. Also can be a sign of malicious activity
## Systemd
With it we can set a time for scripts to run or trigger a task when an event happens. To do this we follow these steps:
1. Create a timer (schedules when your `mytimer.service` should run)
2. Create a service (executes the commands or script)
3. Activate the timer
### Create a timer 
first create the directory and file:
```shell-session
AnasSalhen@htb[/htb]$ sudo mkdir /etc/systemd/system/mytimer.timer.d
AnasSalhen@htb[/htb]$ sudo vim /etc/systemd/system/mytimer.timer
```

It should have The "Unit" option which has a description, the "Timer" option which specifies when to start the timer and when to activate it and the "Install" which specifies where to install the timer.
```txt
[Unit]
Description=My Timer

[Timer]
OnBootSec=3min
OnUnitActiveSec=1hour

[Install]
WantedBy=timers.target
```
`OnBootSec` lets the script run once the system boots. `OnUnitActiveSec` makes the system run it on regular intervals.

### Create a service
```shell-session
AnasSalhen@htb[/htb]$ sudo vim /etc/systemd/system/mytimer.service
```

```txt
[Unit]
Description=My Service

[Service]
ExecStart=/full/path/to/my/script.sh

[Install]
WantedBy=multi-user.target
```
In this file we set a description and specify the full path to the script we want to run. The "multi-user.target" is the unit system that is activated when the system reaches multi-user mode where the OS is fully operational but doesn't have a GUI running.

After that we have to let systemd read the folders again to include changes. Use:
`sudo systemctl daemon-reload`

### Start the timer and service
```shell-session
AnasSalhen@htb[/htb]$ sudo systemctl start mytimer.timer
AnasSalhen@htb[/htb]$ sudo systemctl enable mytimer.timer
```
This starts the service and enables autostart. 

## Cron
Also useful for automating tasks. To set up the cron daemon we need to store tasks in a file called `crontab` and then tell it when to run them.
The structure of cron consists of the following components (columns):

| **Time Frame**         | **Description**                                                       |
| ---------------------- | --------------------------------------------------------------------- |
| Minutes (0-59)         | This specifies in which minute the task should be executed.           |
| Hours (0-23)           | This specifies in which hour the task should be executed.             |
| Days of month (1-31)   | This specifies on which day of the month the task should be executed. |
| Months (1-12)          | This specifies in which month the task should be executed.            |
| Days of the week (0-7) | This specifies on which day of the week the task should be executed.  |
For example a `crontab` could look like:
```txt
# System Update (executed every 6h)
0 */6 * * * /path/to/update_software.sh

# Execute scripts (executed every first day of the month at midnight)
0 0 1 * * /path/to/scripts/run_scripts.sh

# Cleanup DB (executed every Sunday at midnight)
0 0 * * 0 /path/to/scripts/clean_database.sh

# Backups (executed every Sunday at midnight)
0 0 * * 7 /path/to/scripts/backup.sh
```

# Network Services
## SSH
The most commonly used SSH is OpenSSH which is a free open-source implementation of the SSH protocol that allows secure encrypted transmission of data and commands.
To install OpenSSH: `sudo apt install openssh-server -y`
To check is the server is running: `systemctl status ssh`
To login: `ssh (username)@(ip address)`

SSH can be customized using the `/etc/ssh/sshd_config` file.

## NFS
Network File System is a protocol that allows storing and managing files on a remote system as if it were the local system to enable easy collaboration and management of data. Some Linux NFS servers include: NFS-UTILS (`Ubuntu`), NFS-Ganesha (`Solaris`), and OpenNFS (`Redhat Linux`). It can also be used like FTP in transferring files.
To install: `sudo apt install nfs-kernel-server -y`
To check if the server is running: `systemctl status nfs-kernel-server`

NFS can be customized using `/etc/exports` which includes things like access rights (permissions). Here are the most important ones:

| **Permissions**  | **Description**                                                                                                                                            |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rw`             | Gives users and systems read and write permissions to the shared directory.                                                                                |
| `ro`             | Gives users and systems read-only access to the shared directory.                                                                                          |
| `no_root_squash` | Prevents the root user on the client from being restricted to the rights of a normal user.                                                                 |
| `root_squash`    | Restricts the rights of the root user on the client to the rights of a normal user.                                                                        |
| `sync`           | Synchronizes the transfer of data to ensure that changes are only transferred after they have been saved on the file system.                               |
| `async`          | Transfers data asynchronously, which makes the transfer faster, but may cause inconsistencies in the file system if changes have not been fully committed. |

## Web Servers
A web server is software that delivers data, documents, applications, and various functions over the Internet. It uses protocols like HTTP to transfer data which when received is rendered as HTML. 

Most used web servers on Linux include: Apache, Nginx, Lighttpd, and Caddy, with Apache being particularly popular due to its broad compatibility with operating systems including Ubuntu, Solaris, and Red Hat Linux.

### Apache
install: `sudo apt install apache2 -y`
configuration file: `/etc/apache2/apache2.conf`
Example:
```txt
<Directory /var/www/html>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</directory>
```
Here the default `/var/www/html` folder is accessible, `Indexes` and `FollowSymLinks` options are enabled, files in this directory can be overridden with `AllowOverride All` and all users are granted access to this directory.

Also instead of editing the apache main configuration file we can create a `.htaccess` file inside the directory that needs to be edited.

### Python Web Server
a fast simple alternative to apache and can be used to host a single folder with a single command to transfer files to another system.

install: `sudo apt install python3 -y`
`python3 -m http.server` starts a web server on port 8000 and serves files from current directory. To view it navigate to `http://localhost:8000` from your web browser.

`python3 -m http.server --directory /home/username/target_files` serves files from that directory instead of the current directory.

`python3 -m http.server 443` starts the web server on port 443 instead of 8000

## VPN
A Virtual Private Network acts as a secure,  invisible and encrypted tunnel that connects us to another network. VPNs are mainly used by organizations to allow employees to reach company resources without being on-site. It's also used to maintain user anonymity online.

Most widely used VPN solutions  for Linux servers include OpenVPN, L2TP/IPsec, PPTP, SSTP, and SoftEther with OpenVPN being a very popular open-source option compatible with various operating systems, including Ubuntu, Solaris, and Red Hat Linux.

### OpenVPN
install: `sudo apt install openvpn -y`
configuration file: `/etc/openvpn/server.conf`

To connect to vpn save the `.ovpn` file you received from the server on your system then run: `sudo openvpn --config (file.ovpn)` 

# Working With Web Services
## Apache
### Modules
These modules provide additional functionalities to Apache.
- `mod_ssl`: secures the communication between the browser and the web server by encrypting the data.
- `mod_proxy`: directs requests to their correct destination, useful for proxy servers.
- `mod_headers` and `mod_rewrite` allow control over the data traveling between browser and server, allowing you to do things like modifying HTTP headers and URLs on the fly.

### Using Apache
The commands `apache2ctl`, `systemctl` or `service` can be used to start the apache server (i.e. `sudo systemctl start apache2`).
Once started we can use our browser to navigate to the default page (http://localhost). By default, apache works with port 80.

To interact with web servers we need to use our command line tools like `curl` and `wget`. These tools serve as web browsers within the terminal.

## cURL
It's a tool that allows us to transfer files from the shell over HTTP, HTTPS, FTP, SFTP, FTPS, or SCP.
`curl (URL)` returns the source code of that particular URL without rendering.

## wget
Allows downloading files from FTP or HTTP servers directly from the terminal, and it also acts as a download manager. The difference between it and `curl` is that it downloads content and stores it locally.

# Backup and Restore
## Rsync
An open-source tool for fast and secure backups.  it only transfers the portions of files that have changed, making it very fast and efficient.
install: `sudo apt install rsync -y`

```shell-session
rsync -av /path/to/mydirectory user@backup_server:/path/to/backup/directory
```
This command will copy the directory `/path/to/mydirectory` to `backup_server` to the directory `/path/to/backup/directory`. The option archive (`-a`) preserves original file attributes like permissions and verbose (`-v`) shows progress of the process. 

```shell-session
rsync -avz --backup --backup-dir=/path/to/backup/folder --delete /path/to/mydirectory user@backup_server:/path/to/backup/directory
```
This command does an incremental backup. The additional (`-z`) option allows compression for faster transfers. The `--backup` option creates incremental backups in the directory `/path/to/backup/folder`. `--delete` removes removes files from the remote host that no longer exist in the source directory.

```shell-session
rsync -av user@remote_host:/path/to/backup/directory /path/to/mydirectory
```
Restores the backup from the backup server to the backup directory.

```shell-session
rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```
To ensure that the transfer of the backup file is safe we can use SSH. `-e ssh` allows us to do so.

### Automation
We can automate the process by using both cron and rsync to schedule a cron job to run automatically which keeps system up-to-date, reduces errors and improves efficiency. 
We will need to create a script to trigger the rsync command and since we are using a script to perform SSH we need key-based authentication. 

Generate a key pair for the user: `ssh-keygen -t rsa -b 2048`
Copy our public key to the remote server: `ssh-copy-id user@backup_server`

Now create the script `RSYNC_Backup.sh` its content should be as follows:
```bash
#!/bin/bash

rsync -avz -e ssh /path/to/mydirectory user@backup_server:/path/to/backup/directory
```
Then change file permissions to make it executable: `chmod +x RSYNC_Backup.sh`
Edit crontab: `crontab -e`
`0 * * * * /path/to/RSYNC_Backup.sh` This will run the script at the 0th minute of every hour.

## Duplicity
Works similarly to Rsync but adds encryption features. For more security you can use tools like GnuPG, eCryptfs, or LUKS to add another layer of protection to your backups.

## Deja Dup
More user-friendly with a nice GUI. Behind the scenes, uses Rsync and offers encrypted backups like Duplicity.

# File System Management
## Linux File Systems
- ext2: old, no journaling, less suited for modern systems but useful in low overhead scenarios like USB drives.

- ext3 and ext4: more advanced, has journaling (helps in recovering from crashes). ext4 is the default for modern Linux systems because of its performance, reliability and large file support.

- Btrfs: known for advanced features like snapshotting and built-in data integrity checks, making it ideal for complex storage setups.

- XFS: excels at handling large files and has high performance, best suited for environments with high I/O demands.

- NTFS: useful for compatibility when dealing with dual-boot systems or external drives that need to work on both Linux and Windows systems.

## Inodes
Inodes store info about the file like permissions, ownership, size, and timestamps but they don't store its name or contents. These inodes are stored in the inode table which acts as a database that tracks every file and directory on the system.

## File Types
- regular files
- directories
- symbolic links: act as shortcuts to another file, they don't store the file's content but rather a path to it.

## Disks and Drives
We can divide the physical storage space into logical sections, this is called partitioning. Then, each partition can use its own file system like ext4, NTFS, or FAT32. Most common Linux partitioning tools are fdisk, gpart, and GParted.
`sudo fdisk -l`: list all partitions.

## Mounting
Each storage drive or logical partition must be assigned to a specific directory (also called a mount point) in the file system, this process is known as mounting. 

Use `mount` to shows mounted file systems, including the device name, file system type, mount point, and options.

To manually mount a drive use `mount` followed by the path for that drive then the mount point. In the following example we mount a USB drive (`/dev/sdb1`) to the directory `/mnt/usb`.
```shell-session
sudo mount /dev/sdb1 /mnt/usb
```

To unmount use `umount` followed by the mount point: `sudo umount /mnt/usb` 
Note that to unmount we need sufficient permissions. Also, the file system must not be in use by a running process to check that use `lsof`.

There is also the `/etc/fstab` file which can be configured so that specific files are automatically mounted on startup along with other options.

## Swap
When data in the RAM is inactive it's moved to a designated area in the storage drive called the swap space, freeing up the RAM.
`mkswap`: creates a swap space.
`swapon`: allows the system to use it.

When setting up swap space it's important to allocate it on a dedicated partition or file. Also, since sensitive data can be stored there it's recommended to encrypt it.

### Hibernation
While swap space is useful when running low on RAM it's also used for another purpose. Hibernation is saving the system's current state (including open applications and processes) to the swap space and powers off the system so when it's turned back on you can resume where you left.

# Containerization
It's the process of running applications in isolated environments referred to as containers that have all  the required tools, settings and files. In such a process some technologies are used like Docker, Docker Compose and Linux Containers (LXC).

The difference between containers and VMs is that containers don't have a separate OS from the host machine which makes them lightweight and efficient.

Containers offer many advantages one of them being security. They isolate applications from the host system and from each other which reduces the risk of malware spreading through the system.
Note that they don't provide the same level of isolation as VMs. If not properly managed things like privilege escalation and container escapes can bypass security.

In addition to security they provide other advantages like efficiency, consistency and ease of deploy which make applications easier to manage and scale.

## Dockers
An open-source platform for automating the deployment of applications as containers.
```bash
#!/bin/bash

# Preparation
sudo apt update -y
sudo apt install ca-certificates curl gnupg lsb-release -y
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt update -y
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Add user htb-student to the Docker group
sudo usermod -aG docker htb-student
echo '[!] You need to log out and log back in for the group changes to take effect.'

# Test Docker installation
docker run hello-world
```
This script installs docker. Don't worry if you don't understand everything but try to check how these commands work and what's their purpose here.

Then we need to create a Dockerfile. This file contains all the instructions needed to create a read-only docker image. A docker image is like a template, when we run that template it creates a container. 

For example, if we want a container to function as a file hosting server that uses Apache and SSH our Dockerfile would look like the following:
```bash
# Use the latest Ubuntu 22.04 LTS as the base image
FROM ubuntu:22.04

# Update the package repository and install the required packages
RUN apt-get update && \
    apt-get install -y \
        apache2 \
        openssh-server \
        && \
    rm -rf /var/lib/apt/lists/*

# Create a new user called "docker-user"
RUN useradd -m docker-user && \
    echo "docker-user:password" | chpasswd

# Give the docker-user user full access to the Apache and SSH services
RUN chown -R docker-user:docker-user /var/www/html && \
    chown -R docker-user:docker-user /var/run/apache2 && \
    chown -R docker-user:docker-user /var/log/apache2 && \
    chown -R docker-user:docker-user /var/lock/apache2 && \
    usermod -aG sudo docker-user && \
    echo "docker-user ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Expose port 22 (for SSH) and port 80 (for HTTP)
EXPOSE 22 80

# Start the SSH and Apache services
CMD service ssh start && /usr/sbin/apache2ctl -D FOREGROUND
```

Docker images can also be obtained from docker hub which contains images from the Docker development team and established open-source projects. If you wish to, you can upload some of your docker images there too.

Next to build the image use this command: `docker build -t FS_docker .`
`-t FS_docker`: tags the image for easy reference
`.`: means the Dockerfile is in the current directory.

Remember that the image is only a template. To start our container we need to run that image: `docker run -p 8022:22 -p 8080:80 -d FS_docker`

`-p`: maps the host ports 8022 and 8080 to container ports 22 and 80, respectively. 
`-d`: runs the container in detached mode (background).
`FS_docker`: the image used for creating it.

### Docker Commands
| **Command**      | **Description**               |
| ---------------- | ----------------------------- |
| `docker ps`      | List all running containers   |
| `docker stop`    | Stop a running container.     |
| `docker start`   | Start a stopped container.    |
| `docker restart` | Restart a running container.  |
| `docker rm`      | Remove a container.           |
| `docker rmi`     | Remove a Docker image.        |
| `docker logs`    | View the logs of a container. |

### Docker Management
Any changes made to a running container based on an image are not automatically saved to the image. In order to preserve changes we need to create a new Dockerfile that starts with the `FROM` statement (specifying the base image) and then includes the necessary commands to apply the changes.

It's also important to know that Docker containers are stateless so any changes made inside a running container (e.g., modifying files) are lost when it's stopped. For that reason, using volumes is considered best practice to store that data.

In complex production environments using Docker Compose or Kubernetes can make managing multiple containers more efficient.

## Linux Containers (LXC)
Is a containerization technology that creates containers that are similar to a lightweight VM (although still not considered a VM). It uses many isolation features such as control groups (cgroups) and namespaces.

### LXC vs Docker
| **Category**     | **Description**                                                                                                                                                                                                                                                                                                                                                                                                         |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Approach`       | LXC is often seen as a more traditional, system-level containerization tool, focusing on creating isolated Linux environments that behave like lightweight virtual machines. Docker, on the other hand, is application-focused, meaning it is optimized for packaging and deploying single applications or microservices.                                                                                               |
| `Image building` | Docker uses a standardized image format (Docker images) that includes everything needed to run an application (code, libraries, configurations). LXC, while capable of similar functionality, typically requires more manual setup for building and managing environments.                                                                                                                                              |
| `Portability`    | Docker excels in portability. Its container images can be easily shared across different systems via Docker Hub or other registries. LXC environments are less portable in this sense, as they are more tightly integrated with the host system’s configuration.                                                                                                                                                        |
| `Easy of use`    | Docker is designed with simplicity in mind, offering a user-friendly CLI and extensive community support. LXC, while powerful, may require more in-depth knowledge of Linux system administration, making it less straightforward for beginners.                                                                                                                                                                        |
| `Security`       | Docker containers are generally more secure out of the box, thanks to additional isolation layers like AppArmor and SELinux, along with its read-only filesystem feature. LXC containers, while secure, may need additional configurations to match the level of isolation Docker offers by default. Interestingly enough, when misconfigured, both Docker and LXC can present a vector for local privilege escalation. |

### Install and configure LXC
Install: `sudo apt-get install lxc lxc-utils -y`
`sudo lxc-create -n linuxcontainer -t ubuntu` creates a container named `linuxcontainer` using the ubuntu template.

### LXC commands
| Command                                       | Description                                                    |
| --------------------------------------------- | -------------------------------------------------------------- |
| `lxc-ls`                                      | List all existing containers                                   |
| `lxc-stop -n <container>`                     | Stop a running container.                                      |
| `lxc-start -n <container>`                    | Start a stopped container.                                     |
| `lxc-restart -n <container>`                  | Restart a running container.                                   |
| `lxc-config -n <container name> -s storage`   | Manage container storage                                       |
| `lxc-config -n <container name> -s network`   | Manage container network settings                              |
| `lxc-config -n <container name> -s security`  | Manage container security settings                             |
| `lxc-attach -n <container>`                   | Connect to a container.                                        |
| `lxc-attach -n <container> -f /path/to/share` | Connect to a container and share a specific directory or file. |

### LXC and security
As pentesters containerization is extremely useful in many different scenarios like replicating different environments and testing malware in a safe isolated place. 

In order to achieve as much security as possible on LXC several security measures should be implemented like:
- Restricting access to the container
- Limiting resources
- Isolating the container from the host
- Enforcing mandatory access control
- Keeping the container up to date

### Using cgroups
For example, we can use cgroups to limit resources and prevent the container from consuming too much if something goes wrong. To do that we need to open the configuration file: `sudo vim /usr/share/lxc/config/linuxcontainer.conf` and set the following limits to CPU and memory:
```txt
lxc.cgroup.cpu.shares = 512
lxc.cgroup.memory.limit_in_bytes = 512M
```

`lxc.cgroup.cpu.shares`: sets the CPU time a container can use relative to other containers on the system. The default is 1024 which is a fair share so when we set it to 512 the container can use half the available CPU time.

`lxc.cgroup.memory.limit_in_bytes`: the max amount of memory the container can use measured in bytes, kilobytes (K), megabytes (M), gigabytes (G) or terabytes (T).
To apply these changes restart the LXC service: 
`sudo systemctl restart lxc.service`

### namespaces
They provide multiple isolation features like pid, net and mnt.

pid ensures that the container has its own process IDs separate from the host's process ID's which blocks the container's processes from interfering with the host's processes.

net: the container has its own network interfaces, routing tables and firewall rules. Basically all network activity is isolated from the host's network.

mnt: each container has its own root file system.
