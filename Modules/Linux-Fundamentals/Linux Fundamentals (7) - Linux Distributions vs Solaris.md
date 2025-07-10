Last Update: 2025-07-10 2:01 PM
# Solaris
Solaris is a Unix-based OS developed by Sun Microsystems (later acquired by Oracle Corporation) in the 1990s. It is designed to handle large amounts of data and provide reliable and secure services to users and is often used in enterprise environments where security, performance, and stability are key requirements.

## Linux Distribution vs Solaris
Solaris and Linux distributions are two types of OSs that differ significantly. Firstly Solaris is owned by Oracle and isn't open source unlike many Linux distributions. Additionally, Linux distributions commonly use the Zettabyte File System (ZFS) while Solaris uses a Service Management Facility (SMF), both have their own features and purpose.

|**Directory**|**Description**|
|---|---|
|`/`|The root directory contains all other directories and files in the file system.|
|`/bin`|It contains essential system binaries that are required for booting and basic system operations.|
|`/boot`|The boot directory contains boot-related files such as boot loader and kernel images.|
|`/dev`|The dev directory contains device files that represent physical and logical devices attached to the system.|
|`/etc`|The etc directory contains system configuration files, such as system startup scripts and user authentication data.|
|`/home`|Users’ home directories.|
|`/kernel`|This directory contains kernel modules and other kernel-related files.|
|`/lib`|Directory for libraries required by the binaries in /bin and /sbin directories.|
|`/lost+found`|This directory is used by the file system consistency check and repair tool to store recovered files.|
|`/mnt`|Directory for mounting file systems temporarily.|
|`/opt`|This directory contains optional software packages that are installed on the system.|
|`/proc`|The proc directory provides a view into the system's process and kernel status as files.|
|`/sbin`|This directory contains system binaries required for system administration tasks.|
|`/tmp`|Temporary files created by the system and applications are stored in this directory.|
|`/usr`|The usr directory contains system-wide read-only data and programs, such as documentation, libraries, and executables.|
|`/var`|This directory contains variable data files, such as system logs, mail spools, and printer spools.|
Solaris uses the Image Packaging System (IPS) package manager. Solaris also provides advanced security features, such as Role-Based Access Control (`RBAC`) and mandatory access controls, which are not available in all Linux distributions.

## Differences
Differences can be grouped into these categories:
- Filesystem
- Process management
- Package management
- Kernel and Hardware support
- System monitoring
- Security

For example `uname` is the command that displays system information in Linux. In Solaris this is achieved by `showrev` which provides more detailed info like patch level and hardware provider.

Instead of the Advanced Packaging Tool (APT) Solaris uses the Solaris Package Manager (SPM) to manage packages.

Like Linux, Solaris uses `chmod` to manage permissions. To find files use the `find` command.

## NFS in Solaris
It can be configured using the `share` command. For example `share -F nfs -o rw /export/home` shares the `/export/home` directory with read and write permissions. NFS can also be used to mount a file system using the `mount` command. In Solaris, configuration is stored in `/etc/dfs/dfstab`.

## Process Mapping
The `pfiles` command in Solaris works similarly to the `lsof` command in Linux.

## Executable Access
In Solaris `truss` is a very useful command for debugging. By tracing system calls it can help identify errors and performance issues. `strace` is the command that performs this role in Linux.

Both commands are similar but differences do exist. For instance, `truss` can trace signals sent to a process while `strace` can't. Another thing is that unlike `strace`, which requires the `-f` option to trace child processes, `truss` follows them by default. Also, `truss` can trace signals more effectively than `strace`.
