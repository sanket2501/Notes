# 04. Azure SQL Managed Instance

Tags: azure, cloud, database

Azure SQL Managed Instance is a fully managed cloud database service that provides near 100% compatibility with SQL Server. It's designed for lift-and-shift migrations from on-premises SQL Server with minimal changes to applications.

## Table of Contents

- [What is SQL Managed Instance](#what-is-sql-managed-instance)
- [Managed Instance vs. SQL Database](#managed-instance-vs-sql-database)
- [Use cases](#use-cases)
- [Key features](#key-features)
- [Architecture](#architecture)
- [High availability](#high-availability)
- [Migration to Managed Instance](#migration-to-managed-instance)
- [Security](#security)
- [Real-world examples](#real-world-examples)

## What is SQL Managed Instance

**Definition**: A fully managed cloud database service with near 100% SQL Server compatibility.

**Purpose**: Enable lift-and-shift migration of on-premises SQL Server databases to the cloud with minimal code changes.

### Why Use Managed Instance?

```
On-Premises SQL Server:
├─ Maintenance overhead (patching, backups, etc.)
├─ Capital investment in hardware
├─ Limited scalability
└─ High operational cost

Azure SQL Managed Instance:
├─ Microsoft manages all infrastructure
├─ No capital investment
├─ Unlimited scalability
├─ Lower operational cost
└─ SQL Server compatibility
```

## Managed Instance vs. SQL Database

### Comparison Table

| Feature | Managed Instance | SQL Database |
|---------|-----------------|--------------|
| **SQL Server Compatibility** | ~100% | ~70% |
| **Instance-level Features** | Supported (SQL Agent, Linked Servers, CLR) | Limited |
| **Service Model** | Fully isolated | Shared (with controls) |
| **Migration Effort** | Low (lift-and-shift) | Medium (code changes) |
| **Use Case** | Legacy SQL Server apps | Cloud-native apps |
| **Pricing** | vCore-based | DTU or vCore-based |

### When to Use Each

**Use Managed Instance When**:
- Migrating legacy SQL Server databases
- Using SQL Agent Jobs for scheduled tasks
- Using Linked Servers to connect other databases
- Using Cross-database queries
- Using CLR (Common Language Runtime)
- Need 100% SQL Server compatibility

**Use SQL Database When**:
- Building cloud-native applications
- Want maximum managed service simplicity
- Cost is critical (Elastic Pools cheaper)
- Using modern C# and .NET

## Use Cases

### Lift-and-Shift Migration

```
Before:
└─ On-Premises Datacenter
   └─ SQL Server 2019
      ├─ Enterprise database
      ├─ SQL Agent jobs
      ├─ Linked servers
      └─ Custom CLR functions

Challenge: Direct migration to SQL Database = code changes

Solution: Azure SQL Managed Instance
├─ Create Managed Instance
├─ Migrate database (backup/restore)
├─ All features work as-is
└─ Minimal application changes
```

### Multi-database Scenarios

```
Application needs:
├─ Database 1: Business data
├─ Database 2: Analytics
├─ Database 3: Reference data

Using SQL Database:
├─ Separate SQL servers (cross-database queries impossible)

Using Managed Instance:
├─ One instance with all 3 databases
├─ Cross-database queries work naturally
└─ Minimal code changes from on-premises
```

## Key Features

### Fully Managed Service

```
What Microsoft manages:
├─ Operating system
├─ SQL Server software
├─ Patches and updates
├─ Backups and recovery
├─ High availability
├─ Failover handling
└─ Hardware scaling

What you manage:
├─ Databases
├─ Queries
├─ User access
├─ Application logic
└─ Performance tuning
```

### High SQL Server Compatibility

```
Supported features:
├─ SQL Agent Jobs
│  └─ Scheduled tasks, backups, maintenance
├─ Linked Servers
│  └─ Connect to other SQL Server instances
├─ Cross-Database Queries
│  └─ Query multiple databases in same instance
├─ CLR (Common Language Runtime)
│  └─ Run C# code in database
├─ Service Broker
│  └─ Asynchronous messaging
└─ Database Mail
   └─ Send emails from database
```

### Built-in High Availability

```
Architecture:
Primary Instance
├─ Automatically replicated to
└─ Secondary replicas

Automatic failover:
├─ If primary fails
├─ Secondary becomes primary
├─ Zero downtime
└─ 99.99% uptime SLA

No manual setup required
```

### Automatic Backups

```
Backup types:
├─ Full backup: Weekly
├─ Differential backup: Daily
├─ Transaction log backup: Every 10 minutes
└─ Retention: 7-35 days

Recovery options:
├─ Point-in-time restore (PITR)
├─ Geo-restore (restore to different region)
└─ Long-term retention (years)
```

## Architecture

### Managed Instance Architecture

```
Virtual Network (VNet)
└─ Dedicated subnet
   └─ Azure SQL Managed Instance
      ├─ System databases (master, msdb, tempdb)
      ├─ User databases
      └─ SQL Agent
      
Internal only:
├─ Private IP address
├─ No public access (unless configured)
└─ Accessible from applications in same VNet or via ExpressRoute
```

### Network Isolation

```
On-Premises Network
└─ VPN / ExpressRoute
   └─ Azure VNet
      └─ Managed Instance (Private IP)

Benefits:
├─ Network isolation
├─ Secure communication
├─ Private IP only (by default)
└─ Can enable public endpoint (if needed)
```

## High Availability

### Failover Groups

```
Primary Instance (East US)
├─ Automatically replicated to
└─ Secondary Instance (West US)

Client connection:
├─ Points to failover group listener
├─ Automatic failover on primary failure
└─ No connection string changes

Result: Transparent failover to secondary
```

### Read Replicas

```
Primary Instance (read/write)
└─ Replicated to
   └─ Read Replicas (read-only)

Benefits:
├─ Distribute read queries
├─ Reduce load on primary
└─ Geo-distributed query processing
```

## Migration to Managed Instance

### Step 1: Prepare

```
Assessment:
├─ Check SQL Server version compatibility
├─ Identify incompatible features
├─ Plan VNet configuration
├─ Size appropriate vCore count
└─ Plan cutover approach
```

### Step 2: Provision

```
Create Managed Instance:
├─ Configure VNet and subnet
├─ Choose vCore size
├─ Configure storage
├─ Enable backups
└─ Configure security
```

### Step 3: Migrate Database

**Option 1: Backup and Restore**
```csharp
// 1. Take full backup on-premises
// 2. Upload to Azure Storage
// 3. Restore in Managed Instance

// Restore command
RESTORE DATABASE MyDatabase
FROM URL = 'https://storage.blob.core.windows.net/backups/mydb.bak'
```

**Option 2: Azure Database Migration Service (DMS)**
```
GUI-based migration:
├─ Minimal downtime migration
├─ Real-time data synchronization
├─ Automatic schema creation
└─ Validation reports
```

**Option 3: Transaction Replication**
```
On-Premises → Azure (initial copy)
├─ Continuous synchronization
├─ Cutover when ready
└─ Minimal downtime
```

### Step 4: Validate

```
Post-migration:
├─ Verify data integrity
├─ Test application connectivity
├─ Performance benchmarks
├─ User acceptance testing
└─ Cutover approval
```

## Security

### Network Security

```
Security layers:
├─ VNet isolation (network level)
├─ Subnet restrictions
├─ Network Security Groups (NSG)
├─ Private endpoint (no public internet exposure)
└─ VPN / ExpressRoute for on-premises connectivity
```

### Data Protection

```
Encryption at rest (TDE):
└─ Database files encrypted on disk

Encryption in transit (TLS):
└─ Network traffic encrypted

Column-level encryption:
└─ Sensitive data encrypted with client keys
```

### Authentication

```
SQL Authentication:
├─ Username and password
└─ Less secure

Azure AD Authentication:
├─ Managed identity
├─ MFA support
├─ Centralized identity management
└─ Recommended
```

### Audit and Compliance

```
Auditing:
├─ Track database changes
├─ Monitor access
└─ Compliance reporting

Threat Detection:
├─ Anomaly detection
├─ Security alerts
└─ Response recommendations

Compliance:
├─ ISO certification
├─ GDPR compliant
├─ SOC 2 certified
└─ Industry-specific compliance
```

## Real-World Examples

### Example 1: Enterprise Database Migration

```
Current state:
├─ On-Premises SQL Server 2019
├─ Database size: 500 GB
├─ 100 concurrent users
├─ SQL Agent jobs for backups
├─ Linked servers to data warehouse
└─ Annual maintenance downtime

Migration approach:
1. Provision Managed Instance (8 vCore)
2. Backup on-premises database
3. Restore to Managed Instance
4. Update application connection strings
5. Run SQL Agent jobs (unchanged)
6. Validate Linked Server connections
7. Cutover with 2-hour downtime

Result:
├─ Zero maintenance operations needed
├─ Automatic backups
├─ 99.99% uptime SLA
├─ 40% cost reduction vs. on-premises
└─ Minimal application changes
```

### Example 2: Bank Core System

```
Requirements:
├─ Multiple databases (Business, Risk, Analytics)
├─ Cross-database queries
├─ GDPR compliance
├─ 99.99% uptime SLA
├─ Disaster recovery capability

Solution:
└─ Azure SQL Managed Instance
   ├─ VNet in East US (primary)
   ├─ Failover group to West US (secondary)
   ├─ Data encrypted at rest (TDE)
   ├─ Azure AD authentication
   ├─ Audit logging enabled
   ├─ 16 vCore Standard tier
   └─ All databases in single instance (cross-database queries work)

Benefits:
├─ Banks not need to maintain servers
├─ Automatic failover for DR
├─ Meets compliance requirements
├─ Lower operational cost
└─ SQL Agent jobs continue working
```

### Example 3: ISV Product

```
Scenario:
├─ Independent software vendor (ISV)
├─ On-premises SQL Server application
├─ Many customers (multi-tenant)
├─ Each customer has own database

Migration plan:
1. Create Elastic Pool (if cloud-native redesign)
2. Create Managed Instance (if minimal changes)
3. Each customer database on Managed Instance
4. Central management

Using Managed Instance:
├─ Leverage existing SQL code
├─ Reuse existing tools
├─ Migrate quickly (weeks, not months)
├─ Lower risk of breaking changes
└─ Customer data isolation at database level
```

## Best Practices

- Use Managed Instance for legacy SQL Server applications
- Enable failover groups for high availability
- Use Azure AD authentication for security
- Enable Transparent Data Encryption (TDE)
- Configure network isolation (private endpoint)
- Plan VNet sizing appropriately
- Test migration thoroughly before cutover
- Monitor performance after migration
- Use Azure Advisor for optimization recommendations
- Establish backup and recovery procedures

## Summary

Azure SQL Managed Instance is the best choice for organizations seeking to lift-and-shift SQL Server databases to the cloud with minimal application changes. Its high SQL Server compatibility, fully managed operations, and built-in high availability make it ideal for enterprise database migrations. Organizations can reduce operational overhead while maintaining compatibility with existing tools and code.
