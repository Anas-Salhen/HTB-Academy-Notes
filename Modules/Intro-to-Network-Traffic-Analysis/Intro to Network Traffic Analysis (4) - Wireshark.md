# Analysis with Wireshark
## TShark VS. Wireshark (Terminal vs. GUI)
Both options have their merits. TShark is a purpose-built terminal tool based on Wireshark. TShark shares many of the same features that are included in Wireshark and even shares syntax and options. Basic TShark switches include:

|**Switch Command**|**Result**|
|:-:|---|
|D|Will display any interfaces available to capture from and then exit out.|
|L|Will list the Link-layer mediums you can capture from and then exit out. (ethernet as an example)|
|i|choose an interface to capture from. (-i eth0)|
|f|packet filter in libpcap syntax. Used during capture.|
|c|Grab a specific number of packets, then quit the program. Defines a stop condition.|
|a|Defines an autostop condition. Can be after a duration, specific file size, or after a certain number of packets.|
|r (pcap-file)|Read from a file.|
|W (pcap-file)|Write into a file using the pcapng format.|
|P|Will print the packet summary while writing into a file (-W)|
|x|will add Hex and ASCII output into the capture.|
|h|See the help menu|
Filter example: `sudo tshark -i eth0 -f "host 172.16.146.2"`

## Termshark
Termshark is a Text-based User Interface (TUI) application that provides the user with a Wireshark-like interface right in your terminal window.  
<img width="961" height="603" alt="Pasted image 20250830191459" src="https://github.com/user-attachments/assets/b632dba7-6429-43af-a789-428e31142a58" />  

## Wireshark GUI Walkthrough
<img width="1172" height="785" alt="Pasted image 20250830191533" src="https://github.com/user-attachments/assets/41f76da4-d1ad-4cef-8698-6ac2faca2e34" />  
Main panes explanation:
1. Packet List: `Orange`
    - In this window, we see a summary line of each packet that includes the fields listed below by default. We can add or remove columns to change what information is presented.
        - Number- Order the packet arrived in Wireshark
        - Time- Unix time format
        - Source- Source IP
        - Destination- Destination IP
        - Protocol- The protocol used (TCP, UDP, DNS, ETC.)
        - Information- Information about the packet. This field can vary based on the type of protocol used within. It will show, for example, what type of query It is for a DNS packet.
         
2. Packet Details: `Blue`
    - The Packet Details window allows us to drill down into the packet to inspect the protocols with greater detail. It will break it down into chunks that we would expect following the typical OSI Model reference. `The packet is dissected into different encapsulation layers for inspection.`
    - Keep in mind, Wireshark will show this encapsulation in reverse order with lower layer encapsulation at the top of the window and higher levels at the bottom.
     
3. Packet Bytes: `Green`
    - The Packet Bytes window allows us to look at the packet contents in ASCII or hex output. As we select a field from the windows above, it will be highlighted in the Packet Bytes window and show us where that bit or byte falls within the overall packet.
    - This is a great way to validate that what we see in the Details pane is accurate and the interpretation Wireshark made matches the packet output.
    - Each line in the output contains the data offset, sixteen hexadecimal bytes, and sixteen ASCII bytes. Non-printable bytes are replaced with a period in the ASCII format.

