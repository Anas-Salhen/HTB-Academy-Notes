# Key Exchange Mechanisms
Methods used to exchange cryptographic keys between two hosts which is important for encryption.

## Diffie-Hellman
This is a method for 2 parties to agree on a key to use. Both sides agree on some shared public numbers. Everyone can see these, even an attacker. Each side independently chooses a secret random number that they keep private. Each side uses their private number and the public numbers to generate a "public result". This step uses math that’s easy to compute in one direction but nearly impossible to reverse.

They swap their public results with each other. This part can be intercepted — it’s okay, because the private numbers are not revealed. Each side takes the other’s public result and combines it with their own private secret. Due to how the math is structured, both sides end up calculating the same final number. That final number becomes the shared secret key, which can then be used for encryption.

The problem is that it's vulnerable to a MITM attack. If A wants to communicate to B an attacker could impersonate B to respond to A and impersonate A to respond to B. Now the attacker has two separate Diffie-Hellman exchanges, one with A and one with B which means that he can read all encrypted data freely.

## RSA
[Rivest–Shamir–Adleman](https://web.archive.org/web/20240302211950/https://venafi.com/blog/how-diffie-hellman-key-exchange-different-rsa/) (RSA) algorithm is a method to generate a shared secret key which relies on the fact that it's easy to multiply large prime numbers but difficult to factor that result into the original prime factors.

## ECDH
[Elliptic curve Diffie-Hellman](https://medium.com/swlh/understanding-ec-diffie-hellman-9c07be338d4a) (`ECDH`) is a variant of Diffie-Hellman key exchange that uses elliptic curve cryptography to generate the shared secret key. It's more secure than the original Diffie-Hellman in things that include:
- Establishing secure communication channels, such as in the `TLS` protocol
- Providing forward secrecy, which ensures that past communications cannot be revealed even if the private keys are compromised
- Authenticating users and devices, such as in the [Internet Key Exchange](https://docs.oracle.com/cd/E19683-01/816-7264/6md9iem1g/index.html) (`IKE`) protocol used in VPNs

## ECDSA
The [Elliptic Curve Digital Signature Algorithm](https://www.hypr.com/security-encyclopedia/elliptic-curve-digital-signature-algorithm) (`ECDSA`) uses elliptic curve cryptography to generate digital signatures that can authenticate the parties involved in the key exchange.

## Internet Key Exchange
IKE is a protocol used to maintain secure sessions such as the ones used in VPNs. It uses a combination of the Diffie-Hellman key exchange algorithm and other cryptographic techniques.

IKE can also be used for other purposes, such authentication. It is typically used in conjunction with other protocols and algorithms, such as the RSA algorithm for key exchange and digital signatures, and the [Advanced Encryption Standard](https://www.geeksforgeeks.org/advanced-encryption-standard-aes/) (`AES`) for data encryption.

### Main Mode
The default mode for IKE and is more secure than aggressive mode but at the price of lower performance. The key exchange process is performed in three phases in the main mode, each exchanging a different set of security parameters and keys.

### Aggressive Mode
This mode can offer better performance but is less secure than the main mode. In this mode, the key exchange process is performed in two phases, with all security parameters and keys being exchanged in the first phase.

### Pre-Shared Keys
In IKE, a Pre-Shared Key (PSK) is a secret value shared between the two parties involved in the key exchange. This key is used to authenticate the parties and establish a shared secret that encrypts subsequent communication. Using a PSK is optional in IKE. The main advantage of PSK is providing another layer of security but if the key is compromised through a MITM attack it will cause problems.
## Importance of Encryption
- Encrypting and signing messages to provide confidentiality and authentication
- Protecting data in transit over networks, such as in the [Secure Socket Layer](https://www.cloudflare.com/learning/ssl/what-is-ssl/) (`SSL`) and `TLS` protocols
- Generating and verifying digital signatures, which are used to provide authenticity and integrity for electronic documents and other digital data
- Authenticating users and devices, such as in the [Public Key Cryptography for Initial Authentication in Kerberos](https://www.ietf.org/rfc/rfc4556.txt) (`PKINIT`) protocol used by the Kerberos network authentication system
- Protecting sensitive information, such as in the encryption of personal data and confidential documents

# Authentication Protocols
| **Protocol** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Kerberos`   | Key Distribution Center (KDC) based authentication protocol that uses tickets in domain environments.                                                                                                                                                                                                                                                                                                                                               |
| `SRP`        | This is a password-based authentication protocol that uses cryptography to protect against eavesdropping and man-in-the-middle attacks.                                                                                                                                                                                                                                                                                                             |
| `SSL`        | A cryptographic protocol used for secure communication over a computer network.                                                                                                                                                                                                                                                                                                                                                                     |
| `TLS`        | TLS is a cryptographic protocol that provides communication security over the internet. It is the successor to SSL.                                                                                                                                                                                                                                                                                                                                 |
| `OAuth`      | An open standard for authorization that allows users to grant third-party access to their web resources without sharing their passwords.                                                                                                                                                                                                                                                                                                            |
| `OpenID`     | OpenID is a decentralized authentication protocol that allows users to use a single identity to sign in to multiple websites.                                                                                                                                                                                                                                                                                                                       |
| `SAML`       | Security Assertion Markup Language is an XML-based standard for securely exchanging authentication and authorization data between parties.                                                                                                                                                                                                                                                                                                          |
| `2FA`        | An authentication method that uses a combination of two different factors to verify a user's identity.                                                                                                                                                                                                                                                                                                                                              |
| `FIDO`       | The Fast IDentity Online Alliance is a consortium of companies working to develop open standards for strong authentication.                                                                                                                                                                                                                                                                                                                         |
| `PKI`        | PKI is a system for securely exchanging information based on the use of public and private keys for encryption and digital signatures.                                                                                                                                                                                                                                                                                                              |
| `SSO`        | An authentication method that allows a user to use a single set of credentials to access multiple applications.                                                                                                                                                                                                                                                                                                                                     |
| `MFA`        | MFA is an authentication method that uses multiple factors, such as something the user knows (a password), something the user has (a phone), or something the user is (biometric data), to verify their identity.                                                                                                                                                                                                                                   |
| `PAP`        | A simple authentication protocol that sends a user's password in clear text over the network.                                                                                                                                                                                                                                                                                                                                                       |
| `CHAP`       | An authentication protocol that uses a three-way handshake to verify a user's identity.                                                                                                                                                                                                                                                                                                                                                             |
| `EAP`        | A framework for supporting multiple authentication methods, allowing for the use of various technologies to verify a user's identity.                                                                                                                                                                                                                                                                                                               |
| `SSH`        | This is a network protocol for secure communication between a client and a server. We can use it for remote command-line access and remote command execution, as well as for secure file transfer. SSH uses encryption to protect against eavesdropping and other attacks and can also be used for authentication.                                                                                                                                  |
| `HTTPS`      | This is a secure version of the HTTP protocol used for communication on the internet. HTTPS uses SSL/TLS to encrypt communication and provide authentication, ensuring that third parties cannot intercept and read the transmitted data. It is widely used for secure communication over the internet, particularly for web browsing.                                                                                                              |
| `LEAP`       | LEAP is a wireless authentication protocol developed by Cisco. It uses EAP to provide mutual authentication between a wireless client and a server and uses the RC4 encryption algorithm to encrypt communication between the two. Unfortunately, LEAP is vulnerable to dictionary attacks and other security vulnerabilities and has been largely replaced by more secure protocols such as EAP-TLS and PEAP.                                      |
| `PEAP`       | PEAP on the other hand is a secure tunneling protocol used for wireless and wired networks. It is based on EAP and uses TLS to encrypt communication between a client and a server. PEAP uses a server-side certificate to authenticate the server and can also be used to authenticate the client using various methods, such as passwords, certificates, or biometric data. PEAP is widely used in enterprise networks for secure authentication. |

# TCP/UDP Connections
TCP is a connection-oriented protocol that ensures that all data sent from one computer to another is received. If an error occurs while sending data, the receiver sends a message back so the sender can resend the missing data making TCP more reliable but slower than UDP. TCP is better for important data that has to be fully transmitted, such as web pages and emails.

UDP is a connectionless protocol. It is used when speed is more important than reliability, such as for video streaming or online gaming. With `UDP`, there is no verification that the received data is complete and error-free.

## IP Packets
IP Packets are like vehicles in which the data is sent at layer 3 of the OSI model. They consist of an IP header and the payload (the actual data). The IP header contains several fields carrying important info:

| **Field**                | **Description**                                                                |
| ------------------------ | ------------------------------------------------------------------------------ |
| `Version`                | Indicates which version of the IP protocol is being used (IPv4 or IPv6)        |
| `Internet Header Length` | Indicates the size of the header in 32-bit words                               |
| `Class of Service`       | Means how important the transmission of the data is                            |
| `Total length`           | Specifies the total length of the packet in bytes                              |
| `Identification (ID)`    | Is used to identify fragments of the packet when fragmented into smaller parts |
| `Flags`                  | Used to indicate fragmentation                                                 |
| `Fragment Offset`        | Indicates where the current fragment is placed in the packet                   |
| `Time to Live`           | Specifies how long the packet may remain on the network                        |
| `Protocol`               | Specifies which protocol is used to transmit the data, such as TCP or UDP      |
| `Checksum`               | Is used to detect errors in the header                                         |
| `Source/Destination`     | Indicate where the packet was sent from and where it is being sent to          |
| `Options`                | Contain optional information for routing                                       |
| `Padding`                | Pads the packet to a full word length                                          |
Notice the following traffic:
```shell-session
IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1337
IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1338
IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1339
IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1340
IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1341
IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1342
```
We have 2 different IP addresses sending packets. Notice how the value in the IP ID field are so close and continuous? This indicates that all of them are coming from the same host not 2 different hosts. This is important knowledge for a security engineer.

## IP Record-Route Field
The Record-Route field isn't a fixed field but rather part of the options field in the IP header which records the route to a destination device. When the destination device sends back the ICMP Echo Reply packet, the IP addresses of all devices that pass through the packet are listed in the Record-Route field of the IP header. We can get the route in this way using `ping` with the `-r` option.

## traceroute
The `traceroute` tool can also be used to trace the route to a destination more accurately, which uses the TCP timeout method to determine when the route has been fully traced.
1. We send a TCP SYN packet to the destination device with a TTL of 1 in the IP header.
    
    When the TCP SYN packet with a TTL greater than 1 reaches a router, the value of the TTL is decreased by 1, and the packet is forwarded to the next device. If the TCP SYN packet with a TTL of 1 reaches a router, the packet is dropped, and the router sends an ICMP Time-Exceeded packet back to us.
    
2. We receive the ICMP Time-Exceeded packet and note the IP address of the router that sent the packet.
    
3. After that, we send another TCP SYN packet to the destination, increasing the TTL by 1.
    

The process repeats until the TCP SYN packet reaches the destination host and receives a `TCP SYN/ACK` or a `TCP RST` response from the target. Once we receive a response from the destination device, we know that we have traced the route to the destination and ended the traceroute process.

When `traceroute` is used with UDP instead of TCP, we will receive a `Destination Unreachable` and `Port Unreachable` message when the UDP datagram packet reaches the target device. Generally, UDP packets are sent using `traceroute` on Unix hosts.
## IP Payload
It's the actual data being transmitted in the packet also known as segments. It contains data from the layer above 3 in the OSI model (layer 4) in the form of the TCP or UDP protocol. Like packets segments are divided into headers and payloads too.

### TCP
The header contains several important fields:
- The source port indicates the computer from which the packet was sent. 
- The destination port indicates to which computer the packet is sent.
- The sequence number indicates the order in which the data was sent. 
- The confirmation number is used to confirm that all data was received successfully. 
- The control flags indicate whether the packet marks the end of a message, whether it is an acknowledgment that data has been received, or whether it contains a request to repeat data. 
- The window size indicates how much data the receiver can receive. 
- The checksum is used to detect errors in the header and payload. 
- The Urgent Pointer alerts the receiver that important data is in the payload.

Then after the header there is the payload of the segment.

# Cryptography
## Symmetric Encryption
Symmetric encryption, also known as secret key encryption, is a method that uses the same key to encrypt and decrypt the data. [Advanced Encryption Standard](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (AES) and [Data Encryption Standard](https://en.wikipedia.org/wiki/Data_Encryption_Standard) (DES) are examples of symmetric encryption algorithms. This type of encryption is often used to encrypt large amounts of data, such as files on a hard drive or data sent over a network. AES is considered to be the most secure encryption algorithm nowadays.

## Asymmetric Encryption
This method uses 2 different keys, a public key used to encrypt the data and a private key used to decrypt the data. Examples of asymmetric encryption methods include [Rivest–Shamir–Adleman](https://en.wikipedia.org/wiki/RSA_\(cryptosystem\)) (RSA), [Pretty Good Privacy](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) (PGP), and [Elliptic Curve Cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) (ECC). Many apps use asymmetric encryption like E-Signatures, SSH, SSL/TLS, PKI, VPNs and Cloud.

One advantage of asymmetric encryption is its security. Also, since the public key can be accessible to everyone, there is no need to exchange keys secretly. In addition, the asymmetric methods open up the possibility of authentication with digital signatures.

## Data Encryption Standard
DES is a symmetric-key block cipher. The key consists of 64 bits, with 8 bits used as a checksum. Therefore, the actual key length of DES is only 56 bits. An extension of DES is the so-called [Triple DES / 3DES](https://en.wikipedia.org/wiki/Triple_DES), which encrypts data more securely. The procedure for this usually consists of three keys. AES, the successor to DES, provides higher security using longer key lengths.

## Advanced Encryption Standard
It uses 128 bit, 192 bit and 256 bit keys so it's more secure than DES also it's faster since has a more efficient algorithm structure. We can find it in many apps/protocols including: WLAN IEEE 802.11i, IPSec, SSH, VoIP, PGP and OpenSSL.

## Cipher Modes
A cipher mode refers to how a block cipher algorithm encrypts a plaintext message.

| **Cipher Mode**                                                                                                      | **Description**                                                                                                                                                                                                                                                               |
| -------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Electronic Code Book](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation) (`ECB`) mode                    | ECB mode is generally not recommended for use due to its susceptibility to certain types of attacks. Furthermore, it does not hide data patterns efficiently. As a result, statistical analysis can reveal elements of clear-text messages, for example, in web applications. |
| [Cipher Block Chaining](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CBC) (`CBC`) mode               | CBC mode is generally used to encrypt messages like disk encryption and e-mail communication. This is the default mode for AES and is also used in software like TrueCrypt, VeraCrypt, TLS, and SSL.                                                                          |
| [Cipher Feedback](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_feedback_\(CFB\)) (`CFB`) mode | CFB mode is well suited for real-time encryption of a data stream, e.g., network communication encryption or encryption/decryption of files in transit like Public-Key Cryptography Standards (PKCS) and Microsoft's BitLocker.                                               |
| [Output Feedback](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#OFB) (`OFB`) mode                     | OFB mode is also used to encrypt a data stream, e.g., to encrypt real-time communication. However, this mode is considered better for the data stream because of how the key stream is generated. We can find this mode in PKCS but also in the SSH protocol.                 |
| [Counter](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (`CTR`) mode                             | CTR mode encrypts real-time data streams AES uses, e.g., network communication, disk encryption, and other real-time scenarios where data is processed. An example of this would be IPsec or Microsoft's BitLocker.                                                           |
| [Galois/Counter](https://en.wikipedia.org/wiki/Galois/Counter_Mode) (`GCM`) mode                                     | GCM is used in cases where confidentiality and integrity need to be protected together, such as wireless communications, VPNs, and other secure communication protocols.                                                                                                      |
