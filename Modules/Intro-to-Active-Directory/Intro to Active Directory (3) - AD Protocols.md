# Kerberos, DNS, LDAP, MSRPC
## Kerberos
Kerberos is an open standard and has been the default authentication protocol for domain accounts since Windows 2000. It relies on mutual authentication (both the client and server verify their identity) and is considered a stateless authentication protocol that relies on tickets instead of transmitting user passwords over the network. Steps to authenticate with Kerberos:

When a user logs in, their password is used to encrypt a timestamp, which is sent to the Key Distribution Center (KDC) which is within domain controllers to verify the integrity of the authentication by decrypting it. The KDC then issues a Ticket-Granting Ticket (TGT), encrypting it with the secret key of the krbtgt account then sends it to the user. This TGT is used to request service tickets for accessing network resources, allowing authentication without repeatedly transmitting the user's credentials. This process decouples the user's credentials from requests to resources.

The user presents the TGT to the DC, requesting a Ticket Granting Service (TGS) ticket for a specific service. This is the TGS-REQ. If the TGT is successfully validated, its data is copied to create a TGS ticket.

The TGS is encrypted with the NTLM password hash of the service or computer account in whose context the service instance is running and is delivered to the user in the TGS_REP.

The user presents the TGS to the service, and if it is valid, the user is permitted to connect to the resource (AP_REQ).  
<img width="2576" height="2352" alt="Pasted image 20250901224151" src="https://github.com/user-attachments/assets/abd85ba6-f028-4cda-831f-6ca42d3e6ff4" />  
The Kerberos protocol uses port 88 (both TCP and UDP). When enumerating an Active Directory environment, we can often locate Domain Controllers by performing port scans looking for open port 88 using a tool such as Nmap.

## DNS
DNS resolves hostnames into IP addresses. AD uses a special internal DNS namespace (separate from public DNS) so devices on the domain can find each other easily. This includes finding Domain Controllers, file servers, or printers. 

AD stores information about services in Service (SRV) records. For example, when a client needs to find a Domain Controller, it asks DNS for an SRV record. DNS responds with the hostname of a Domain Controller, then the client resolves it to an IP and connects.

Machines may get new IP addresses (DHCP, reboots, network changes). With Dynamic DNS, they automatically update their DNS records so that other systems can still find them. Without it, admins would have to manually edit DNS every time an IP changed, which is error-prone.

By default, DNS queries use UDP port 53 (fast, lightweight). If the response is too big (>512 bytes) or if UDP fails, it switches to TCP port 53.

### Using nslookup
We can perform a `nslookup` for the domain name and retrieve all Domain Controllers' IP addresses in a domain.
```powershell-session
PS C:\htb> nslookup INLANEFREIGHT.LOCAL

Server:  172.16.6.5
Address:  172.16.6.5

Name:    INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```
To get the DNS name using the IP address:
```powershell-session
PS C:\htb> nslookup 172.16.6.5

Server:  172.16.6.5
Address:  172.16.6.5

Name:    ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```
To get the ip address of the host we can do it with or without specifying the FQDN:
```powershell-session
PS C:\htb> nslookup ACADEMY-EA-DC01

Server:   172.16.6.5
Address:  172.16.6.5

Name:    ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```

## LDAP
Lightweight Directory Access Protocol (LDAP) is an open-source and cross-platform protocol used for authentication against various directory services (such as AD). LDAP uses port 389, and LDAP over SSL (LDAPS) communicates over port 636.

AD stores user account information and security information such as passwords and facilitates sharing this information with other devices on the network. LDAP is the language that applications use to communicate with other servers that provide directory services. The relationship between AD and LDAP can be compared to Apache and HTTP. The same way Apache is a web server that uses the HTTP protocol, Active Directory is a directory server that uses the LDAP protocol.  
<img width="2576" height="1457" alt="Pasted image 20250901230624" src="https://github.com/user-attachments/assets/9864dcca-4c90-4507-8756-5c65c3cb5a7b" />  
An LDAP session begins by first connecting to an LDAP server, also known as a Directory System Agent. The Domain Controller in AD actively listens for LDAP requests, such as security authentication requests.

### AD LDAP authentication
LDAP uses a "BIND" operation for authentication. There are 2 types of LDAP authentication:
1. Simple Authentication: This includes anonymous authentication, unauthenticated authentication, and username/password authentication. Simple authentication means that a username and password create a BIND request to authenticate to the LDAP server.
    
