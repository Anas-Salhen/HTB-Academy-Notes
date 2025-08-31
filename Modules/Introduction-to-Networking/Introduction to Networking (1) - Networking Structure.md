# Intro
URL vs FQDN:
- an `FQDN` (`www.hackthebox.eu`) only specifies the address of the website
- an `URL` (`https://www.hackthebox.eu/example?floor=2&office=dev&employee=17`) is more specific

## Good Security Practices
1. The Web Server should be in a DMZ (Demilitarized Zone) because clients on the internet can initiate communications with the website, making it more likely to become compromised. Placing it in a separate network allows the administrators to put networking protections between the web server and other devices.
    
2. Workstations should be on their own network, and in a perfect world, each workstation should have a Host-Based Firewall rule preventing it from talking to other workstations. If a Workstation is on the same network as a Server, networking attacks like `spoofing` or `man in the middle` become much more of an issue.
    
3. The Switch and Router should be on an "Administration Network." This prevents workstations from snooping in on any communication between these devices. I have often performed a Penetration Test and saw `OSPF` (Open Shortest Path First) advertisements. Since the router did not have a `trusted network`, anyone on the internal network could have sent a malicious advertisement and performed a `man in the middle` attack against any network.
    
4. IP Phones should be on their own network. Security-wise this is to prevent computers from being able to eavesdrop on communication. In addition to security, phones are unique in the sense that latency/lag is significant. Placing them on their own network can allow network administrators to prioritize their traffic to prevent high latency more easily.
    
5. Printers should be on their own network. This may sound weird, but it is next to impossible to secure a printer. Due to how Windows works, if a printer tells a computer authentication is required during a print job, that computer will attempt an `NTLMv2` authentication, which can lead to passwords being stolen. Additionally, these devices are great for persistence and, in general, have tons of sensitive information sent to them.

# Network Types
| **Network Type**                   | **Definition**                               |
| ---------------------------------- | -------------------------------------------- |
| Wide Area Network (WAN)            | Internet                                     |
| Local Area Network (LAN)           | Internal Networks (Ex: Home or Office)       |
| Wireless Local Area Network (WLAN) | Internal Networks accessible over Wi-Fi      |
| Virtual Private Network (VPN)      | Connects multiple network sites to one `LAN` |
## WAN
The WAN (Wide Area Network) is commonly referred to as `The Internet`. Networking equipment often have an address for WAN communications and an address for LAN communications. 

However it isn't only internet, many large companies or government agencies will have an "Internal WAN" (also called Intranet, Airgap Network, etc.). Traits of WAN networks include using a WAN Specific routing protocol such as BGP and an IP schema that isn't within RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

