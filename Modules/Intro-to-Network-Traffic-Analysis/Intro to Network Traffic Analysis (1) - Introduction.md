# Network Traffic Analysis
Common traffic analysis tools

| **Tool**                | **Description**                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tcpdump`               | [tcpdump](https://www.tcpdump.org/) is a command-line utility that, with the aid of LibPcap, captures and interprets network traffic from a network interface or capture file.                                                                                                                                                                                                                                  |
| `TShark`                | [TShark](https://www.wireshark.org/docs/man-pages/tshark.html) is a network packet analyzer much like TCPDump. It will capture packets from a live network or read and decode from a file. It is the command-line variant of Wireshark.                                                                                                                                                                         |
| `Wireshark`             | [Wireshark](https://www.wireshark.org/) is a graphical network traffic analyzer. It captures and decodes frames off the wire and allows for an in-depth look into the environment. It can run many different dissectors against the traffic to characterize the protocols and applications and provide insight into what is happening.                                                                          |
| `NGrep`                 | [NGrep](https://github.com/jpr5/ngrep) is a pattern-matching tool built to serve a similar function as grep for Linux distributions. The big difference is that it works with network traffic packets. NGrep understands how to read live traffic or traffic from a PCAP file and utilize regex expressions and BPF syntax. This tool shines best when used to debug traffic from protocols like HTTP and FTP.  |
| `tcpick`                | [tcpick](http://tcpick.sourceforge.net/index.php?p=home.inc) is a command-line packet sniffer that specializes in tracking and reassembling TCP streams. The functionality to read a stream and reassemble it back to a file with tcpick is excellent.                                                                                                                                                          |
| `Network Taps`          | Taps ([Gigamon](https://www.gigamon.com/), [Niagra-taps](https://www.niagaranetworks.com/products/network-tap)) are devices capable of taking copies of network traffic and sending them to another place for analysis. These can be in-line or out of band. They can actively capture and analyze the traffic directly or passively by putting the original packet back on the wire as if nothing had changed. |
| `Networking Span Ports` | [Span Ports](https://en.wikipedia.org/wiki/Port_mirroring) are a way to copy frames from layer two or three networking devices during egress or ingress processing and send them to a collection point. Often a port is mirrored to send those copies to a log server.                                                                                                                                          |
| `Elastic Stack`         | The [Elastic Stack](https://www.elastic.co/elastic-stack) is a culmination of tools that can take data from many sources, ingest the data, and visualize it, to enable searching and analysis of it.                                                                                                                                                                                                            |
| `SIEMs`                 | `SIEMS` (such as [Splunk](https://www.splunk.com/en_us)) are a central point in which data is analyzed and visualized. Alerting, forensic analysis, and day-to-day checks against the traffic are all use cases for a SIEM.                                                                                                                                                                                     |
## BPF Syntax
Many network analysis tools utilize [Berkeley Packet Filter (BPF)](https://en.wikipedia.org/wiki/Berkeley_Packet_Filter) so knowing its [syntax](https://www.ibm.com/docs/en/qsip/7.4?topic=queries-berkeley-packet-filters) is very valuable for us.

## NTA Workflow
### 1. Ingest Traffic
Once we have decided on our placement, begin capturing traffic. Utilize capture filters if we already have an idea of what we are looking for.

### 2. Reduce Noise by Filtering
Capturing traffic of a link, especially one in a production environment, can be extremely noisy. Once we complete the initial capture, an attempt to filter out unnecessary traffic from our view can make analysis easier. (Broadcast and Multicast traffic, for example.)

### 3. Analyze and Explore
Now is the time to start carving out data pertinent to the issue we are chasing down. Look at specific hosts, protocols, even things as specific as flags set in the TCP header. The following questions will help us:
1. Is the traffic encrypted or plain text? Should it be?
2. Can we see users attempting to access resources to which they should not have access?
3. Are different hosts talking to each other that typically do not?

### 4. Detect and Alert
1. Are we seeing any errors? Is a device not responding that should be?
2. Use our analysis to decide if what we see is benign or potentially malicious.
3. Other tools like IDS and IPS can come in handy at this point. They can run heuristics and signatures against the traffic to determine if anything within is potentially malicious.

### 5. Fix and Monitor
Fix and monitor is not a part of the loop but should be included in any workflow we perform. If we make a change or fix an issue, we should continue to monitor the source for a time to determine if the issue has been resolved.

# Networking Primer - Layers 1-4
Here is a refresher on OSI vs TCP/IP and protocols, PDUs at each layer.  
<img width="2576" height="1533" alt="Pasted image 20250828200330" src="https://github.com/user-attachments/assets/fff259d8-a697-4199-a151-c31741551335" />  
Below is an example of a PDU in Wireshark. It shows us the PDU in the order that it was unencapsulated.  
<img width="2064" height="1096" alt="Pasted image 20250828200519" src="https://github.com/user-attachments/assets/352e2bfa-2d26-4880-9b8c-3e5348b3b1d0" />  

## TCP Three-way Handshake
This handshake is the way TCP establishes a connection between 2 hosts. This depends on 2 flags in the TCP header called the SYN flag and the ACK flag.
1. The client sends a packet with the SYN flag set to on along with other negotiable options in the TCP header.
    1. This is a synchronization packet. It will only be set in the first packet from host and server and enables establishing a session by allowing both ends to agree on a sequence number to start communicating with.
    2. This is crucial for the tracking of packets. Along with the sequence number sync, many other options are negotiated in this phase to include window size, maximum segment size, and selective acknowledgments.
	
2. The server will respond with a TCP packet that includes a SYN flag set for the sequence number negotiation and an ACK flag set to acknowledge the previous SYN packet sent by the host.
    1. The server will also include any changes to the TCP options it requires set in the options fields of the TCP header.
	
3. The client will respond with a TCP packet with an ACK flag set agreeing to the negotiation.
    1. This packet is the end of the three-way handshake and establishes the connection between client and server.

<img width="2416" height="888" alt="Pasted image 20250828201941" src="https://github.com/user-attachments/assets/a3cb527c-f7c4-4b2f-bbe7-631f5b40ee6d" />  
Notice in this output the three way handshake with SYN, SYN ACK then ACK. The numbers underlined in green are port numbers. In the first line the source port is 57678 which is a random high number used by the client and the destination port is 80 which is the port that HTTP listens on. When the server responds it does with the same ports just swapped.

### Session Teardown  
There is another handshake which is used to terminate the connection. It utilizes the FIN and ACK flags. At the end of the session when data is fully received the client sends a message with the FIN flag. The server responds with ACK then sends its own FIN. Finally the client responds with ACK and the session is terminated.

# Networking Primer - Layers 5-7
## HTTP
Hypertext Transfer Protocol (HTTP) is a stateless Application Layer protocol that allows the client to ask the server for resources like HTML, images, hyperlinks or videos. It normally utilizes TCP port 80 or even 8000 in some cases and can be modified to use other ports or UDP.

### HTTP Methods
| **Method** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `HEAD`     | required, it's a safe method that requests a response from the server similar to a `GET` request except that the message body is not included. It is a great way to acquire more information about the server and its operational status.                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `GET`      | required, `GET` is the most common method used. It requests information and content from the server. For example, `GET http://10.1.1.1/Webserver/index.html` requests the index.html page from the server based on our supplied URI.                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `POST`     | optional, `POST` is a way to submit information to a server based on the fields in the request. For example, submitting a message to a Facebook post or website forum is a POST action. The actual action taken can vary based on the server, and we should pay attention to the response codes sent back to validate the action.                                                                                                                                                                                                                                                                                                                                          |
| `PUT`      | optional, `PUT` will take the data appended to the message and place it under the requested URI. If an item does not exist there already, it will create one with the supplied data. If an object already exists, the new PUT will be considered the most up-to-date, and the object will be modified to match. The easiest way to visualize the differences between PUT and POST is to think of it like this; PUT will create or update an object at the URI supplied, while POST will create child entities at the provided URI. The action taken can be compared with the difference between creating a new file vs. writing comments about that file on the same page. |
| `DELETE`   | optional, `DELETE` does as the name implies. It will remove the object at the given URI.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `TRACE`    | optional, Allows for remote server diagnosis. The remote server will echo the same request that was sent in its response if the `TRACE` method is enabled.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `OPTIONS`  | optional, The `OPTIONS` method can gather information on the supported HTTP methods the server recognizes. This way, we can determine the requirements for interacting with a specific resource or server without actually requesting data or objects from it.                                                                                                                                                                                                                                                                                                                                                                                                             |
| `CONNECT`  | optional, `CONNECT` is reserved for use with Proxies or other security devices like firewalls. `CONNECT` allows for tunneling over HTTP. (SSL tunnels)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
As a requirement by the standard only `HEAD` and `GET` must always work and exist. Other methods like `POST`, `PUT`, etc., can be enabled or disabled (like on a read-only page). For more info about HTTP see RFC:2616.