2. SASL Authentication: [The Simple Authentication and Security Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) framework uses other authentication services, such as Kerberos, to bind to the LDAP server and then uses this authentication service (Kerberos in this example) to authenticate to LDAP. The LDAP server uses the LDAP protocol to send an LDAP message to the authorization service, which initiates a series of challenge/response messages resulting in either successful or unsuccessful authentication. SASL can provide additional security due to the separation of authentication methods from application protocols.
LDAP authentication messages are sent in cleartext by default so anyone can sniff out LDAP messages on the internal network. It is recommended to use TLS encryption or similar to safeguard this information in transit.

## MSRPC
It's Microsoft's implementation of Remote Procedure Call (RPC), an interprocess communication technique used for client-server model-based applications. Windows systems use MSRPC to access systems in Active Directory using four key RPC interfaces.

| Interface Name | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `lsarpc`       | A set of RPC calls to the [Local Security Authority (LSA)](https://networkencyclopedia.com/local-security-authority-lsa/) system which manages the local security policy on a computer, controls the audit policy, and provides interactive authentication services. LSARPC is used to perform management on domain security policies.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `netlogon`     | Netlogon is a Windows process used to authenticate users and other services in the domain environment. It is a service that continuously runs in the background.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `samr`         | Remote SAM (samr) provides management functionality for the domain account database, storing information about users and groups. IT administrators use the protocol to manage users, groups, and computers by enabling admins to create, read, update, and delete information about security principles. Attackers (and pentesters) can use the samr protocol to perform reconnaissance about the internal domain using tools such as [BloodHound](https://github.com/BloodHoundAD/) to visually map out the AD network and create "attack paths" to illustrate visually how administrative access or full domain compromise could be achieved. Organizations can [protect](https://stealthbits.com/blog/making-internal-reconnaissance-harder-using-netcease-and-samri1o/) against this type of reconnaissance by changing a Windows registry key to only allow administrators to perform remote SAM queries since, by default, all authenticated domain users can make these queries to gather a considerable amount of information about the AD domain. |
| `drsuapi`      | drsuapi is the Microsoft API that implements the Directory Replication Service (DRS) Remote Protocol which is used to perform replication-related tasks across Domain Controllers in a multi-DC environment. Attackers can utilize drsuapi to [create a copy of the Active Directory domain database](https://attack.mitre.org/techniques/T1003/003/) (NTDS.dit) file to retrieve password hashes for all accounts in the domain, which can then be used to perform Pass-the-Hash attacks to access more systems or cracked offline using a tool such as Hashcat to obtain the cleartext password to log in to systems using remote management protocols such as Remote Desktop (RDP) and WinRM.                                                                                                                                                                                                                                                                                                                                                           |

# NTLM Authentication
Aside from Kerberos and LDAP, AD uses several other authentication methods like LM, NTLM, NTLMv1, and NTLMv2. LM and NTLM are hash names while NTLMv1 and NTLMv2 are authentication protocols that utilize the LM or NT hash.

|**Hash/Protocol**|**Cryptographic technique**|**Mutual Authentication**|**Message Type**|**Trusted Third Party**|
|---|---|---|---|---|
|`NTLM`|Symmetric key cryptography|No|Random number|Domain Controller|
|`NTLMv1`|Symmetric key cryptography|No|MD4 hash, random number|Domain Controller|
|`NTLMv2`|Symmetric key cryptography|No|MD4 hash, random number|Domain Controller|
|`Kerberos`|Symmetric key cryptography & asymmetric cryptography|Yes|Encrypted ticket using DES, MD5|Domain Controller/Key Distribution Center (KDC)|

## LM
LAN Manager (LM or LANMAN) hashes are the oldest password storage mechanism used by the windows OS. If in use, they are stored in the SAM database on a Windows host and the NTDS.DIT database on a Domain Controller. For its security weaknesses LM hashes have been turned off by default since Windows Vista/Server 2008. However, it is still common to encounter, especially in large environments where older systems are still used. 

Passwords using LM are limited to a maximum of `14` characters and aren't case sensitive, all characters are converted to uppercase. Then they are split into 2 words 7 characters each. If the password is less than 14 characters the rest of it is filled by zeroes. 

Those 2 words are then used to create two DES keys that will encrypt the string `KGS!@#$%` creating two 8-byte ciphertext values which are concatenated into the final string which is the hashed representation of the password. 

The fact that in the hashing process the password is split into 2 words means that attackers need to brute force seven characters twice which is easier than dealing with the entire 14 characters together and can be done using tools like Hashcat. Also if the password is less than 7 characters then the second word will be all zeroes and when hashed it will always result in the same string. When attackers see that string they will know that the second word is all zeroes and that they only need to brute force the first word.

## NTHash (NTLM)
NT LAN Manager (NTLM), is an authentication protocol that uses three messages. First, the client sends a `NEGOTIATE_MESSAGE` asking the server to start the authentication process. The server responds with a `CHALLENGE_MESSAGE` which contains an 8-byte challenge. The client takes it and encrypts it using a key which is derived from the NT hash of his password and sends the ciphertext to the server in the `AUTHENTICATE_MESSAGE`. If it matches the result the server has then authentication is successful.

To get the NT hash of the password (the one used to derive the encryption key) we need to take the password itself then convert it to UTF-16 little-endian encoding and then encrypt it using MD4. The result of that is the NT hash.

Even though NTLM is stronger than LM it still has issues. It's not hard to brute force it using a tool like Hashcat. Using powerful GPUs and password cracking tools, an 8 character NTLM password can be cracked in under 3 hours. Even longer passwords can be cracked using an offline dictionary attack.

NTLM is also vulnerable to pass-the-hash attacks which means that an attacker doesn't need to know the original password and can use the NTLM hash to authenticate. NTLM hashes are stored locally in the SAM database or the NTDS.DIT database file on a Domain Controller. A hash looks similar to this: 
```shell-session
Rachel:500:aad3c435b514a4eeaad3b935b51304fe:e46b9e548fa0d122de7f59fb6d48eaa2:::
```
- `Rachel`: username
- `500`: the Relative Identifier (RID). 500 is the known RID for the `administrator` account
- `aad3c435b514a4eeaad3b935b51304fe`: the LM hash and, if LM hashes are disabled on the system, can not be used for anything
- `e46b9e548fa0d122de7f59fb6d48eaa2`: the NT hash. This hash can either be cracked offline to reveal the cleartext value (depending on the length/strength of the password) or used for a pass-the-hash attack using a tool like [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec).

## NTLMv1 (Net-NTLMv1)
With this protocol the authentication process is as follows. 
- The server generates an 8-byte random number `C` (the challenge) and sends it to the client. 
- The client takes the user’s LM hash or NT hash of the password (depending on configuration). This hash is 16 bytes, it gets split and padded into three DES keys (`K1`, `K2`, `K3`), each 7 bytes → 56-bit DES keys. 
- Each key encrypts the server’s challenge with DES: `response = DES(K1, C) | DES(K2, C) | DES(K3, C)`. 
- The three encrypted blocks are concatenated into a 24-byte response. 
- The server, knowing the user’s stored hash, repeats the calculation and checks if the client’s response matches.
Pass-the-hash attacks don't work with NTLMv1. NTLMv1 still had its flaws which is why we needed NTLMv2 to fix them.

## NTLMv2 (Net-NTLMv2)
First introduced in Windows NT 4.0 SP4 and has been the default in Windows since Server 2000. It is hardened against certain spoofing attacks that NTLMv1 is susceptible to. 
Algorithm for authentication:
- Server generates an 8-byte random challenge (`SC`)
- Client generates its own 8-byte random challenge too (`CC`)
- A value called `CC*` is generated using like timestamp, domain name, randomly generated number, etc.
- `v2-Hash = HMAC-MD5(NT-Hash, UserName || DomainName)`:
	- The `v2-Hash` is generated using the NT hash as a key to apply HMAC-MD5 hashing to the username and domain name
- `LMv2 = HMAC-MD5(v2-Hash, SC || CC)`:
	- `LMv2` is made by applying HMAC-MD5 hashing to the client's challenge and the server's challenge using the v2 hash as the key
- `NTv2 = HMAC-MD5(v2-Hash, SC || CC*)`:
	- `NTv2` is made by applying HMAC-MD5 hashing to `CC*` and the server's challenge using the v2 hash as the key
- `Response = LMv2 || CC || NTv2 || CC*`:
	- The final response that the client sends to the server consists of LMv2, the client's challenge, NTv2 and `CC*`.
The server knows the user’s password (through the NT-Hash stored in the SAM/AD). It rebuilds the v2-Hash. Then recomputes LMv2 and NTv2 using the same inputs. If they match the client’s, authentication succeeds.

## Domain Cached Credentials (MSCache2)
Sometimes a domain-joined host might not be able to access the DC (due to a network issue for example). Microsoft developed an algorithm for such cases called [MS Cache v1 and v2](https://webstersprodigy.net/2014/02/03/mscash-hash-primer-for-pentesters/) or (DCC). Hosts save the last ten hashes for any domain users that successfully logged into the machine in the `HKEY_LOCAL_MACHINE\SECURITY\Cache` registry key. These hashes can't be used in pass-the-hash attacks and can't be easily cracked with tools like Hashcat and powerful GPUs.
