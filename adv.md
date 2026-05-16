# Azure Connectivity Services — Points to Remember

## 1. Point-to-Site (P2S) VPN

### What is it?

Point-to-Site VPN allows an individual client device (Laptop/Desktop) to securely connect to an Azure Virtual Network over the internet.

### Common Use Cases

* Remote employee access
* Work from home connectivity
* Admin access to Azure VMs
* Developer secure access to private resources

### Key Points to Remember

* Client-to-Azure connection
* Uses VPN Gateway
* Secure encrypted tunnel over internet
* No public IP required on Azure VM
* Supports:

  * OpenVPN
  * IKEv2
  * SSTP
* Authentication methods:

  * Azure AD
  * Certificate-based
  * RADIUS
* Works on:

  * Windows
  * macOS
  * Linux
* GatewaySubnet is mandatory
* Requires VPN Client installation on local machine

### Important Ports

* SSTP → TCP 443
* IKEv2 → UDP 500 & UDP 4500

### Real-Time Example

Employee laptop connecting securely to Azure VM from home.

---

# 2. Site-to-Site (S2S) VPN

### What is it?

Site-to-Site VPN connects an entire on-premises network/datacenter to Azure VNet using VPN devices/firewalls.

### Common Use Cases

* Hybrid cloud setup
* Datacenter to Azure connectivity
* Branch office connectivity
* DR (Disaster Recovery)

### Key Points to Remember

* Network-to-network connection
* Entire office network can access Azure
* Requires:

  * VPN Gateway in Azure
  * VPN device/firewall on-premises
* Uses IPSec/IKE VPN tunnel
* Requires public IP on on-prem firewall/router
* Supports BGP routing
* Encrypted communication over internet

### Requirements

* GatewaySubnet
* Local Network Gateway
* VPN Gateway
* Shared Key (PSK)

### Real-Time Example

Company office network connected to Azure production environment.

---

# 3. ExpressRoute

### What is it?

ExpressRoute provides private dedicated connectivity between on-premises infrastructure and Azure without using the public internet.

### Common Use Cases

* Enterprise workloads
* High-speed connectivity
* Banking applications
* SAP workloads
* Low latency production systems

### Key Points to Remember

* Private connection to Azure
* Does NOT use public internet
* More secure and reliable
* Lower latency
* Higher bandwidth
* SLA-backed connectivity
* Supports Microsoft Peering & Private Peering
* Requires telecom/connectivity provider

### Connectivity Models

* Any-to-any (IPVPN)
* Point-to-point Ethernet
* Colocation

### ExpressRoute Features

* Up to 100 Gbps bandwidth
* Supports global connectivity
* Supports hybrid cloud
* Faster than VPN

### Real-Time Example

Large enterprise connecting datacenter to Azure using leased fiber/MPLS circuit.

---

# Difference Chart

| Feature                  | Point-to-Site VPN | Site-to-Site VPN     | ExpressRoute                 |
| ------------------------ | ----------------- | -------------------- | ---------------------------- |
| Connectivity Type        | Client to Azure   | Network to Azure     | Private Dedicated Connection |
| Uses Internet            | Yes               | Yes                  | No                           |
| Encryption               | Yes               | Yes                  | Optional                     |
| Performance              | Moderate          | Moderate             | High                         |
| Latency                  | Higher            | Moderate             | Low                          |
| Cost                     | Low               | Medium               | High                         |
| Setup Complexity         | Easy              | Moderate             | Complex                      |
| Scalability              | Small users       | Medium/Large offices | Enterprise scale             |
| Requires VPN Device      | No                | Yes                  | Yes                          |
| Requires ISP Provider    | No                | No                   | Yes                          |
| Security Level           | Good              | Better               | Highest                      |
| Typical Users            | Remote employees  | Organizations        | Large Enterprises            |
| Azure Component          | VPN Gateway       | VPN Gateway          | ExpressRoute Circuit         |
| Public Internet Exposure | Yes               | Yes                  | No                           |
| Reliability              | Moderate          | Good                 | Very High                    |
| Bandwidth                | Lower             | Medium               | Very High                    |

---

# Quick Interview Points

## P2S

* Individual device connectivity
* Ideal for remote users
* Requires VPN client

## S2S

* Connects entire network
* Hybrid cloud architecture
* Uses IPSec tunnel

## ExpressRoute

* Private dedicated connection
* No internet usage
* Enterprise-grade connectivity
* Lowest latency

---

# Easy Memory Trick

| Service      | Easy Understanding         |
| ------------ | -------------------------- |
| P2S          | One Laptop → Azure         |
| S2S          | Office Network → Azure     |
| ExpressRoute | Private Fiber/MPLS → Azure |

---

# Architecture Flow

## Point-to-Site

Local Laptop → Internet → VPN Gateway → Azure VNet

## Site-to-Site

Office Firewall → IPSec Tunnel → Azure VPN Gateway → Azure VNet

## ExpressRoute

Datacenter/MPLS Provider → ExpressRoute Circuit → Azure VNet

---

# Azure Services Used

* Microsoft [Microsoft Azure](https://azure.microsoft.com/?utm_source=chatgpt.com)
* Azure VPN Gateway
* Azure ExpressRoute
* Azure Virtual Network