## LAN / WLAN
LANs and WLANs will typically assign IP Addresses designated for local use (RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

## VPN
### Site-To-Site VPN
Both the client and server are Network Devices and share entire network ranges. This is most commonly used to join company networks together over the Internet, allowing multiple locations to communicate over the Internet as if they were local.
### Remote Access VPN
This involves the client's computer creating a virtual interface that behaves as if it is on a client's network. HTB uses something called split-tunnel VPN which is good for HTB but other companies don't really use it for a number of reasons.
- **Split-Tunnel VPN**
    - Only traffic for specific networks goes through the VPN tunnel.
    - Example: Only `10.10.10.0/24` routes through the VPN, while all other traffic (YouTube, Google, etc.) still uses your normal Internet connection.
    - ✅ Advantage for HTB: You get lab access without worrying about HTB monitoring all your browsing.
    - ❌ Bad for companies: If malware runs on your PC, its malicious traffic might go directly to the Internet, bypassing company monitoring.
- **Full-Tunnel VPN**
    - All traffic (both lab/company and general Internet traffic) is routed through the VPN.
    - ✅ Better for companies (stronger monitoring and protection).
    - ❌ For HTB users, it could slow things down and raise privacy concerns since all browsing goes through their servers.
### SSL VPN
A VPN that is done within the web browser

## Other Network Types
| Network Type                          | Definition                        |
| ------------------------------------- | --------------------------------- |
| Global Area Network (GAN)             | Global network (the Internet)     |
| Metropolitan Area Network (MAN)       | Regional network (multiple LANs)  |
| Wireless Personal Area Network (WPAN) | Personal network (like Bluetooth) |
## GAN
A worldwide network such as the `Internet` is known as a `Global Area Network` (`GAN`). Internationally active companies also maintain isolated networks that span several `WAN`s and connect company computers worldwide. `GAN`s use the glass fibers infrastructure of wide-area networks and interconnect them by international undersea cables or satellite transmission.

## MAN
Covers a city or metro region (bigger than a LAN, smaller than a WAN). Often uses fiber optics and high-performance routers to achieve LAN-like speeds over city-wide distances. Usually run by **telecom operators** (since building fiber infrastructure across a city is expensive) and can be linked to WANs and GANs.

# Networking Topologies
Network topologies in terms of:
1. Connections
	- Wired: Coaxial cabling, Glass fiber cabling, Twisted-pair cabling, etc.
	- Wireless: Wi-Fi, Cellular, Satellite, etc. 
2. Nodes - Network Interface Controller (NICs)
	- Repeaters
	- Router/Modem
	- Hubs
	- Gateways
	- Bridges
	- Firewalls
	- Switches
3. Classifications
	- Point-to-Point
	- Bus
	- Star
	- Ring
	- Mesh
	- Tree
	- Hybrid
	- Daisy Chain

## Point-to-Point
<img width="2576" height="684" alt="Pasted image 20250824174207" src="https://github.com/user-attachments/assets/b88f9ad3-e41a-4497-a500-4eacab4875b1" />  


## Bus
All hosts are connected via a transmission medium like a cable. Since the medium is shared with all the others, only `one host can send`, and all the others can only receive and evaluate the data and see whether it is intended for itself.  
<img width="2576" height="1294" alt="Pasted image 20250824174346" src="https://github.com/user-attachments/assets/985a9898-53a6-4f87-a8c9-a32673822064" />  


## Star
<img width="2576" height="1707" alt="Pasted image 20250824174418" src="https://github.com/user-attachments/assets/f4004259-0d07-4a67-ae17-3ea0f911fb91" />  


## Ring
In a physical ring, computers (nodes) are connected in a circle with 2 cables for each computer one for incoming data and one for outgoing data. Transmission isn't controlled by a network device but rather a protocol that all nodes adhere.

In a logical ring, nodes are physically connected like a star to a central network device. That device forwards data from one node to the next in sequence, simulating a ring. 

To manage transmission, data flows only in one direction along with a token that determines which node will transmit data (the one holding it). Then the token is forwarded to the next node in the sequence.  
<img width="2576" height="1746" alt="Pasted image 20250824175424" src="https://github.com/user-attachments/assets/5fc4c5b2-f227-4101-aa07-fc7d507c620c" />  


## Mesh
In a fully meshed structure, each host is connected to every other host in the network. Primarily used in `WAN` or `MAN` to ensure high reliability, bandwidth and alternative routes in case of a failure.

In a partially meshed structure, specific nodes are connected to exactly one other node (like endpoints), and some other nodes are connected to two or more other nodes with a point-to-point connection.  
<img width="2576" height="2024" alt="Pasted image 20250824175741" src="https://github.com/user-attachments/assets/e23615fb-342a-469e-84af-57336c40b4a6" />  


## Tree
More like an extended star topology. They can be found in large company buildings, broadband networks and MAN networks.  
<img width="2576" height="1715" alt="Pasted image 20250824180022" src="https://github.com/user-attachments/assets/afc09e82-8a20-406d-b803-7534a8414aae" />  


## Hybrid
Combines two different topology types.  
<img width="2576" height="1726" alt="Pasted image 20250824180136" src="https://github.com/user-attachments/assets/90558b98-e0aa-4ea5-a15e-c41afdf8a3f1" />  


## Daisy Chain
<img width="2576" height="1716" alt="Pasted image 20250824180208" src="https://github.com/user-attachments/assets/aa13a46b-22b7-405f-bc3c-2cc41c0bc085" />  


# Proxies
Many people have different opinions on what a proxy is:
- Security Professionals jump to `HTTP Proxies` (BurpSuite) or pivoting with a `SOCKS/SSH Proxy` (`Chisel`, `ptunnel`, `sshuttle`).
- Web Developers use proxies like Cloudflare or ModSecurity to block malicious traffic.
- Average people may think a proxy is used to obfuscate your location and access another country's Netflix catalog.
- Law Enforcement often attributes proxies to illegal activity.

Not all of these examples are correct. A proxy is when a device or service sits in the middle of a connection and acts as a mediator. The mediator is the critical piece of information because it means the device in the middle must be able to inspect the contents of the traffic which means that it operates on L7 of the OSI model.

The average person idea of a proxy is that it changes their IP address and obfuscates their location which is incorrect. What does that is a VPN not a proxy.

## Dedicated Proxy / Forward Proxy
What most people imagine a proxy to be, a client makes a request to a computer which carries out the request. For example, in corporate networks sensitive computers typically go through a proxy instead of interacting with the internet directly.

`Winsock` is a Microsoft API that lets applications talk to the network in a standardized way. Web browsers (like Internet Explorer, Edge, or Chrome) that rely on it obey system proxy settings by default. This is a problem, if the malware also uses `Winsock` it will be aware of how to deal with the proxy.

On the other hand, Firefox depends on `libcurl` not `Winsock` so in this case if the malware is going to bypass proxy it needs to be configured with additional code.

An example of forward proxy is Burp Suite which can be configured to be a reverse proxy or transparent.  
<img width="2576" height="1496" alt="Pasted image 20250825061358" src="https://github.com/user-attachments/assets/f0e3aa0a-4461-4723-97cd-83282c684d3c" />  


## Reverse Proxy
It's the opposite of forward proxy, instead of filtering outgoing requests it filters incoming ones.

Many organizations use CloudFlare to protect them from DDoS Attacks.

Pentesters configure reverse proxies on infected devices. The infected device will listen on a port and any client that connects to it will be secretly forwarded to the attacker. This helps bypass firewalls and evade detection by things like an Intrusion Detection System (IDS) since traffic looks like it's going to a normal company device.

Another common use for reverse proxy is in Web Application Firewalls (WAFs). They inspect web requests and block them if they are malicious.  
<img width="2576" height="1526" alt="Pasted image 20250825062348" src="https://github.com/user-attachments/assets/8d180228-4d34-4e88-974a-734b4c16827a" />  


## (Non-) Transparent Proxy
These proxies can act in one of two ways:
1. `Transparent proxies`: the client (user) doesn't know they exist, they just do their job.
2. `Non-transparent proxies`: the client must configure the proxy and is aware of its existence.
