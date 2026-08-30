# 01. Azure Virtual Machines

Tags: azure, cloud, certification

Azure Virtual Machines (VMs) are the foundational IaaS offering in Azure. They provide complete control over compute resources, operating systems, and installed software, making them ideal for lift-and-shift migrations and workloads requiring custom configuration.

## Table of Contents

- [Virtualization concepts](#virtualization-concepts)
- [What is an Azure VM](#what-is-an-azure-vm)
- [VM sizes and series](#vm-sizes-and-series)
- [VM architecture components](#vm-architecture-components)
- [Networking](#networking)
- [Network security](#network-security)
- [Creating and managing VMs](#creating-and-managing-vms)
- [Real-world examples](#real-world-examples)

## Virtualization Concepts

**Virtualization**: The process of creating multiple virtual computers from a single physical server.

```
Physical Server (8-core, 64GB RAM)
├─ VM 1: Windows (2-core, 8GB)
├─ VM 2: Linux (2-core, 8GB)
├─ VM 3: Ubuntu (2-core, 8GB)
└─ VM 4: Windows (2-core, 8GB)

Result: 4 isolated environments from 1 physical machine
```

### Benefits

- **Cost Savings**: Multiple workloads on single hardware
- **Resource Efficiency**: Better utilization of CPU, memory, storage
- **Isolation**: Applications don't interfere with each other
- **Scalability**: Easy to add or remove VMs
- **Flexibility**: Different OS and software on each VM

## Hypervisor

**Definition**: Software that creates and manages virtual machines.

### Type 1 Hypervisor (Bare Metal)
Runs directly on hardware without an OS.

```
Hardware
└─ Hypervisor (Hyper-V, VMware ESXi)
   ├─ VM 1
   ├─ VM 2
   └─ VM 3
```

**Advantages**: Better performance, better security

### Type 2 Hypervisor (Hosted)
Runs on top of an operating system.

```
Hardware
└─ OS (Windows/Linux)
   └─ Hypervisor (VirtualBox, VMware Workstation)
      ├─ VM 1
      ├─ VM 2
      └─ VM 3
```

**Advantages**: Easier to set up

Azure uses Type 1 hypervisors for production workloads.

## What is an Azure VM

An Azure Virtual Machine is a cloud-based computer that you control completely.

### Capabilities

- Install any operating system (Windows Server, Linux, Ubuntu)
- Install custom software and tools
- Deploy applications
- Manage networking and security
- Full administrative control

### Use Cases

- Hosting websites and web applications
- Running backend services and APIs
- Development and testing environments
- Database servers
- Big data processing
- Lift-and-shift migration from on-premises

### Advantages

- Multiple OS support (Windows/Linux)
- Full control over configuration
- Pay-as-you-use pricing
- Highly scalable
- Secure networking
- Integration with other Azure services

## VM Sizes and Series

Azure VMs come in different sizes optimized for different workloads.

### General Purpose (B, D series)
**Best For**: Web servers, development, small databases

```
Characteristics:
├─ Balanced CPU and RAM ratio
├─ Good for most workloads
├─ Cost-effective
└─ Examples: D2s_v3, B2s
```

### Compute Optimized (F, H series)
**Best For**: High-traffic applications, batch processing, graphics rendering

```
Characteristics:
├─ High CPU relative to memory
├─ Fast processors
├─ Good for complex calculations
└─ Examples: F2s_v2, H16r
```

### Memory Optimized (E, M series)
**Best For**: Databases, in-memory caches, big data analytics

```
Characteristics:
├─ High RAM relative to CPU
├─ Great for data-intensive workloads
├─ Support for large datasets
└─ Examples: E4s_v3, M128s
```

### Storage Optimized (L series)
**Best For**: Data warehousing, NoSQL databases, large-scale analytics

```
Characteristics:
├─ High disk throughput
├─ Fast I/O operations
├─ Sequential read/write optimized
└─ Examples: L8s_v2, L16s_v2
```

## VM Architecture Components

### Resource Group
A container that holds all related Azure resources.

```
Resource Group: MyApp
├─ Virtual Machine
├─ Virtual Network
├─ Storage Account
├─ Network Interface
└─ Public IP
```

### Virtual Machine
The actual compute resource where your OS and applications run.

### Disks

**OS Disk**: Contains the operating system
```
Characteristics:
├─ Persistent (not deleted on restart)
├─ Default size: 30-128 GB
├─ Can be SSD or HDD
└─ Example: C:\ drive on Windows
```

**Data Disks**: Application data storage
```
Characteristics:
├─ Persistent (not deleted on restart)
├─ Optional, can add multiple
├─ Up to 32 TB per disk
└─ Examples: D:\ drive, additional storage
```

**Temporary Disk**: Cache and temporary files
```
Characteristics:
├─ Temporary (deleted on restart)
├─ Not suitable for persistent data
├─ Fast local SSD
└─ Should not be used for critical data
```

## Networking

### Virtual Network (VNet)
A private network in Azure for VM communication.

```
VNet: 10.0.0.0/16
├─ Subnet 1: 10.0.1.0/24 (Frontend)
│  └─ VM1 (10.0.1.4)
│  └─ VM2 (10.0.1.5)
└─ Subnet 2: 10.0.2.0/24 (Backend)
   └─ VM3 (10.0.2.4)
```

### Network Interface (NIC)
Connects VM to the network (like an Ethernet card).

```
VM
└─ NIC
   ├─ Private IP (10.0.1.4)
   ├─ Public IP (optional)
   └─ Connected to VNet Subnet
```

### IP Addresses

**Private IP**: Used for internal communication within VNet
```
Example: 10.0.1.4
Used for: VM-to-VM communication, database access
```

**Public IP**: Used to access VM from internet
```
Example: 40.123.45.67
Used for: RDP (port 3389), SSH (port 22), HTTP (port 80), HTTPS (port 443)
```

## Network Security

### Network Security Group (NSG)
A firewall that controls inbound and outbound traffic.

```
NSG Rules:
├─ Rule 1: Allow HTTPS (port 443) from Internet
├─ Rule 2: Allow HTTP (port 80) from Internet
├─ Rule 3: Allow RDP (port 3389) from specific IP only
└─ Rule 4: Deny everything else
```

### Common Ports

| Port | Protocol | Use Case |
|------|----------|----------|
| 80 | HTTP | Web traffic |
| 443 | HTTPS | Secure web traffic |
| 3389 | RDP | Remote Desktop (Windows) |
| 22 | SSH | Secure Shell (Linux) |
| 5432 | PostgreSQL | Database access |
| 3306 | MySQL | Database access |

### Load Balancer
Distributes traffic across multiple VMs.

```
Internet Traffic
↓
Azure Load Balancer (Public IP: 40.1.2.3)
├─ Forward to VM1 (10.0.1.4)
├─ Forward to VM2 (10.0.1.5)
└─ Forward to VM3 (10.0.1.6)

Benefits:
├─ High availability
├─ Load distribution
├─ Automatic health checks
└─ Failover support
```

## Creating and Managing VMs

### Create VM via Azure Portal

1. Go to Azure Portal → Virtual Machines
2. Click "Create" → "Virtual Machine"
3. Fill in configuration:
   - Resource Group
   - VM Name
   - Region
   - OS Image
   - Size
   - Authentication (password/SSH key)
4. Configure networking (VNet, subnet, NSG rules)
5. Review and create

### Create VM via Azure CLI

```bash
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_D2s_v3 \
  --admin-username azureuser \
  --generate-ssh-keys
```

### Connect to VM

**Windows VM (RDP)**:
```
Download .rdp file → Open with Remote Desktop → Enter credentials
```

**Linux VM (SSH)**:
```bash
ssh azureuser@40.123.45.67
```

### VM States

```
Deallocated (Stopped)
├─ Not running
├─ Not consuming compute resources
├─ Storage still costs
└─ Can restart instantly

Running
├─ Currently active
├─ Consuming compute resources
└─ Full functionality

Deleted
├─ Permanently removed
├─ Cannot recover (unless backed up)
└─ All resources freed
```

## Real-World Examples

### Example 1: Web Server VM

```csharp
// IIS Web Server running on Windows VM
// VM: Windows Server 2019, Standard_D2s_v3
// Public IP: 40.1.2.3
// NSG: Allow HTTP (80), HTTPS (443)

// Users access: http://40.1.2.3
// IIS serves requests
// Backend VM handles requests
```

### Example 2: Database Server VM

```
// PostgreSQL running on Linux VM
// VM: Ubuntu 20.04, Memory_Optimized (E4s_v3)
// Private IP: 10.0.2.4
// NSG: Allow port 5432 from Frontend subnet only

// Web servers (10.0.1.0/24) can connect
// Internet cannot connect directly
// Secure by default
```

### Example 3: Lift-and-Shift Migration

```
On-Premises:
└─ Windows Server 2012 with custom software

Migration Strategy:
1. Create disk from on-premises server
2. Upload to Azure storage
3. Create VM from custom image
4. Verify application works
5. Update DNS to point to Azure VM
6. Decommission on-premises server

Result: Zero-downtime migration
```

## Best Practices

- Use managed disks (simpler, better performance)
- Enable Azure Backup for data protection
- Use NSGs to restrict network access
- Keep OS and software updated
- Monitor VM performance with Azure Monitor
- Use availability sets or zones for high availability
- Clean up unused resources to control costs

## Summary

Azure Virtual Machines provide the foundation for many Azure workloads. They offer complete control over the computing environment, making them ideal for legacy application migration, development/testing, and any scenario requiring OS-level control. Understanding VM architecture, networking, and security is essential for Azure development and operations.
