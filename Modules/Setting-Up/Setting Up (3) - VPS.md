# Virtual Private Server (VPS)
It is an isolated environment created on a physical server using virtualization technology, it is considered IaaS. It allows us to configure **without restrictions** and uses the resources of a physical server to provide **server functionalities** which is why it's also called Virtual Dedicated Server **(VDS).**
It is a compromise between inexpensive shared hosting and the usually costly rental of dedicated server technology.

Uses for VPS:

| Webserver          | LAMP/XAMPP stack | Mail server   | DNS server      |
| ------------------ | ---------------- | ------------- | --------------- |
| Development server | Proxy server     | Test server   | Code repository |
| Pentesting         | VPN              | Gaming server | C2 server       |

Common VPS providers include: [Vultr](https://www.vultr.com/products/cloud-compute/), [Linode](https://www.linode.com/pricing/), [DigitalOcean](https://www.digitalocean.com/pricing), [OneHostCloud](https://onehostcloud.hosting/).

## Securing the Network
Tools:
1. [Netbird](https://netbird.io/): excels in use cases requiring simple, automated setup for secure access to cloud and on-premises resources, like connecting developer workstations to private repositories.
2. [Tailscale](https://tailscale.com/): a WireGuard-based virtual private cloud and requires minimal configuration which makes it perfect for use cases such as securing remote employees connections to internal services (e.g., CI/CD pipelines, internal dashboards) or enabling site-to-site networking without complex firewall rules.
3. [Twingate](https://www.twingate.com/): its Relay and Connector architecture and API-first design make it ideal for things like securing access to databases, Kubernetes clusters, or legacy applications in multi-cloud or on-premises environments.

These providers focus on a framework called Zero Trust Network Access (ZTNA) which authorizes every connection attempt. 
## Securing the VPS
To improve security we should limit access to the VPS to SSH and disable other services.
We can also harden the SSH using:
- Install Fail2ban and configure backups
- Working only with SSH keys
- Reduce Idle timeout interval
- Disable passwords
- Disable x11 forwarding
- Use a different port
- Limit users' SSH access
- Disable root logins
- Use SSH proto 2
- Enable 2FA Authentication for SSH
Other changes that can be made to SSH: set ban time to 4 weeks and allow maximum 3 attempts.

| Setting                                              | Description                                                                                                                                                                                           |
| ---------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| LogLevel VERBOSE                                     | Gives the verbosity level that is used when logging messages from SSH daemon.                                                                                                                         |
| PermitRootLogin no                                   | Specifies whether root can log in using SSH.                                                                                                                                                          |
| MaxAuthTries 3                                       | Specifies the maximum number of authentication attempts permitted per connection.                                                                                                                     |
| MaxSessions 5                                        | Specifies the maximum number of open shell, login, or subsystem (e.g., SFTP) sessions allowed per network connection.                                                                                 |
| HostbasedAuthentication no                           | Specifies whether rhosts or /etc/hosts.equiv authentication together with successful public key client host authentication is allowed (host-based authentication).                                    |
| PermitEmptyPasswords no                              | When password authentication is allowed, it specifies whether the server allows login to accounts with empty password strings.                                                                        |
| ChallengeResponseAuthentication yes                  | Specifies whether challenge-response authentication is allowed.                                                                                                                                       |
| UsePAM yes                                           | Specifies if PAM modules should be used for authentification.                                                                                                                                         |
| X11Forwarding no                                     | Specifies whether X11 forwarding is permitted.                                                                                                                                                        |
| PrintMotd no                                         | Specifies whether SSH daemon should print /etc/motd when a user logs in interactively.                                                                                                                |
| ClientAliveInterval 600                              | Sets a timeout interval in seconds, after which if no data has been received from the client, the SSH daemon will send a message through the encrypted channel to request a response from the client. |
| ClientAliveCountMax 0                                | Sets the number of client alive messages which may be sent without SSH daemon receiving any messages back from the client.                                                                            |
| AllowUsers (username)                                | This keyword can be followed by a list of user name patterns, separated by spaces. If specified, login is allowed only for user names that match one of the patterns.                                 |
| Protocol 2                                           | Specifies the usage of the newer protocol which is more secure.                                                                                                                                       |
| AuthenticationMethods publickey,keyboard-interactive | Specifies the authentication methods that must be successfully completed for a user to be granted access.                                                                                             |
| PasswordAuthentication no                            | Specifies whether password authentication is allowed.                                                                                                                                                 |

## 2FA
We can use Google Authenticator which generates a six-digit code (OTP) every 30 seconds to authenticate our identity. We can install it on our smartphone and we need Google-Authenticator PAM Module on our VPS. Emergency scratch codes should be saved safely in case we lose our phones.

After completing these security measures we can use [SCP](https://en.wikipedia.org/wiki/Secure_copy_protocol) to transfer our resources to the VPS.