## HTTPS
HTTP Secure is a modification of HTTP designed to utilize TLS or SSL for better security and encryption. HTTPS runs on TCP port 443 and sometimes 8443.

### TLS Handshake
<img width="2416" height="886" alt="Pasted image 20250828205605" src="https://github.com/user-attachments/assets/353914b3-a1d6-453d-9338-0c9263e53cfe" />  
Since TLS (or HTTPS) runs on top of TCP we first need to establish a TCP session (see the blue box). Then the TLS handshake begins and the client and server exchange hello messages and start agreeing on connection parameters like session identifier, peer x509 certificate, compression algorithm to be used, the cipher spec encryption algorithm, if the session is resumable, and a 48-byte master secret shared between the client and server to validate the session.

Once the session is established any data sent will appear as Application Data. For more info on HTTPS and TLS see RFC:8446.

## FTP
File Transfer Protocol is an application layer protocol for quick data transfer. It can be accessed from the CLI, a web browser or a graphical client such as FileZilla. FTP is considered an insecure protocol so most users moved to better alternatives like SFTP.

FTP uses TCP port 20 for data transfer and uses port 21 for issuing commands to control the FTP session.

FTP has an active (default) and passive mode. In active mode the client connects to the server’s port 21 (control channel). When the client requests data (e.g., `LIST` or `RETR file.txt`), it sends a `PORT` command over the control channel. This tells the server: _“I’ve opened a listening port on my machine (say, port 49152). Please connect back to me there for the data channel"._ The server initiates a new TCP connection from its port 20 (data port) to the client’s chosen port. The problem is If the client is behind a firewall or NAT, the server can’t reach the client directly so data connection fails.

