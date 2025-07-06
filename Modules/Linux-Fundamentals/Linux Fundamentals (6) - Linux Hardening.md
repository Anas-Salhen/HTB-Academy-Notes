# Linux Security
Linux systems are less prone to viruses that affect Windows operating systems and do not present as large an attack surface as Active Directory domain-joined hosts.

One of the most important security measures is keeping the OS and packages up to date which can be done by: `apt update && apt dist-upgrade`

To set firewall rules we can use the Linux firewall or `iptables`.

As for SSH, you should disable logging in as the root user and disable passwords since SSH keys are safer.

Also, avoid logging into your system as the root user as much as possible and always stick to principle of least privilege. This principle means that you (or other users) should have the least amount of permissions needed to get the job done. If you need to run a command as root then specify it in the `sudoers` configuration rather than giving full `sudo` rights. `fail2ban` is a useful tool that blocks the ip address after a specified number of failed login attempts.

Moreover, it's important to audit the system periodically to check for security issues like out-of-date kernel, user permission issues, world-writable files, and misconfigured cron jobs, or misconfigured services.

A number of tools can be used to enhance the security of Linux including SELinux, AppArmor, [Snort](https://www.snort.org/), [chkrootkit](http://www.chkrootkit.org/), [rkhunter](https://packages.debian.org/sid/rkhunter) and [Lynis](https://cisofy.com/lynis/).

Make sure to apply some security settings like:
- Removing or disabling all unnecessary services and software
- Removing all services that rely on unencrypted authentication mechanisms
- Ensure NTP is enabled and Syslog is running
- Ensure that each user has its own account
- Enforce the use of strong passwords
- Set up password aging and restrict the use of previous passwords
- Locking user accounts after login failures
- Disable all unwanted SUID/SGID binaries

## TCP Wrappers
Their configuration files are `/etc/hosts.allow` and `/etc/hosts.deny`. The following are examples of what you might find in these files:
```shell-session
AnasSalhen@htb[/htb]$ cat /etc/hosts.allow

# Allow access to SSH from the local network
sshd : 10.129.14.0/24

# Allow access to FTP from a specific host
ftpd : 10.129.14.10

# Allow access to Telnet from any host in the inlanefreight.local domain
telnetd : .inlanefreight.local
```

```shell-session
AnasSalhen@htb[/htb]$ cat /etc/hosts.deny

# Deny access to all services from any host in the inlanefreight.com domain
ALL : .inlanefreight.com

# Deny access to SSH from a specific host
sshd : 10.129.22.22

# Deny access to FTP from hosts with IP addresses in the range of 10.129.22.0 to 10.129.22.255
ftpd : 10.129.22.0/24
```
Please note that the order of the rules in the file is important and the first rule that matches the situation will be applied. Also TCP wrappers aren't a replacement for firewalls since they control access to services and not ports.

# Firewall Setup
The goal of firewalls is to monitor and control network traffic. One way to configure the firewall is using the `iptables` tool which was introduced in the Linux 2.4 kernel in 2000 and replaced `ipchains` and `ipfwadm`. `iptables` is capable of creating complex rulesets to protect the system against threats such as DoS attacks, port scans and network intrusion attempts.

Behind the scenes `iptables` works with a built-in firewall framework inside the Linux kernel called Netfilter.

Solutions other than `iptables` exist like `nftables` which provides a more modern syntax and improved performance. However migration from `iptables` to `nftables` requires some effort since the syntax of the rules isn't compatible.

There is also `UFW` which stands for Uncomplicated Firewall. It's user-friendly and is built on top of the iptables framework like nftables.

Finally, there is `FirewallD` which is known for being a dynamic, powerful and flexible solution that can support complex configurations.

## iptables
Its main components are:

| **Component** | **Description**                                                                                                                                                              |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Tables`      | Tables are used to organize and categorize firewall rules.                                                                                                                   |
| `Chains`      | Chains are used to group a set of firewall rules applied to a specific type of network traffic.                                                                              |
| `Rules`       | Rules define the criteria for filtering network traffic and the actions to take for packets that match the criteria.                                                         |
| `Matches`     | Matches are used to match specific criteria for filtering network traffic, such as source or destination IP addresses, ports, protocols, and more.                           |
| `Targets`     | Targets specify the action for packets that match a specific rule. For example, targets can be used to accept, drop, or reject packets or modify the packets in another way. |
### Tables
| **Table Name** | **Description**                                                             | **Built-in Chains**                             |
| -------------- | --------------------------------------------------------------------------- | ----------------------------------------------- |
| `filter`       | Used to filter network traffic based on IP addresses, ports, and protocols. | INPUT, OUTPUT, FORWARD                          |
| `nat`          | Used to modify the source or destination IP addresses of network packets.   | PREROUTING, POSTROUTING                         |
| `mangle`       | Used to modify the header fields of network packets.                        | PREROUTING, OUTPUT, INPUT, FORWARD, POSTROUTING |
| `raw`          | Used to configure special packet processing options.                        | PREROUTING, OUTPUT                              |
### Chains
Built-in `filter` chains:
- INPUT: filter incoming network traffic
- OUTPUT: filter outgoing network traffic
- FORWARD: filter traffic forwarded between different network interfaces
Built-in `nat` chains:
- PREROUTING: modify the destination IP address of incoming packets before the routing table processes them
- POSTROUTING: modify the source IP address of outgoing packets after the routing table has processed them
Built-in `mangle` chains: PREROUTING, OUTPUT, INPUT, FORWARD and POSTROUTING which are used to modify the header fields of packets.

There are also user-defined chains which can simplify management by grouping firewall rules based on specific criteria, such as source IP address, destination port, or protocol and they can be added to any of the 3 main tables (filter, nat and mangle).

### Rules and Targets
|**Target Name**|**Description**|
|---|---|
|`ACCEPT`|Allows the packet to pass through the firewall and continue to its destination|
|`DROP`|Drops the packet, effectively blocking it from passing through the firewall|
|`REJECT`|Drops the packet and sends an error message back to the source address, notifying them that the packet was blocked|
|`LOG`|Logs the packet information to the system log|
|`SNAT`|Modifies the source IP address of the packet, typically used for Network Address Translation (NAT) to translate private IP addresses to public IP addresses|
|`DNAT`|Modifies the destination IP address of the packet, typically used for NAT to forward traffic from one IP address to another|
|`MASQUERADE`|Similar to SNAT but used when the source IP address is not fixed, such as in a dynamic IP address scenario|
|`REDIRECT`|Redirects packets to another port or IP address|
|`MARK`|Adds or modifies the Netfilter mark value of the packet, which can be used for advanced routing or other purposes|
For example: `sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT` adds an entry to the INPUT chain that allows incoming TCP traffic whose destination port is 22 (SSH) to be accepted.

### Matches
| **Match Name**          | **Description**                                                    |
| ----------------------- | ------------------------------------------------------------------ |
| `-p` or `--protocol`    | Specifies the protocol to match (e.g. tcp, udp, icmp)              |
| `--dport`               | Specifies the destination port to match                            |
| `--sport`               | Specifies the source port to match                                 |
| `-s` or `--source`      | Specifies the source IP address to match                           |
| `-d` or `--destination` | Specifies the destination IP address to match                      |
| `-m state`              | Matches the state of a connection (e.g. NEW, ESTABLISHED, RELATED) |
| `-m multiport`          | Matches multiple ports or port ranges                              |
| `-m tcp`                | Matches TCP packets and includes additional TCP-specific options   |
| `-m udp`                | Matches UDP packets and includes additional UDP-specific options   |
| `-m string`             | Matches packets that contain a specific string                     |
| `-m limit`              | Matches packets at a specified rate limit                          |
| `-m conntrack`          | Matches packets based on their connection tracking information     |
| `-m mark`               | Matches packets based on their Netfilter mark value                |
| `-m mac`                | Matches packets based on their MAC address                         |
| `-m iprange`            | Matches packets based on a range of IP addresses                   |

# System Logs
Types of system logs on Linux:
- Kernel Logs
- System Logs
- Authentication Logs
- Application Logs
- Security Logs

## Kernel Logs
They contain info about the kernel like hardware drivers, system calls, and kernel events and are stored in the `/var/log/kern.log` file. These logs can give important info about vulnerable drivers, system crashes, resource limitations, suspicious system calls and other activities that could indicate the presence of malware.

## System Logs
They contain info about system-level events such as service starts/stops, login attempts and system reboots. These logs are stored in `/var/log/syslog`.

## Authentication Logs
They contain info about user authentication attempts. While the `syslog` file can store similar login information, the `/var/log/auth.log` file (where authentication logs are stored on Ubuntu) specifically focuses on user authentication attempts, making it a more valuable resource for identifying potential security threats.

## Application Logs
They contain info about specific applications and are often stored in the files of these applications like `/var/log/apache2/error.log` or `/var/log/mysql/error.log`. By examining these logs we can identify vulnerabilities in the application.

There are many types of application logs including access and audit logs. Access logs keep a record of user and process activity on the system, including login attempts, file accesses, and network connections.

Audit logs record information about security-relevant events on the system, such as modifications to system configuration files or attempts to modify system files or settings.

Locations of access logs on common services:

| **Service**  | **Description**                                                                                               |
| ------------ | ------------------------------------------------------------------------------------------------------------- |
| `Apache`     | Access logs are stored in the `/var/log/apache2/access.log` file (or similar, depending on the distribution). |
| `Nginx`      | Access logs are stored in the `/var/log/nginx/access.log` file (or similar).                                  |
| `OpenSSH`    | Access logs are stored in the `/var/log/auth.log` file on Ubuntu and in `/var/log/secure` on CentOS/RHEL.     |
| `MySQL`      | Access logs are stored in the `/var/log/mysql/mysql.log` file.                                                |
| `PostgreSQL` | Access logs are stored in the `/var/log/postgresql/postgresql-version-main.log` file.                         |
| `Systemd`    | Access logs are stored in the `/var/log/journal/` directory.                                                  |

## Security Logs
These logs are recorded on different locations depending on the application/tool in use. 
Examples:
- Fail2ban: records failed login attempts in `/var/log/fail2ban.log`
- UFW firewall: records activity in `/var/log/ufw.log`
- Other general security events can be found in `/var/log/syslog` or `/var/log/auth.log`
Commands like `tail`, `grep`, and `sed` are helpful when searching and filtering logs.