To start a capture:  
![first-capture-ws](https://github.com/user-attachments/assets/37ad4a3b-65ec-47d6-b4fa-efd22bb9eb91)  
To save a capture to a file: File ⇢ Save.

## Wireshark Filtering
### Capture Filters
They are entered before the capture is started. These use BPF syntax like `host 214.15.2.30` much in the same fashion as TCPDump. Common capture filters:

|       **Capture Filters**       | **Result**                                                                                                           |
| :-----------------------------: | -------------------------------------------------------------------------------------------------------------------- |
|          host x.x.x.x           | Capture only traffic pertaining to a certain host                                                                    |
|         net x.x.x.x/24          | Capture traffic to or from a specific network (using slash notation to specify the mask)                             |
|     src/dst net x.x.x.x/24      | Using src or dst net will only capture traffic sourcing from the specified network or destined to the target network |
|             port #              | will filter out all traffic except the port you specify                                                              |
|           not port #            | will capture everything except the port specified                                                                    |
|          port # and #           | AND will concatenate your specified ports                                                                            |
|          portrange x-x          | portrange will grab traffic from all ports within the range only                                                     |
|        ip / ether / tcp         | These filters will only grab traffic from specified protocol headers.                                                |
| broadcast / multicast / unicast | Grabs a specific type of traffic. one to one, one to many, or one to all.                                            |
We can view existing default capture filters from Capture ⇢ Capture Filters. From here, we can modify the existing filters or add our own. To apply the filter to a capture, click Capture ⇢ Options in the new window select the drop-down for Capture filter for selected interfaces or type in the filter we wish to use. `below the red arrow in the picture below`  
<img width="1892" height="960" alt="Pasted image 20250830195347" src="https://github.com/user-attachments/assets/8cc86d94-aa53-4ef0-a56a-d3aa61f58138" />  

### Display Filters
They are used while the capture is running and after the capture has stopped. While utilizing display filters traffic is processed to show only what is requested but the rest of the capture file will not be overwritten.

|    **Display Filters**     | **Result**                                                                                    |
| :------------------------: | --------------------------------------------------------------------------------------------- |
|     ip.addr == x.x.x.x     | Capture only traffic pertaining to a certain host. This is an OR statement.                   |
|   ip.addr == x.x.x.x/24    | Capture traffic pertaining to a specific network. This is an OR statement.                    |
|   ip.src/dst == x.x.x.x    | Capture traffic to or from a specific host                                                    |
| dns / tcp / ftp / arp / ip | filter traffic by a specific protocol. There are many more options.                           |
|       tcp.port == x        | filter by a specific tcp port.                                                                |
|  tcp.port / udp.port != x  | will capture everything except the port specified                                             |
|       and / or / not       | AND will concatenate, OR will find either of two options, NOT will exclude your input option. |
To apply a display filter, from the main Wireshark capture window: select the bookmark in the Toolbar → , then select an option from the drop-down. Alternatively, place the cursor in the text radial → and type in the filter we wish to use. If the field turns green, the filter is correct. `Just like in the image below.`  
<img width="1074" height="374" alt="Pasted image 20250830195809" src="https://github.com/user-attachments/assets/84583e78-96aa-4a5d-9638-6df996ec295e" />  

# Wireshark Advanced Usage
## Statistics
The statistics tab has many useful plugins which can give us detailed reports about the network traffic being utilized. It can show us everything from the top talkers in our environment to specific conversations and even breakdown by IP and protocol.  
<img width="1891" height="1059" alt="Pasted image 20250830233256" src="https://github.com/user-attachments/assets/09f43a64-4cb2-480a-9b1f-6d4e67c84853" />  

## Analyze
From the Analyze tab, we can utilize plugins that allow us to do things such as following TCP streams, filter on conversation types, prepare new packet filters and examine the expert info Wireshark generates about the traffic.

## Following TCP Streams
To utilize this feature:
- right-click on a packet from the stream we wish to recreate.
- select follow → TCP
- this will open a new window with the stream stitched back together. From here, we can see the entire conversation.
![follow-tcp](https://github.com/user-attachments/assets/2901d90a-8813-4039-832f-903520657507)  
Alternatively, we can utilize the filter `tcp.stream eq #` to find and track conversations captured in the pcap file.

## Extracting Data and Files From a Capture
Imagine someone sends an image or file over the network can we get it? Since the file is split and sent in separate packets if you have the full conversation, wireshark can reassemble the file and save it for you. To extract files from a stream:
- stop your capture.
- Select File → Export → , then select the protocol format to extract from (DICOM, HTTP, SMB, etc.).
This doesn't work with encrypted data.
![extract-http](https://github.com/user-attachments/assets/af1d9dbb-af38-4c99-b5f9-9b5ebff05af4)  
For example if we have FTP data that we want to analyze we could:
1. Identify any FTP traffic using the `ftp` display filter.
2. Look at the command controls sent between the server and hosts to determine if anything was transferred and who did so with the `ftp.request.command` filter.
3. Choose a file, then filter for `ftp-data`. Select a packet that corresponds with our file of interest and follow the TCP stream that correlates to it.
4. Once done, Change "`Show and save data as`" to "`Raw`" and save the content as the original file name.
5. Validate the extraction by checking the file type.