In passive mode, client connects to server’s port 21 (control channel) and sends a `PASV` command. The server responds with an IP address and a random port (e.g., “I’m listening on 192.168.1.5:21234”). The client initiates the data channel connection to the server’s given port. Since the client always initiates both channels, this works much better when the client is behind a firewall or NAT (common in modern networks).

Most common FTP commands over port 21:

|**Command**|**Description**|
|---|---|
|`USER`|specifies the user to log in as.|
|`PASS`|sends the password for the user attempting to log in.|
|`PORT`|when in active mode, this will change the data port used.|
|`PASV`|switches the connection to the server from active mode to passive.|
|`LIST`|displays a list of the files in the current directory.|
|`CWD`|will change the current working directory to one specified.|
|`PWD`|prints out the directory you are currently working in.|
|`SIZE`|will return the size of a file specified.|
|`RETR`|retrieves the file from the FTP server.|
|`QUIT`|ends the session.|
For more info on FTP, see RFC:959.

## SMB
Server Message Block is a protocol widely seen in windows enterprise environments that enables sharing resources like printers, shared drives, authentication servers, and more. It requires user authentication to access these resources. In the past, SMB utilized NetBIOS as its transport mechanism over UDP ports 137 and 138. Since modern changes, SMB now supports direct TCP transport over port 445, NetBIOS over TCP port 139, and even the QUIC protocol.
