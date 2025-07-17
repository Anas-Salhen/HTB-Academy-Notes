Last Update: 2025-07-10 2:00 PM
# History
First Unix operating system was released by Ken Thompson and Dennis Ritchie (who both worked for AT&T at the time) in 1970. The Berkeley Software Distribution (BSD) was released in 1977, but since it contained the Unix code owned by AT&T, its development stopped.
Richard Stallman started the GNU project in 1983. His goal was to create a free Unix-like operating system, and part of his work resulted in the GNU General Public License (GPL) being created.

In 1991 a Finnish student named Linus Torvalds started Linux as a personal project aiming to create a free operating system kernel. Over the years, the Linux kernel has gone from a few files written in C under licensing that prohibited commercial distribution to the latest version with over 23 million source code lines (comments excluded), licensed under the GNU General Public License v2.

Linux is available in over 600 distributions and is considered **more secure and more customizable** than other OSs. It is **less susceptible to malware** than Windows operating systems and is very frequently updated. It's also generally stable but **doesn't have as many hardware drivers** as windows.

Since it's free and open-source it can be modified and used commercially or non-commercially by anyone to the point that Android is based on the Linux kernel which is why Linux is the most widely installed OS.

# Principles - Philosophy

| Principle                                                     | Description                                                                                                                                                    |
| ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Everything is a file`                                        | All configuration files for the various services running on the Linux operating system are stored in one or more text files.                                   |
| `Small, single-purpose programs`                              | Linux offers many different tools that we will work with, which can be combined to work together.                                                              |
| `Ability to chain programs together to perform complex tasks` | The integration and combination of different tools enable us to carry out many large and complex tasks, such as processing or filtering specific data results. |
| `Avoid captive user interfaces`                               | Linux is designed to work mainly with the shell (or terminal), which gives the user greater control over the operating system.                                 |
| `Configuration data stored in a text file`                    | An example of such a file is the `/etc/passwd` file, which stores all users registered on the system.                                                          |

# Components
| **Component**     | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Bootloader`      | A piece of code that runs to guide the booting process to start the operating system. Parrot Linux uses the GRUB Bootloader.                                                                                                                                                                                                                                                                                                        |
| `OS Kernel`       | The kernel is the main component of an operating system. It manages the resources for system's I/O devices at the hardware level.                                                                                                                                                                                                                                                                                                   |
| `Daemons`         | Background services are called "daemons" in Linux. Their purpose is to ensure that key functions such as scheduling, printing, and multimedia are working correctly. These small programs load after we booted or log into the computer.                                                                                                                                                                                            |
| `OS Shell`        | The operating system shell or the command language interpreter (also known as the command line) is the interface between the OS and the user. This interface allows the user to tell the OS what to do. The most commonly used shells are Bash, Tcsh/Csh, Ksh, Zsh, and Fish.                                                                                                                                                       |
| `Graphics server` | This provides a graphical sub-system (server) called "X" or "X-server" that allows graphical programs to run locally or remotely on the X-windowing system.                                                                                                                                                                                                                                                                         |
| `Window Manager`  | Window managers control the placement and appearance of windows in the GUI. Full desktop environments (like GNOME, KDE, MATE, Unity XFCE and Cinnamon) build upon these to offer a complete user experience. A desktop environment usually has several applications, including file and web browsers. These allow the user to access and manage the essential and frequently accessed features and services of an operating system. |
| `Utilities`       | Applications or utilities are programs that perform particular functions for the user or another program.                                                                                                                                                                                                                                                                                                                           |

# Architecture and File System Hierarchy
Linux OS is broken down into the following layers:

