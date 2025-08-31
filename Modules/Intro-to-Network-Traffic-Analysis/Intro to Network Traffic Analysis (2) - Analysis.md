# The Analysis Process
## Analysis Dependencies 
Traffic capturing and analysis can be performed in two different ways, active or passive. Each has its dependencies. With passive, we are just copying data that we can see without directly interacting with the packets. Active capture requires us to take a more hands-on approach.

| **Dependencies**                          | **Passive** | **Active** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------------------------- | ----------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Permission`                              | Yes         | Yes        | Depending on the organization we are working in, capturing data can be against policy or even against the law in some sensitive areas like healthcare or banking. Be sure always to obtain permission in writing from someone with the proper authority to grant it to you. We may style ourselves as hackers, but we want to stay in the light legally and ethically.                                                                                                                                                                                                                                                                                                                                                                                                            |
| `Mirrored Port`                           | Yes         | No         | A switch or router network interface configured to copy data from other sources to that specific interface, along with the capability to place your NIC into promiscuous mode. Having packets copied to our port allows us to inspect any traffic destined to the other links we could normally not have visibility over. Since VLANs and switch ports will not forward traffic outside of their broadcast domain, we have to be connected to the segment or have that traffic copied to our specific port. When dealing with wireless, passive can be a bit more complicated. We must be connected to the SSID we wish to capture traffic off of. Just passively listening to the airwaves around us will present us with many SSID broadcast advertisements, but not much else. |
| `Capture Tool`                            | Yes         | Yes        | A way to ingest the traffic. A computer with access to tools like TCPDump, Wireshark, Netminer, or others is sufficient. Keep in mind that when dealing with PCAP data, these files can get pretty large quickly. Each time we apply a filter to it in tools like Wireshark, it causes the application to parse that data again. This can be a resource-intensive process, so make sure the host has abundant resources.                                                                                                                                                                                                                                                                                                                                                          |
| `In-line Placement`                       | No          | Yes        | Placing a Tap in-line requires a topology change for the network you are working in. The source and destination hosts will not notice a difference in the traffic, but for the sake of routing and switching, it will be an invisible next hop the traffic passes through on its way to the destination.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `Network Tap or Host With Multiple NIC's` | No          | Yes        | A computer with two NIC's, or a device such as a Network Tap is required to allow the data we are inspecting to flow still. Think of it as adding another router in the middle of a link. To actively capture the traffic, we will be duplicating data directly from the sources. The best placement for a tap is in a layer three link between switched segments. It allows for the capture of any traffic routing outside of the local network. A switched port or VLAN segmentation does not filter our view here.                                                                                                                                                                                                                                                             |
| `Storage and Processing Power`            | Yes         | Yes        | You will need plenty of storage space and processing power for traffic capture off a tap. Much more traffic is traversing a layer three link than just inside a switched LAN. Think of it like this; When we passively capture traffic inside a LAN, it's like pouring water into a cup from a water fountain. It's a steady stream but manageable. Actively grabbing traffic from a routed link is more like using a water hose to fill up a teacup. There is a lot more pressure behind the flow, and it can be a lot for the host to process and store.                                                                                                                                                                                                                        |

# Analysis in Practice
## Descriptive Analysis
1. What is the issue?
    - Suspected breach? Networking issue?
2. Define our scope and the goal. (what are we looking for? which time period?)
    - Target: multiple hosts potentially downloading a malicious file from bad.example.com
    - When: within the last 48 hours + 2 hours from now.
    - Supporting info: filenames/types 'superbad.exe' 'new-crypto-miner.exe'
3. Define our target(s) (net / host(s) / protocol)
    - Scope: 192.168.100.0/24 network, protocols used were HTTP and FTP.

## Diagnostic Analysis
4. Capture network traffic
    - Plug into a link with access to the 192.168.100.0/24 network to capture live traffic to try and grab one of the executables in transfer. See if an admin can pull PCAP and/or netflow data from our SIEM for the historical data.
5. Identification of required network traffic components (filtering)
    - Once we have traffic, filter out any packets not needed for this investigation to include; any traffic that matches our common baseline and keep anything relevant to the scope of the investigation. For example, HTTP and FTP from the subnet, anything transferring or containing a GET request for the suspected executable files.
6. An understanding of captured network traffic
    - Once we have filtered out the noise, it is time to dig for our targets—filter on things like `ftp-data` to find any files transferred and reconstruct them. For HTTP, we can filter on `http.request.method == "GET"` to see any GET requests that match the filenames we are searching for. This can show us who has acquired the files and potentially other transfers internal to the network on the same protocols.

## Predictive Analysis
7. Note-taking and mind mapping of the found results
    - Annotating everything we do, see, or find throughout the investigation is crucial. Ensure we are taking ample notes, including:
	    - Timeframes we captured traffic during.
	    - Suspicious hosts within the network.
	    - Conversations containing the files in question. ( to include timestamps and packet numbers)
8. Summary of the analysis (what did we find?)
    - Finally, summarize what we have found explaining the relevant details so that superiors can decide to quarantine the affected hosts or perform more significant incident response.
    - Our analysis will affect decisions made, so it is essential to be as clear and concise as possible. 

## Prescriptive Analysis
Using the results of our workflow, we can make sound decisions as to what actions are required to solve the problem and prevent it from happening again.

## Key Components of an Effective Analysis
1. Know your environment
2. Placement is Key: closest to the source of the issue is the ideal placement of our capturing tool.
3. Persistence: don't lose the drive to find the problem. It could mean the difference between stopping the attacker and a full-scale breach like a ransomware attack.

## Analysis Approach
Start with standard protocols first, most attacks will come from the internet. HTTP/S, FTP, E-mail, and basic TCP and UDP traffic will be the most common things so start with them and clear anything unnecessary to the investigation then work your way into protocols and things that are more specific. Be mindful of the security policy of the network. Does our organization allow for RDP sessions that are initiated outside the enterprise? What about the use of Telnet?

Look for patterns. Is a specific host or set of hosts checking in with something on the internet at the same time daily? This is a typical Command and Control profile setup.

Check anything host to host within our network. In a standard setup, the user's hosts will rarely talk to each other. So be suspicious of any traffic that appears like this.

Look for unique events. In large environments, patterns are expected, so anything sticking out warrants a look.

