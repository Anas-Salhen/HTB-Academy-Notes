# Network Layer
Layer 3 of OSI controls the exchange of data packets. It is responsible for logical addressing (assigning IPs) and routing. Most Used Protocols in this layer:
- `IPv4` / `IPv6`
- `IPsec`
- `ICMP`
- `IGMP`
- `RIP`
- `OSPF`

# IP Addresses
Each host on the network is identified by a Media Access Control address (MAC). This MAC address is good for layer 2 communications (same network) but isn't enough if the hosts are from different networks in which case we need layer 3 protocols IPv4 and/or IPv6.

## IPv4 Structure
Most common method of assigning IP addresses. An IPv4 address consists of a 32 bit binary number, each 8 bits forming a byte (also called an octet) for a total of 4 octets. Each octet can be translated into a decimal number (0-255) for readability. For Example, 01111111.00000000.00000000.00000001 would be 127.0.0.1 and so on. Each network interface is assigned a unique IP address.

The IP address has a network part then a host part. The host part is usually assigned by a router or an administrator. The network part on the internet is controlled by an organization called IANA.

All available IPv4s in the world are divided into the following classes.

| **`Class`** | **Network Address** | **First Address** | **Last Address** | **Subnetmask**       | **Subnets** | **IPs**        |
| ----------- | ------------------- | ----------------- | ---------------- | -------------------- | ----------- | -------------- |
| `A`         | 1.0.0.0             | 1.0.0.1           | 127.255.255.255  | 255.0.0.0 or /8      | 127         | 16,777,214 + 2 |
| `B`         | 128.0.0.0           | 128.0.0.1         | 191.255.255.255  | 255.255.0.0 or /16   | 16,384      | 65,534 + 2     |
| `C`         | 192.0.0.0           | 192.0.0.1         | 223.255.255.255  | 255.255.255.0 or /24 | 2,097,152   | 254 + 2        |
| `D`         | 224.0.0.0           | 224.0.0.1         | 239.255.255.255  | Multicast            | Multicast   | Multicast      |
| `E`         | 240.0.0.0           | 240.0.0.1         | 255.255.255.255  | reserved             | reserved    | reserved       |
The subnet mask determines which bits are network bits and which ones are host bits. I

n each of the first 3 classes we see 2 addresses counted separately in the IP column. These 2 are reserved addresses, network address and broadcast address.

Another important address to know is the default gateway (usually the address of the router) and it's common for this address to be the first or last assignable address in the network.

The last address of a network is the broadcast address and is used when a host wants to send a message to everyone in the network.

`Classless Inter-Domain Routing` (`CIDR`) is a method that makes network sizes more flexible.

# Subnetting
Dividing an address range into smaller address ranges is called subnetting. To get familiar with subnets and subnet masks take this example.

An IPv4 address `192.168.12.160` the subnet mask is `255.255.255.192`, can be referred to as `/26`. This means that the first 26 bits of the address are the network part and the rest is the host part. When converting 192.168.12.160 to binary we get (11000000.10101000.00001100.10)100000

But why did we translate the subnet mask 255.255.255.192 to /26? It's because when converting it to binary we get 11111111.11111111.11111111.11000000 the one bits here represent the network bits that we can't change and when counting them here you will see they are 26 bits.

The 6 host bits in this case are the ones that we are free to change. Since the network bits can't be changed, the range of usable IPs in this network is from 11000000.10101000.00001100.10(000000) to 11000000.10101000.00001100.10(111111), In decimal from 192.168.12.128 to 192.168.12.191

The first address (all 0s in the host part) in this range is reserved for the network address. The last address (all 1s in the host part) in this range is reserved for the broadcast address. So now that 192.168.12.128 and 192.168.12.191 are reserved, the remaining addresses (192.168.12.129 to 192.168.12.190) can be assigned to hosts. Therefore, this subnet offers 62 IPs (for hosts) + 2 IPs (network and broadcast), a total of 64 IPs.

## Subnetting Into Smaller Networks
Imagine we are asked to split this into 4 subnets. If we add 1 bit from the host to the network part we would get 2 subnets so we need to add 2 bits (since 2 squared is 4) from the host part to the network part (so now network is 28 bits and host is 4 bits). Our new subnets will be 192.168.12.128/28, 192.168.12.144/28, 192.168.12.160/28 and 192.168.12.176/28 to understand how we came up with them read them in binary.

# MAC Addresses 
Each network interface has its own Media Access Control (MAC) address. The MAC address is the physical address which operates on layer 2 of the OSI model and it consists of 48 bits (6 octets) represented in hexadecimal format. Example: in binary 11011110.10101101.10111110.11101111.00010011.00110111 each 4 bits from this MAC address form one hexadecimal character which translates to DEAD.BEEF.1337