| **Layer**        | **Description**                                                                                                                                                                                                                                                                                    |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Hardware`       | Peripheral devices such as the system's RAM, hard drive, CPU, and others.                                                                                                                                                                                                                          |
| `Kernel`         | The core of the Linux operating system whose function is to virtualize and control common computer hardware resources like CPU, allocated memory, accessed data, and others. The kernel gives each process its own virtual resources and prevents/mitigates conflicts between different processes. |
| `Shell`          | A command-line interface (**CLI**), also known as a shell that a user can enter commands into to execute the kernel's functions.                                                                                                                                                                   |
| `System Utility` | Makes available to the user all of the operating system's functionality.                                                                                                                                                                                                                           |

Linux is structured in a tree-like hierarchy documented in the  [Filesystem Hierarchy Standard](http://www.pathname.com/fhs/) (FHS). These are the top-level directories:
![Pasted image 20250407143235](https://github.com/user-attachments/assets/98d07ee5-df87-4a92-8239-d57cf6a17eab)


| **Path** | **Description**                                                                                                                                                                                                                                                                                                                    |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/`      | The top-level directory is the root filesystem and contains all of the files required to boot the operating system before other filesystems are mounted, as well as the files required to boot the other filesystems. After boot, all of the other filesystems are mounted at standard mount points as subdirectories of the root. |
| `/bin`   | Contains essential command binaries.                                                                                                                                                                                                                                                                                               |
| `/boot`  | Consists of the static bootloader, kernel executable, and files required to boot the Linux OS.                                                                                                                                                                                                                                     |
| `/dev`   | Contains device files to facilitate access to every hardware device attached to the system.                                                                                                                                                                                                                                        |
| `/etc`   | Local system configuration files. Configuration files for installed applications may be saved here as well.                                                                                                                                                                                                                        |
| `/home`  | Each user on the system has a subdirectory here for storage.                                                                                                                                                                                                                                                                       |
| `/lib`   | Shared library files that are required for system boot.                                                                                                                                                                                                                                                                            |
| `/media` | External removable media devices such as USB drives are mounted here.                                                                                                                                                                                                                                                              |
| `/mnt`   | Temporary mount point for regular filesystems.                                                                                                                                                                                                                                                                                     |
| `/opt`   | Optional files such as third-party tools can be saved here.                                                                                                                                                                                                                                                                        |
| `/root`  | The home directory for the root user.                                                                                                                                                                                                                                                                                              |
| `/sbin`  | This directory contains executables used for system administration (binary system files).                                                                                                                                                                                                                                          |
| `/tmp`   | The operating system and many programs use this directory to store temporary files. This directory is generally cleared upon system boot and may be deleted at other times without any warning.                                                                                                                                    |
| `/usr`   | Contains executables, libraries, man files, etc.                                                                                                                                                                                                                                                                                   |
| `/var`   | This directory contains variable data files such as log files, email in-boxes, web application related files, cron files, and more.                                                                                                                                                                                                |

# Linux Distributions (Distros)
OSs based on the Linux kernel. While they all share the same principles, components and architecture, each distro offers its unique software packages, configurations, UI and tools. 

Servers use it because it's secure, reliable and frequently updated. Cybersecurity specialists use it because it's open source, meaning its source code is available for scrutiny, customization and optimization. Linux distros can be used anywhere including servers, mobiles, embedded systems, cloud and desktop computing.

Popular distros include:
- [Ubuntu](https://ubuntu.com/): popular among desktop and beginner users also based on debian
- [Fedora](https://getfedora.org/): popular among desktop and beginner users
- [CentOS](https://www.centos.org/): popular for enterprise-level computing.
- [Debian](https://www.debian.org/): popular for servers and embedded systems
- [Red Hat Enterprise Linux](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux): popular for enterprise-level computing.
- [ParrotOS](https://www.parrotsec.org/): cybersecurity and used in HTB academy
- [BlackArch](https://www.blackarch.org/): cybersecurity
- [Kali](https://www.kali.org/): most popular in cybersecurity also based on debian
- [Raspberry Pi OS](https://www.raspberrypi.com/software/)
- [Pentoo](https://www.pentoo.ch/)
- [BackBox](https://www.backbox.org/)

# Debian
It uses Advanced Package Tool (APT) as its packet management system which helps keep the system up-to-date and secure by automatically downloading and installing security updates as soon as they are available. Debian has a steeper learning curve compared to other distros but it offers more flexibility and control.

It's also known for security, reliability, commitment to privacy and long-term support releases which provide updates and security patches for up to 5 years. This is important for systems that need to be up 24/7 like servers.

# Intro to Shell
Many servers are based on Linux because it's less error-prone as opposed to Windows servers, so it's important to know how to use shell. A Linux terminal, (also called a shell, command line or console) provides a text based I/O interface between the user and the kernel.

There are also terminal emulators that allow the use of text-based programs within a GUI. There are also command-line interfaces (CLI) that run as additional terminals in one terminal. In short a terminal is an interface for the shell interpreter. 

Terminal emulators and multiplexers provide us with more functionalities like splitting the terminal into one window, working in multiple directories, creating different workspaces, etc. An example of a multiplexer is Tmux which could look like this: 
![Pasted image 20250407164813](https://github.com/user-attachments/assets/c45a7dfd-ebec-4e4c-a3e1-a8f5a71ab14e)


The most commonly used shell in Linux is Bourne-Again Shell **(BASH)** which is part of the GNU project. Besides BASH there are other shells like  [Tcsh/Csh](https://en.wikipedia.org/wiki/Tcsh), [Ksh](https://en.wikipedia.org/wiki/KornShell), [Zsh](https://en.wikipedia.org/wiki/Z_shell) and [Fish](https://en.wikipedia.org/wiki/Friendly_interactive_shell).
