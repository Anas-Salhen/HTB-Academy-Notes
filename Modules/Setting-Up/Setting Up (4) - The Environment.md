# Terminal Emulators
1. [Ghostty](https://ghostty.org/): Focuses on performance and speed while allowing customization of necessary parts.
2. [Alacritty](https://alacritty.org/): Fast but less customization.
3. [Wave Terminal](https://www.waveterm.dev/): AI-driven and Visually stunning but requires a lot of resources.

# The Shell
Z Shell (ZSH) is a really good shell especially when paired with ohmyzsh and the powerlevel 10k theme.

# Terminal Multiplexer
It's a tool that allows multiple terminal sessions to be managed in the same window.
Tools like Wave terminal, Ghostty and Tmux are often useful for this purpose.

Note that Ghostty doesn't support session reattachment over SSH unlike Tmux which supports it but has a harder learning curve. Once you get used to it it becomes a very powerful tool.

# Code Editor
## VSCode
it's a graphical Integrated Development Environment (IDE) which has many useful tools for debugging and Git. It's also highly customizable and has an integrated terminal.
Some useful extensions for VSCode include:

|**General**|**Python**|**Docker**|**Git**|**AI**|
|---|---|---|---|---|
|[Better Comments](https://marketplace.visualstudio.com/items/?itemName=aaron-bond.better-comments)|[Python](https://marketplace.visualstudio.com/items/?itemName=ms-python.python)|[Docker](https://marketplace.visualstudio.com/items/?itemName=ms-azuretools.vscode-docker)|[Gitlens](https://marketplace.visualstudio.com/items/?itemName=eamodio.gitlens)|[Continue](https://marketplace.visualstudio.com/items/?itemName=Continue.continue)|
|[Material Icon Theme](https://marketplace.visualstudio.com/items/?itemName=PKief.material-icon-theme)|[Python Auto VENV](https://marketplace.visualstudio.com/items/?itemName=WolfiesHorizon.python-auto-venv)|[Remote Containers](https://marketplace.visualstudio.com/items/?itemName=ms-vscode-remote.remote-containers)|||
|[Postman for VSCode](https://marketplace.visualstudio.com/items/?itemName=Postman.postman-for-vscode)|[Python Indent](https://marketplace.visualstudio.com/items/?itemName=KevinRose.vsc-python-indent)||||
|[Remote SSH](https://marketplace.visualstudio.com/items/?itemName=ms-vscode-remote.remote-ssh)|[Indent Rainbow](https://marketplace.visualstudio.com/items/?itemName=oderwat.indent-rainbow)||||
|[Quicktype](https://marketplace.visualstudio.com/items/?itemName=quicktype.quicktype)|[Arepl](https://marketplace.visualstudio.com/items/?itemName=almenon.arepl)||||
|[Prettier](https://marketplace.visualstudio.com/items/?itemName=esbenp.prettier-vscode)|||||
|[Peacock](https://marketplace.visualstudio.com/items/?itemName=johnpapa.vscode-peacock)|

## NeoVim
a terminal based text editor that is very lightweight and requires minimal resources making it significantly faster than VSCode. There is also NvChad which is a pre-configured neovim framework. It's recommended to run `vimtutor` before using neovim/vim.

# Productivity Utilities
## FZF
Is a terminal based tool that uses fuzzy search instead of exact search which is fast, works well with long lists and tolerant of typos.

## EZA
Is a modern alternative to the linux command `ls`. It provides:
- Colorful detailed file listings
- Git status
- Icons
- Advanced features like hyperlinks

## Bat
An alternative to the `cat` command which offers syntax highlighting, Git integration and a user friendly interface.

## Btop
A replacement for `htop` and `top` that monitors CPU, memory, disk, network and processes.

# Remote Desktop
Remote desktop protocols (RDPs) allow users to control a computer or a server remotely over the network. Some of the tools that can be used to achieve such a task are:
- XfreeRDP: supports network level authentication, TLS encryption, and credential passing. Also supports low-latency connection with the support for H.264 compression.
- RDesktop: lightweight but less actively maintained that xfreerdp resulting in fewer features.
- Rustdesk: an open source alternative to TeamViewer and AnyDesk. It has many features that make it both secure and efficient.

# Containers
![[Pasted image 20250402053954.png]]
Containers don't have their own OS which makes them lighter than VMs. this is also known as application virtualization. They are known for high scalability. Each container uses an image (copy) of the file system.

| VM                                    | Container                                                                    |
| ------------------------------------- | ---------------------------------------------------------------------------- |
| Applications + OS                     | Applications and necessary OS components (libraries, binaries, dependencies) |
| A hypervisor provides virtualization  | The OS with the container engine provides its own virtualization             |
| VMs run isolated on a physical server | Containers run isolated on the OS                                            |
Namespaces: isolates the environment, making it seem like each container has its own resources and they can't see each other.
Cgroups: This feature allows the kernel to limit and prioritize the amount of CPU, memory, and I/O resources allocated to each container, ensuring resource control.

Cooperation between different applications is possible through a container **(daemon)** like Linux Container Daemon **(LXD)** which is similar to Linux Containers **(LXC)**.

LXC is a container-based virtualization technology which uses namespaces and cgroups. It was also the basis for Docker virtualization.

LXD allows the use of commands which enables automating mass container management.

Orchestration systems such as Apache Mesos or Google Kubernetes distribute the containers over the existing hardware based on predefined rules and monitor them.

## Docker
An open source software that isolates applications into containers which simplifies the deployment of applications. It's also known for portability since programs and their dependencies are stored in **Docker Images** which can run on any OS. Docker Engine provides the interface between host resources and running containers.

## Vagrant 
A tool mainly used to configure VMs using code that is placed in a Vagrantfile instead of doing it manually. Then the code can be processed through the Vagrant CLI. Vagrant supports providers like VMware and Docker. 

# Isolation and Sandboxing
Sandboxing is used to control the environment in which we execute applications preventing them from getting unauthorized access to system resources. For example when we receive a suspicious link we can launch a browser in a secure sandbox and check the link there.

**Isolation** is a security concept that involves separating processes/applications/environments and preventing them from interacting with each other. Sandboxing is part of isolation.

Sandboxing tools:
- Firejail: a lightweight tool that uses kernel security features to isolate the processes. However it isn't 100% bulletproof its reliance on namespaces and seccomp has known vulnerabilities—especially if syscalls aren’t properly filtered—and a kernel exploit exists that could bypass firejail’s restrictions.

- KASM: a secure, browser-based platform that lets you run apps and entire desktops in the cloud—like having a temporary computer inside your web browser, with no installation needed.

# HTTP Utilities
Every business has to interact with the internet whether through HTTP requests, APIs or web servers. Some of the best tools for that purpose are:
- Curlie: built upon `curl` and `httpie`.
- Postman: Offers both a command line and a GUI. It is suited for things beyond basic one-off API requests.
- Posting.sh

# Self Hosting AI
Using an AI/LLM locally is beneficial for many reasons including privacy and offline accessibility. Another important aspect is being able to to fine-tune the model. However the drawback is that it requires a lot of CPU and GPU resources.
One of the best tools for that purpose is LM Studio.

# AI in the Terminal
We can use AI in our terminal by linking LM Studio with terminal emulators like Wave terminal.
There is also Warp terminal which comes equipped with its own AI. However to use your own locally hosted AI you need an enterprise plan.
