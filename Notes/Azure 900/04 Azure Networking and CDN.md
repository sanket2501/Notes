# 04. Azure Networking and CDN

Tags: azure, cloud

Azure networking services provide the foundation for secure communication between resources. Understanding virtual networks, load balancing, and content delivery is essential for building scalable and resilient applications.

## Table of Contents

- [Azure Virtual Network (VNet)](#azure-virtual-network-vnet)
- [VNet components](#vnet-components)
- [Azure VPN Gateway](#azure-vpn-gateway)
- [Load Balancing](#load-balancing)
- [Azure Content Delivery Network](#azure-content-delivery-network)
- [Network security](#network-security)

## Azure Virtual Network (VNet)

**Definition**: A logically isolated representation of your network in the cloud.

**Key Functionality**:
- Isolation and segmentation
- Communication between resources
- Scoped to a single region
- Can be connected across regions using VNet Peering

### VNet Architecture

```
Azure Virtual Network (East US Region)
│
├─ Subnet 1 (10.0.1.0/24)
│  ├─ Virtual Machine 1
│  └─ Virtual Machine 2
│
├─ Subnet 2 (10.0.2.0/24)
│  └─ Web App
│
└─ Subnet 3 (10.0.3.0/24)
   └─ Database Server
```

### Real-World Use Case

```
Traditional Network:
192.168.1.0/24
├─ Workstations (192.168.1.0-100)
├─ Servers (192.168.1.100-150)
└─ Printers (192.168.1.150-200)

Azure VNet (Replicate this):
10.0.0.0/16
├─ Subnet: VMs (10.0.1.0/24)
├─ Subnet: Web Services (10.0.2.0/24)
├─ Subnet: Database (10.0.3.0/24)
└─ VPN Gateway → Connect to on-premises
```

## VNet Components

### Subnets

**Definition**: A logical division of a VNet's address space.

```
VNet: 10.0.0.0/16 (65,536 addresses)
│
├─ Subnet 1: 10.0.1.0/24 (256 addresses)
├─ Subnet 2: 10.0.2.0/24 (256 addresses)
└─ Subnet 3: 10.0.3.0/24 (256 addresses)

Benefits:
- Logical segmentation
- Security group management
- Improved address allocation
```

### Network Security Groups (NSG)

**Definition**: A firewall that controls inbound/outbound traffic.

```csharp
// Deny all incoming traffic except HTTPS
var inboundRule = new SecurityRule
{
    Name = "AllowHTTPS",
    Protocol = "Tcp",
    DestinationPortRange = "443",
    Access = "Allow",
    Direction = "Inbound"
};

// Result: Only HTTPS traffic reaches the VM
```

### Virtual Network Peering

**Definition**: Connect two VNets as if they were on the same network.

```
VNet 1 (East US)          VNet 2 (West US)
├─ VM1                    ├─ VM2
└─ Database               └─ Analytics

Peering enabled
↓
VM1 can communicate directly with VM2
↓
VM1 can query Analytics database in West US
```

## Azure VPN Gateway

**Definition**: Sends encrypted traffic across the public internet to connect on-premises networks to Azure.

### Use Cases

**Site-to-Site VPN**: Connect entire office/datacenter to Azure

```
On-Premises Datacenter
├─ 100 employees
├─ File servers
└─ Applications

VPN Gateway
↓ (Encrypted tunnel)
↓
Azure VNet
├─ Cloud resources
└─ Hybrid cloud services

Result: Seamless hybrid environment
```

**Point-to-Site VPN**: Connect individual remote workers to Azure

```
Remote Employee Laptop
↓
VPN Client software
↓
Azure VPN Gateway
↓
Azure VNet resources

Result: Secure remote access
```

### Real-World Hybrid Scenario

```
Company A Expansion:
Phase 1: Keep on-premises datacenter
├─ Email servers
├─ File servers
└─ Legacy applications

Phase 2: Migrate new workloads to Azure
├─ Web applications
├─ Databases
└─ Analytics

Phase 3: Integration
├─ On-premises servers ↔ Azure VPN Gateway ↔ Azure VNet
├─ Employees access both seamlessly
└─ Gradual migration possible
```

## Load Balancing

Azure provides two load balancing services for different scenarios.

### Azure Load Balancer

**Layer**: OSI Layer 4 (TCP/UDP)

**Use Case**: Ultra-high-performance, low-latency load balancing

**Functionality**:
- Route traffic based on IP protocol data
- Internal (private) or External (internet-facing)
- High availability for TCP/UDP applications

### Architecture

```
Internet Traffic
↓
Azure Load Balancer
├─ Rules: Forward :80 to backend pool on :8080
└─ Health probe: Check VM health every 15 seconds
  ├─ Healthy VM1 → Forward traffic
  ├─ Unhealthy VM2 → Skip this VM
  └─ Healthy VM3 → Forward traffic

Result: Traffic distributed only to healthy VMs
```

### Azure Application Gateway

**Layer**: OSI Layer 7 (HTTP/HTTPS)

**Use Case**: Advanced routing for web applications

**Key Features**:
- URL path-based routing
- Host-based routing
- Web Application Firewall (WAF)
- SSL/TLS termination
- Session affinity (sticky sessions)

### Path-Based Routing Example

```
Application Gateway receives request
│
├─ /api/* → Route to API backend pool (VMs)
├─ /images/* → Route to static storage (CDN)
└─ /admin/* → Route to admin backend pool (VMs)

Single entry point
↓
Intelligent routing based on URL path
↓
Simplified architecture
```

### Comparison

| Feature | Load Balancer | Application Gateway |
|---------|---------------|-------------------|
| **Layer** | Layer 4 (TCP/UDP) | Layer 7 (HTTP/HTTPS) |
| **Routing** | IP-based | URL/host-based |
| **WAF** | No | Yes |
| **SSL Termination** | No | Yes |
| **Session Affinity** | No | Yes (cookie-based) |
| **Use Case** | Non-HTTP protocols | Web applications |

## Azure Content Delivery Network

**Definition**: A global, distributed network of servers designed to accelerate content delivery to users worldwide.

### How CDN Works

```
User in Tokyo requests image from US-hosted website
│
├─ Without CDN:
│  └─ Request → US Server (slow, high latency)
│
└─ With CDN:
   ├─ Request to nearest POP (Point of Presence)
   ├─ POP has cached copy
   └─ Image delivered from Tokyo POP (fast, low latency)

Result: ~80% faster content delivery
```

### Benefits

**Latency Reduction**: Content served from nearest edge location

**Bandwidth Savings**: Static content cached at POPs, reducing origin server load

**Availability**: If origin server fails, cached content still available

### Real-World Example

```
Global E-Commerce Site
├─ Product images stored in Azure East US
├─ Users in Europe, Asia, Australia
│
├─ Without CDN:
│  └─ Every user downloads from East US
│     └─ Slow for users far away
│
└─ With Azure CDN:
   ├─ Images cached at:
   │  ├─ Amsterdam POP (Europe users)
   │  ├─ Singapore POP (Asia users)
   │  └─ Sydney POP (Australia users)
   └─ Result: Fast, local delivery globally
```

### CDN Configuration

```
Static Website in Azure Storage
↓
Azure CDN Profile
├─ Origin: Azure Storage (origin.azurewebsites.net)
├─ TTL: 1 week
└─ Caching rules: Cache images, but not HTML
↓
User Requests
├─ First request: Downloaded from origin
├─ Cached at nearest POP
└─ Subsequent requests: Served from POP cache
```

## Network Security

### Defense in Depth

```
Level 1: Network Security Group (NSG)
├─ Firewall rules
└─ Protocol-based access

Level 2: Application Gateway WAF
├─ Protect against SQL injection
├─ Protect against XSS
└─ Protect against CSRF

Level 3: Application Layer
├─ HTTPS/TLS encryption
├─ Authentication
└─ Input validation

Level 4: Data Layer
├─ Database firewalls
├─ Encryption at rest
└─ Column-level encryption
```

### Real-World Security Example

```
Public Web Application
│
├─ NSG allows only HTTPS (:443)
├─ Application Gateway with WAF
│  └─ Block SQL injection attempts
├─ App Service running HTTPS only
│  └─ All communication encrypted
└─ SQL Database
   └─ IP whitelist restricted to App Service
   └─ Data encrypted at rest

Result: Multi-layer defense
```

## Summary

Azure networking provides the plumbing for distributed applications. Virtual Networks provide isolation and structure, VPN Gateways enable hybrid cloud, Load Balancers distribute traffic intelligently, and CDN accelerates content delivery globally. Together, these services form the foundation for scalable, secure, globally-accessible applications.
