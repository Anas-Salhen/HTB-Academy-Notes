# Network Configuration
## Network Access Control (NAC)
Key NAC modules include:
- Discretionary Access Control (`DAC`): The owner of the resource sets permissions for who can access it.

- Mandatory Access Control (`MAC`): Permissions are enforced by the operating system, not the owner of the resource, making it more secure but less flexible. Each resource has a security label the identifies its security level and each user or process has a security clearance that identifies its level. The user is granted access if their security level is greater than or equal to that of the resource.

- Role-Based Access Control (`RBAC`): Permissions are assigned based on roles within an organization, making it easier to manage user privileges.

Tools such as `syslog`, `rsyslog`, `ss` (for socket statistics), `lsof` (to list open files), and the `ELK stack` (Elasticsearch, Logstash, and Kibana) can be used to monitor and analyze network traffic.

## Configuring Network Interfaces
`ifconfig` and `ip` commands allows us to view a lot of info on our network interfaces like IP addresses, netmasks, and status. 
Although `ifconfig` has been deprecated in newer versions of Linux and replaced by `ip` which offers more advanced features.

To activate a network interface we can use either of these commands to activate a specific interface (like eth0):
`sudo ifconfig eth0 up` or `sudo ip link set eth0 up`

This command assigns the ip address 192.168.1.2 to the interface eth0: 
`sudo ifconfig eth0 192.168.1.2`

This command assigns the netmask 255.255.255.0 to the interface eth0:
`sudo ifconfig eth0 netmask 255.255.255.0`

This command makes 192.168.1.1 the default gateway for the interface eth0:
`sudo route add default gw 192.168.1.1 eth0`

To set a DNS server (like Google's DNS server - 8.8.8.8), first open the `/etc/resolv.conf` file: `sudo vim /etc/resolv.conf` then type the following in the file:  
```txt
nameserver 8.8.8.8
```

Note that changes made to the `/etc/resolv.conf` can be overwritten by services like NetworkManager or systemd-resolved. To make permanent changes edit the `/etc/network/interfaces` file:
```txt
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```
Once making these changes save the text file then restart the networking service using:
`sudo systemctl restart networking`

## Troubleshooting
Some of the commonly used tools:
1. Ping
2. Traceroute
3. Netstat
4. Tcpdump
5. Wireshark
6. Nmap

### Ping
Tests connectivity by sending signals called packets to a host and measuring whether they reach or not and the time they take. For example to ping Google's DNS server use: `ping 8.8.8.8`

### traceroute
Similar to ping except it shows you the route your message took to reach its destination.

### netstat
Shows info about active network connections like protocol, number of bytes sent/received, ip address, port numbers and connection state.

### Common issues and causes
Common network issues:
- Network connectivity issues
- DNS resolution issues (it's always about DNS)
- Loss of data packets
- Network performance issues
Common causes for them:
- Incorrectly configured firewalls or routers,
- damaged network cables or connections,
- incorrect network settings,
- hardware failures,
- incorrect DNS server settings or DNS server failures
- incorrectly configured DNS entries,
- network congestion,
- outdated network hardware or incorrectly configured network settings,
- unpatched software or firmware and missing security controls.

## Hardening
- Security-Enhanced Linux (SELinux): Is a Mandatory Access Control (MAC) system that is integrated into the Linux kernel.

- AppArmor: Like SELinux it's a MAC system however it's simpler and more user friendly, although it doesn't provide the same level of fine-grained control.

- TCP wrappers: When a request is made it intercepts it and checks the request against a list of allowed or blocked ip addresses. While it doesn't provide the same control of AppArmor or SELinux it's useful for basic network-level protection. 

# Remote Desktop Protocols in Linux
These protocols allow you to control a remote system as if you were sitting in front of the computer. Two of the most common protocols for remote access are:
1. Remote Desktop Protocol (RDP): primarily used in windows environments.
2. Virtual Network Computing (VNC): popular in Linux environments although it's also cross platform.

## X11
X11 is a protocol that applications use to communicate with the computer and provide it with instructions (like draw a button here or an icon there) which are used to display graphics. It's very common in Unix/Linux based systems. 
This protocol while useful for displaying graphics on the same computer, it can also be used to display the graphics from one computer on another, remotely. 

The main disadvantage of X11 is unencrypted data transmission which can be solved by SSH tunneling. For this purpose we need to set the  X11Forwarding option to yes in the `/etc/ssh/sshd_config` file.

Now we can start an application like firefox remotely using the following command.
`ssh -X htb-student@10.129.23.11 /usr/bin/firefox`
`-X`: enables X11Forwarding.
`htb-student@10.129.23.11`: logs into the remote machine (10.129.23.11) as the user (htb-student).
`/usr/bin/firefox`: instead of opening a shell it opens firefox.

### X11 Security
We should pay attention for the TCP ports (6000-6010) used by X11. Without proper security measures an attacker can use an open X server (it's the user-side part of X11) to expose sensitive data allowing them to see the windows/graphics being transmitted.

## XDMCP
 X Display Manager Control Protocol (XDMCP) is a protocol that can be used to gain access to another desktop remotely. Note that this protocol is insecure and can be exploited using attacks like man in the middle. The attacker can intercept communications in order to gain unauthorized access to the remote system.

## VNC
Virtual Network Computing (VNC) is a system based on the RFB protocol that allows controlling a computer remotely, it can also be used for screen sharing. It's considered to be secure and it uses encryption and requires user authentication.

There are 2 different ways to use VNC. The first one allows you to control the remote desktop as if you were sitting in front of it. But since the original mouse and keyboard of that computer remain usable 2 people can use it together. Therefore it's recommended that they agree about who is in control.
The second way is to make you login to a separate virtual session.

VNC is available on all OSs which makes it popular in IT.

The VNC server's display 0 listens on TCP port 5900. Other displays can be added like display 1 on port 5901, display 2 on port 5902 and so on.

Popular VNC implementations include:
- [TigerVNC](https://tigervnc.org/)
- [TightVNC](https://www.tightvnc.com/)
- [RealVNC](https://www.realvnc.com/en/)
- [UltraVNC](https://uvnc.com/)
UltraVNC and RealVNC are known for having higher security.

In the following example we set up TigerVNC.
```shell-session
sudo apt install xfce4 xfce4-goodies tigervnc-standalone-server -y
```
This command installs  VNC and XFCE4 (a system that provides GUI for the desktop) since VNC connections with GNOME aren't really stable.

During installation a hidden folder `.vnc` is created in the home directory. We have to create 2 more files, `xstartup` which determines how the VNC session is created and `config` which determines the settings.
First create the files: `touch ~/.vnc/xstartup ~/.vnc/config`
Then use: `cat <<EOT >> ~/.vnc/xstartup`
`cat` normally just views the file but in this case it allows us to append text line by line to the end of the file until we type EOT. Type this:
```shell-session
#!/bin/bash
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
/usr/bin/startxfce4
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
x-window-manager &
EOT
```

Then do the same with the other file:
```shell-session
username@htb:~$ cat <<EOT >> ~/.vnc/config

geometry=1920x1080
dpi=96
EOT
```

Change permissions to make `xstartup` executable by the service: `chmod +x ~/.vnc/xstartup`
To start the VNC server: `vncserver`
To display VNC sessions: `vncserver -list`
To make it more secure it's recommended to use an SSH tunnel then you can use `xtightvncviewer` to connect to that tunnel.
