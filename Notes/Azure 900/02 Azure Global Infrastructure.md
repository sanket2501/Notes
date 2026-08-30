# 02. Azure Global Infrastructure

Tags: azure, cloud

Azure's global infrastructure ensures high availability, disaster recovery, and compliance with data residency requirements. Understanding the hierarchy of physical and logical units is essential for designing resilient applications.

## Table of Contents

- [Azure infrastructure hierarchy](#azure-infrastructure-hierarchy)
- [Data centers](#data-centers)
- [Regions](#regions)
- [Availability zones](#availability-zones)
- [Region pairs](#region-pairs)
- [Geographies](#geographies)
- [Choosing the right deployment location](#choosing-the-right-deployment-location)

## Azure Infrastructure Hierarchy

```
Geography (e.g., United States, Europe)
└── Region (e.g., East US, West Europe)
    ├── Data Center
    ├── Data Center
    └── Data Center (Availability Zone 1, 2, 3)
```

## Data Centers

**Definition**: Physical buildings managed by Microsoft containing computing, storage, and networking hardware.

**Key Points**:
- Customers do not interact with data centers directly
- Resources are deployed to regions, not individual data centers
- Microsoft operates data centers with redundancy and security

## Regions

**Definition**: A geographical area containing one or more data centers (or 3+ Availability Zones).

**Key Functionality**:
- Provides deployment location choice
- Ensures low network latency to local users
- Enables data residency compliance

### Azure Regions Examples

| Region | Location | Use Case |
|--------|----------|----------|
| East US | Virginia | North American customers |
| West Europe | Netherlands | European customers |
| Southeast Asia | Singapore | Asia-Pacific customers |
| Australia East | Sydney | Australian customers |
| Japan East | Tokyo | Japanese customers |

### Benefits of Regions

```
User in New York
↓
Deploys to "East US" region
↓
Low latency (close proximity)
↓
Fast response times
```

**Real-world example**: A US-based SaaS company deploys to East US and West US for redundancy and low-latency access.

## Availability Zones

**Definition**: Physically separate data centers within a single Azure region with independent power, cooling, and networking.

**Key Points**:
- Minimum of three separate zones per supported region
- Each zone has independent infrastructure
- A failure in one zone does not affect others

### High Availability with AZs

```
Application deployed across 3 Availability Zones
├── Zone 1: VM instance (Running)
├── Zone 2: VM instance (Running)
└── Zone 3: VM instance (Running)

If Zone 1 fails:
├── Zone 1: VM instance (FAILED)
├── Zone 2: VM instance (Running) ← Traffic automatically rerouted
└── Zone 3: VM instance (Running) ← Traffic automatically rerouted

99.99% uptime SLA
```

### Types of Services

**Zonal Services**: You manually specify which zone to deploy to.

```
You choose: Zone 1, Zone 2, or Zone 3
Example: A specific Virtual Machine in Zone 2
```

**Zone-Redundant Services**: Azure automatically replicates across all zones.

```
You just create the service
Azure automatically replicates across all 3 zones
Example: Zone-Redundant Storage (ZRS)
```

## Region Pairs

**Definition**: Two regions within the same geography, separated by at least 300 miles.

### Purpose

```
Region 1 (Primary)           Region 2 (Paired)
└─ East US                   └─ West US
   (Customer data)              (Backup data)
   
Planned Azure maintenance applied sequentially:
1. Apply update to West US (Region 2)
2. Wait for validation
3. Apply update to East US (Region 1)

This ensures continuous availability
```

### Examples of Region Pairs

| Primary Region | Paired Region | Geography |
|----------------|---------------|-----------|
| East US | West US | United States |
| West Europe | North Europe | Europe |
| Southeast Asia | East Asia | Asia Pacific |
| Australia East | Australia Southeast | Australia |
| Japan East | Japan West | Japan |

**Real-world benefit**: If an entire region fails (earthquake, regional power outage), your application automatically failsover to the paired region.

## Geographies

**Definition**: A discrete market (e.g., Europe, United States, Asia Pacific) that serves as a data residency boundary.

### Compliance and Data Residency

```
GDPR Requirement (Europe):
└─ Personal data must stay in Europe
   ├── Choose West Europe region ✓
   ├── Choose North Europe region ✓
   └── Do NOT choose East US region ✗

China Data Residency:
└─ Chinese data must stay in China
   ├── Choose China East region ✓
   └── Do NOT choose any other region ✗
```

**Real-world example**: A financial company processing data for EU customers must deploy to West Europe to comply with GDPR.

## High Availability vs. Disaster Recovery

| Goal | Azure Component | Protection Level |
|------|-----------------|------------------|
| **High Availability** | Availability Zones | Protects against single data center failure (localized) |
| **Disaster Recovery** | Region Pairs | Protects against entire region failure (catastrophic) |
| **Compliance** | Geographies | Ensures data stays within legal/political boundary |

### Visual: Layered Protection

```
Level 1: Availability Zones (within region)
└─ Protects against local DC failure
   └─ 99.99% uptime SLA

Level 2: Region Pairs (within geography)
└─ Protects against regional failure
   └─ Automatic failover capability

Level 3: Geographies (global)
└─ Ensures data residency compliance
   └─ GDPR, China data residency, etc.
```

## Choosing the Right Deployment Location

### Decision Matrix

```
Question 1: Compliance requirement for data location?
├─ YES → Choose specific Geography
└─ NO → Move to Question 2

Question 2: Need protection against single data center failure?
├─ YES → Deploy across multiple Availability Zones
└─ NO → Deploy to single zone (cost savings)

Question 3: Need protection against entire region failure?
├─ YES → Set up geo-replication to paired region
└─ NO → Deploy only to primary region
```

### Real-World Example: E-Commerce Application

```
Customer Base: 
- 70% in North America
- 20% in Europe
- 10% in Asia

Deployment Strategy:
1. Primary: East US (zone-redundant) - For NA customers
2. Paired: West US (zone-redundant) - For DR/failover
3. Secondary: West Europe (zone-redundant) - For EU customers (GDPR)
4. Tertiary: Southeast Asia (single zone) - For Asia-Pacific (lower priority)

Result: Low latency + High availability + Compliance
```

## Summary

Azure's infrastructure hierarchy from data centers → regions → availability zones → geographies provides multiple levels of reliability and compliance. Choosing the right location and redundancy strategy is crucial for designing resilient, compliant applications that serve global customers.