The first half (3 bytes / 24 bits / 6 hexadecimal characters) is called the Organization Unique Identifier (OUI) which is defined for manufacturers by the Institute of Electrical and Electronics Engineers (IEEE). The second half is called Individual Address Part or Network Interface Controller (NIC) which is assigned to the device by the manufacturer.

If a host wants to communicate with a host within the same subnet it's delivered to the host with a MAC address that matches the destination MAC address of the message. If it's outside the subnet (IP from different subnet) then the message or ethernet frame is sent to the default gateway (a router) which then handles transporting it to that host using ARP protocol.

## Special Bits
In DEAD.BEEF.1337 the first octet DE = 11011110. The Second bit from the right in the first octet 110111(1)0 is called the U/L bit.
- 0: If it equals 0 then it's a universally administered address, globally unique and assigned by the vendor. 
- 1: If it's 1 then it's a locally administered address defined by some software or an admin.

The last bit in the first octet 1101111(0) is the I/G bit.
- 0: If it equals 0 then it's a unicast address (frames go to a single host)
- 1: If it equals 1 then it's a multicast address (frames are sent to multiple hosts)

There is also the MAC address where all bits are 1s, FFFF.FFFF.FFFF which is the broadcast MAC address. Frames sent to this address are broadcasted to every host in the same network of the sender.

## MAC Address Attacks
- `MAC spoofing`: This involves altering the MAC address of a device to match that of another device, typically to gain unauthorized access to a network. This attack uses tools like [Ettercap](https://github.com/Ettercap/ettercap) and [Cain & Abel](https://github.com/xchwarze/Cain).
- `MAC flooding`: This involves sending many packets with different MAC addresses to a network switch, causing it to reach its MAC address table capacity and effectively preventing it from functioning correctly.
- `MAC address filtering`: Some networks may be configured only to allow access to devices with specific MAC addresses that we could potentially exploit by attempting to gain access to the network using a spoofed MAC address.
## Address Resolution Protocol
Address Resolution Protocol (ARP) is a network protocol used when we want to know the MAC address of a device whose IP address is known to us. It uses 2 types of messages ARP requests and ARP replies.

### ARP Requests
The host broadcasts an ARP request (its destination IP is a broadcast IP address) and it specifies in it the IP whose MAC address is unknown.

### ARP Replies
When a device receives an ARP request that matches its IP address it replies to the sender with an ARP reply this time in a unicast not broadcast message. The message allows the requesting device to know the MAC address of the replying device. Example:
```shell-session
1   0.000000 10.129.12.100 -> 10.129.12.255 ARP 60  Who has 10.129.12.101?  Tell 10.129.12.100
2   0.000015 10.129.12.101 -> 10.129.12.100 ARP 60  10.129.12.101 is at AA:AA:AA:AA:AA:AA
```

# IPv6 Addresses
It's the successor to IPv4 however it has 128 bits instead on 32. Long term, IPv6 is expected to completely replace IPv4 but both can be used at the same time (Dual Stack).

| **Features**       | **IPv4**      | **IPv6**                     |
| ------------------ | ------------- | ---------------------------- |
| Bit length         | 32-bit        | 128 bit                      |
| OSI layer          | Network Layer | Network Layer                |
| Addressing range   | ~ 4.3 billion | ~ 340 undecillion            |
| Representation     | Binary        | Hexadecimal                  |
| Prefix notation    | 10.10.10.0/24 | fe80::dd80:b1a9:6687:2d3b/64 |
| Dynamic addressing | DHCP          | SLAAC / DHCPv6               |
| IPsec              | Optional      | Mandatory                    |
There are 3 types of IPv6 addresses:
- Unicast: to a single host
- Multicast: to multiple hosts, all of them receive the message
- Anycast: multiple hosts are eligible but only one receives the message (useful for load-balancing)
IPv6 doesn't broadcast however similar functions to broadcast can be achieved with multicast.

## Notation
IPv6 addresses consist of 128 bits / 16 bytes with that many numbers we need something more efficient than decimal notation which is why hexadecimal is used like: fe80:0000:0000:0000:dd80:b1a9:0687:2d3b/64. Each 4 hexadecimal numbers are separated by a colon. In RFC 5952, the aforementioned IPv6 address notation was defined:
- All alphabetical characters are always written in lower case.
- All leading zeros of a block are always omitted.
- One or more consecutive blocks of `4 zeros` (hex) are shortened by two colons (`::`).
- The shortening to two colons (`::`) may only be performed `once` starting from the left.
So the earlier example would be: fe80::dd80:b1a9:687:2d3b/64

