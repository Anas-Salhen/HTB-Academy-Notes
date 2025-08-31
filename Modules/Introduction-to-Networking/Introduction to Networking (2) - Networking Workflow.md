# Networking Models
2 models describe the communication and transfer of data: the `ISO/OSI model` and the `TCP/IP model`. They are divided into layers as seen below.  
<img width="2576" height="1533" alt="Pasted image 20250825063206" src="https://github.com/user-attachments/assets/1f1637c6-7dd4-48bf-9491-3df9d69db1d0" />  


## OSI Model
`Open Systems Interconnection` model, published by the `International Telecommunication Union` (`ITU`) and the `International Organization for Standardization` (`ISO`). The 7 layers of the OSI model represent phases in the establishment of each connection through which the sent packets pass.

| **Layer**        | **Function**                                                                                                                                                                                                                |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `7.Application`  | Among other things, this layer controls the input and output of data and provides the application functions.                                                                                                                |
| `6.Presentation` | The presentation layer's task is to transfer the system-dependent presentation of data into a form independent of the application.                                                                                          |
| `5.Session`      | The session layer controls the logical connection between two systems and prevents, for example, connection breakdowns or other problems.                                                                                   |
| `4.Transport`    | Layer 4 is used for end-to-end control of the transferred data. The Transport Layer can detect and avoid congestion situations and segment data streams.                                                                    |
| `3.Network`      | On the networking layer, connections are established in circuit-switched networks, and data packets are forwarded in packet-switched networks. Data is transmitted over the entire network from the sender to the receiver. |
| `2.Data Link`    | The central task of layer 2 is to enable reliable and error-free transmissions on the respective medium. For this purpose, the bitstreams from layer 1 are divided into blocks or frames.                                   |
| `1.Physical`     | The transmission techniques used are, for example, electrical signals, optical signals, or electromagnetic waves. Through layer 1, the transmission takes place on wired or wireless transmission lines.                    |
The layers `2-4` are `transport oriented`, and the layers `5-7` are `application oriented`. In each layer specific tasks are performed and it communicates with neighboring layers. Each layer offers services for use to the layer directly above it and uses services of the layer below it.

When an application sends a packet to the other system, the system works the layers shown above from layer `7` down to layer `1`, and the receiving system unpacks the received packet from layer `1` up to layer `7`.

## TCP/IP Model
`TCP/IP` (`Transmission Control Protocol`/`Internet Protocol`) is a generic term for a family of protocols that are essential to the functions of the internet. The TCP/IP model is often referred to as the Internet Protocol Suite. It consists of these 4 layers.

| **Layer**       | **Function**                                                                                                                                                                                                                                     | OSI Equivalent                                                 |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------- |
| `4.Application` | The Application Layer allows applications to access the other layers' services and defines the protocols applications use to exchange data.                                                                                                      | Corresponds to layers 5-7 in the OSI model.                    |
| `3.Transport`   | The Transport Layer is responsible for providing (TCP) session and (UDP) datagram services for the Application Layer.                                                                                                                            | Corresponds to the transport layer (layer 4) in the OSI model. |
| `2.Internet`    | The Internet Layer is responsible for host addressing, packaging, and routing functions.                                                                                                                                                         | Corresponds to the network layer (layer 3) in the OSI model.   |
| `1.Link`        | The Link layer is responsible for placing the TCP/IP packets on the network medium and receiving corresponding packets from the network medium. TCP/IP is designed to work independently of the network access method, frame format, and medium. | Corresponds to layers 1-2 in the OSI model.                    |
Most important tasks of TCP/IP:

| **Task**               | **Protocol** | **Description**                                                                                                                                                                                                                                                                                                                        |
| ---------------------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Logical Addressing`   | `IP`         | Due to many hosts in different networks, there is a need to structure the network topology and logical addressing. Within TCP/IP, IP takes over the logical addressing of networks and nodes. Data packets only reach the network where they are supposed to be. The methods to do so are `network classes`, `subnetting`, and `CIDR`. |
| `Routing`              | `IP`         | For each data packet, the next node is determined in each node on the way from the sender to the receiver. This way, a data packet is routed to its receiver, even if its location is unknown to the sender.                                                                                                                           |
| `Error & Control Flow` | `TCP`        | The sender and receiver are frequently in touch with each other via a virtual connection. Therefore control messages are sent continuously to check if the connection is still established.                                                                                                                                            |
| `Application Support`  | `TCP`        | TCP and UDP ports form a software abstraction to distinguish specific applications and their communication links.                                                                                                                                                                                                                      |
| `Name Resolution`      | `DNS`        | DNS provides name resolution through Fully Qualified Domain Names (FQDN) into IP addresses, enabling us to reach the desired host with the specified name on the internet.                                                                                                                                                             |

## ISO/OSI vs. TCP/IP
`TCP/IP` is a communication protocol that allows hosts to connect to the Internet. It refers to the `Transmission Control Protocol` used in and by applications on the Internet. In contrast to `OSI`, it allows a lightening of the rules that must be followed, provided that general guidelines are followed.

`OSI`, on the other hand, is a communication gateway between the network and end-users. The OSI model is usually referred to as the reference model. It is also known for its strict protocol and limitations.

## Packet Transfers
When data is requested from an application it adds a header to it and sends it as a Protocol Data Unit (PDU) to the layer below it, this is called encapsulation. All layers do this until the PDU reaches the physical layer. When the receiver gets the data it unpacks it starting from the physical layer all the way to the application layer, this is called decapsulation.  
<img width="2650" height="959" alt="Pasted image 20250825070226" src="https://github.com/user-attachments/assets/2a687e63-9152-4993-a18d-ebb0e1257b40" />  

