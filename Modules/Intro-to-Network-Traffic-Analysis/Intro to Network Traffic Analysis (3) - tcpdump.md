# Tcpdump Fundamentals
`tcpdump` is a command line packet sniffer built for Unix based systems and had a windows twin called WinDump. It uses the libraries `pcap` and `libpcap`, paired with an interface in promiscuous mode to listen for data.

Most common switches for it:

| **Switch Command** | **Result**                                                                                                                                                 |
| :----------------: | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
|         D          | Will display any interfaces available to capture from.                                                                                                     |
|         i          | Selects an interface to capture from. ex. -i eth0                                                                                                          |
|         n          | Do not convert addresses (i.e., host addresses, port numbers, etc.) to names.                                                                              |
|         e          | Will grab the ethernet header along with upper-layer data.                                                                                                 |
|         X          | Show Contents of packets in hex and ASCII.                                                                                                                 |
|         XX         | Same as X, but will also specify ethernet headers. (like using Xe)                                                                                         |
|     v, vv, vvv     | Increase the verbosity of output shown and saved.                                                                                                          |
|         c          | Grab a specific number of packets, then quit the program.                                                                                                  |
|         s          | Defines how much of a packet to grab.                                                                                                                      |
|         S          | change relative sequence numbers in the capture display to absolute sequence numbers. (13248765839 instead of 101)                                         |
|         q          | Print less protocol information.                                                                                                                           |
|    r file.pcap     | Read from a file.                                                                                                                                          |
|    w file.pcap     | Write into a file                                                                                                                                          |
|         l          | `-l` will line buffer instead of pooling and pushing in chunks. It allows us to send the output directly to another tool such as `grep` using a pipe `\|`. |

## Tcpdump Output
<img width="1261" height="629" alt="Pasted image 20250829145629" src="https://github.com/user-attachments/assets/983c428e-4a32-42d9-a0ac-eaa39feb6326" />  
Output explained:

| **Filter**                           | **Result**                                                                                                                                                                                                                                                                                       |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Timestamp                            | Yellow, The timestamp field comes first and is configurable to show the time and date in a format we can ingest easily.                                                                                                                                                                          |
| Protocol                             | Orange, This section will tell us what the upper-layer header is. In our example, it shows IP.                                                                                                                                                                                                   |
| Source & Destination IP.Port         | Orange, This will show us the source and destination of the packet along with the port number used to connect. Format == `IP.port == 172.16.146.2.21`                                                                                                                                            |
| Flags                                | Green, This portion shows any flags utilized.                                                                                                                                                                                                                                                    |
| Sequence and Acknowledgement Numbers | Red, This section shows the sequence and acknowledgment numbers used to track the TCP segment. Our example is utilizing low numbers to assume that relative sequence and ack numbers are being displayed.                                                                                        |
| Protocol Options                     | Blue, Here, we will see any negotiated TCP values established between the client and server, such as window size, selective acknowledgments, window scale factors, and more.                                                                                                                     |
| Notes / Next Header                  | White, Misc notes the dissector found will be present here. As the traffic we are looking at is encapsulated, we may see more header information for different protocols. In our example, we can see the TCPDump dissector recognizes FTP traffic within the encapsulation to display it for us. |
The amount of output and details depend on verbosity.

# Tcpdump Packet Filtering
Helpful filters:

| **Filter**           | **Result**                                                                                               |
| -------------------- | -------------------------------------------------------------------------------------------------------- |
| host                 | `host` will filter visible traffic to show anything involving the designated host. Bi-directional        |
| src / dst            | `src` and `dst` are modifiers. We can use them to designate a source or destination host or port.        |
| net                  | `net` will show us any traffic sourcing from or destined to the network designated. It uses / notation.  |
| proto                | will filter for a specific protocol type. (ether, TCP, UDP, and ICMP as examples)                        |
| port                 | `port` is bi-directional. It will show any traffic with the specified port as the source or destination. |
| portrange            | `portrange` allows us to specify a range of ports. (0-1024)                                              |
| less / greater "< >" | `less` and `greater` can be used to look for a packet or protocol option of a specific size.             |
| and / &&             | `and` `&&` can be used to concatenate two different filters together. for example, src host AND port.    |
| or                   | `or` allows for a match on either of two conditions. It does not have to meet both. It can be tricky.    |
| not                  | `not` is a modifier saying anything but x. For example, not UDP.                                         |
Filters can utilize the common protocol name or protocol number for any IP, IPv6, or Ethernet protocol. Common examples would be TCP(6), UDP(17), or ICMP(1). The following commands produce the same output.
```shell-session
sudo tcpdump -i eth0 udp
```

```shell-session
sudo tcpdump -i eth0 proto 17
```

For protocols like DNS which uses both TCP and UDP on port 53 we can filter for traffic on one of them with `tcp port 53` or we can get both DNS TCP and UDP traffic with `port 53`.

```shell-session
sudo tcpdump -i eth0 'tcp[13] &2 != 0'
```
This filter captures only TCP packets with the SYN flag set and it's written in BPF syntax.
