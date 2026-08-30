# 05. Azure Cosmos DB

Tags: azure, cloud, database, nosql

Azure Cosmos DB is a globally distributed, multi-model, NoSQL database service. It provides high availability, low latency, and elastic scalability for modern applications that require flexible, schema-free data models.

## Table of Contents

- [What is Cosmos DB](#what-is-cosmos-db)
- [Core characteristics](#core-characteristics)
- [Data models and APIs](#data-models-and-apis)
- [Consistency models](#consistency-models)
- [Throughput and scaling](#throughput-and-scaling)
- [Partitioning](#partitioning)
- [Global distribution](#global-distribution)
- [Real-world examples](#real-world-examples)

## What is Cosmos DB

**Definition**: A globally distributed, multi-model database service with automatic scaling and replication.

**Purpose**: Store and query flexible, semi-structured data (JSON documents) across multiple regions with guaranteed performance.

### Cosmos DB vs. Traditional SQL Database

```
Traditional SQL:
├─ Schema-based (rigid)
├─ Single region (or replicated)
├─ Scaling is complex
└─ Transactions on single server

Cosmos DB:
├─ Schema-free (flexible)
├─ Multi-region (automatic replication)
├─ Automatic scaling
└─ Multi-region transactions
```

## Core Characteristics

### Document-Based

Cosmos DB stores data as JSON documents.

```json
{
  "id": "customer-001",
  "name": "John Doe",
  "email": "john@example.com",
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "state": "NY"
  },
  "orders": [
    { "orderId": "order-1", "amount": 99.99 },
    { "orderId": "order-2", "amount": 149.99 }
  ]
}
```

### Schema Flexibility

```
Database 1: Customer document
└─ {name, email, phone}

Database 2: Product document
└─ {title, description, price, category}

Database 3: Order document
└─ {customerId, items[], total, status}

No predefined schema required
All in same collection
```

### Automatic Scaling

```
Initial workload: 400 RU/s
├─ 1000 requests/second
├─ Performance: Good

Traffic spike: 2000 RU/s needed
├─ Cosmos DB automatically scales
├─ No code changes
└─ Performance: Maintained

Low traffic: 100 RU/s needed
├─ Cosmos DB automatically scales down
├─ Cost reduced
└─ Performance: Maintained
```

### Global Distribution

```
Write to one region:
└─ East US (primary write)

Automatically replicated to:
├─ West US
├─ Europe (West)
├─ Southeast Asia
└─ Australia (East)

Reads can happen from any region:
├─ User in New York → Read from East US (fast)
├─ User in London → Read from Europe (fast)
└─ User in Singapore → Read from Southeast Asia (fast)
```

## Data Models and APIs

### Document API (MongoDB API)

```csharp
// Store and query JSON documents like MongoDB
var client = new MongoClient(connectionString);
var db = client.GetDatabase("mydb");
var collection = db.GetCollection<BsonDocument>("customers");

// Insert document
var doc = new BsonDocument
{
    { "name", "John" },
    { "email", "john@example.com" }
};
await collection.InsertOneAsync(doc);

// Query
var filter = Builders<BsonDocument>.Filter.Eq("name", "John");
var result = await collection.Find(filter).ToListAsync();
```

### SQL API (Query Language)

```sql
-- Query JSON documents with SQL
SELECT * FROM customers c WHERE c.name = "John"

SELECT c.name, COUNT(o.orderId) as OrderCount
FROM customers c
JOIN orders o IN c.orders
GROUP BY c.name
```

### Table API (Key-Value Store)

```csharp
// Similar to Azure Table Storage
var tableClient = new TableClient(uri, "customers", credential);

// Insert entity
var entity = new TableEntity("partition1", "row1")
{
    { "Name", "John" },
    { "Email", "john@example.com" }
};
await tableClient.AddEntityAsync(entity);
```

### Gremlin API (Graph Database)

```
Graph structure:
└─ Nodes: Customers, Products
   └─ Edges: Purchased, Related

Query: "Which products were bought by customers who also bought product X?"
└─ Gremlin query: Traverse graph edges
```

## Consistency Models

Cosmos DB offers 5 consistency levels to balance consistency and performance.

### Strong Consistency

```
Write operation
├─ Replicated to all regions
├─ All replicas acknowledged
└─ Read always sees latest

Characteristics:
├─ Guaranteed consistency
├─ Highest latency
└─ Lowest throughput

Use Case: Financial transactions
```

### Eventual Consistency

```
Write operation
├─ Acknowledged after local write
├─ Replication continues in background
└─ Read might see old data temporarily

Characteristics:
├─ No consistency guarantee (initially)
├─ Lowest latency
├─ Highest throughput

Use Case: Social media feeds
```

### Session Consistency (Default)

```
Write operation by user A
├─ Replicated to regions
├─ Session token updated

Read by user A
├─ Uses session token
├─ Sees user A's own writes

Read by user B
├─ Might see slightly old data

Characteristics:
├─ Balanced consistency/latency
├─ Good for applications
└─ Recommended default
```

## Throughput and Scaling

### Request Units (RU)

**RU (Request Unit)**: Unit of throughput billing.

```
Cost calculation:
└─ 1 RU = CPU, memory, I/O for small operation

Examples:
├─ Read 1 KB document: 1 RU
├─ Write 1 KB document: 5 RU
├─ Query with aggregation: 5-10 RU
└─ Complex query: 20+ RU
```

### Provisioned Throughput

```
Configuration:
├─ Dedicated throughput: 400-250,000 RU/s
└─ Shared throughput: Minimum 100 RU/s

Billing:
└─ Monthly cost = provisioned RU/s
   └─ Example: 400 RU/s = $0.13/hour
```

### Autoscale Throughput

```
Configuration:
├─ Min: 4,000 RU/s
├─ Max: 40,000 RU/s
└─ Automatically scales between min/max

Billing:
└─ Hourly rate based on actual RU/s used
   └─ More cost-effective for variable workloads
```

## Partitioning

### Partition Key

Cosmos DB uses partition key to distribute data.

```
Collection: Orders
├─ Partition key: customerId
├─ Document 1: {customerId: "C001", orderId: 1}
├─ Document 2: {customerId: "C001", orderId: 2}
├─ Document 3: {customerId: "C002", orderId: 3}
└─ Document 4: {customerId: "C002", orderId: 4}

Logical partitions:
├─ Partition 1: All orders for C001 and C002
├─ Partition 2: All orders for C003 and C004
└─ Partition N: Other customer orders

Physical partitions:
└─ Each logical partition replicated across regions
```

### Partition Key Selection

```
Good partition key:
├─ Distributes data evenly (customerId)
├─ Queries use partition key
├─ Grows uniformly over time
└─ Examples: userId, customerId, tenantId

Bad partition key:
├─ Always same value (status: "active")
├─ Uneven distribution (lastModified date)
├─ Creates hot partitions (bottleneck)
└─ Hurts performance and scalability
```

## Global Distribution

### Multi-Region Replication

```
Write Region (Primary):
└─ East US
   └─ All writes go here

Read Regions:
├─ West US
├─ Europe (West)
├─ Southeast Asia
└─ Australia (East)

Data flow:
├─ Write to East US
├─ Automatically replicated
└─ Available in all regions for reading
```

### Failover Handling

```
Primary Region (East US) fails
│
Cosmos DB automatically:
├─ Detects failure
├─ Promotes secondary region (West US) to primary
├─ Updates write endpoint
└─ Application connection string points to new primary

Result:
├─ Zero downtime
├─ Automatic failover
└─ No manual intervention
```

## Real-World Examples

### Example 1: Social Media Application

```
Collection: Posts
├─ Partition key: userId
├─ Documents:
   {
     "id": "post-001",
     "userId": "user123",
     "content": "Hello world",
     "timestamp": "2024-01-15T10:00:00Z",
     "likes": 42,
     "comments": [
       {"userId": "user456", "text": "Great post!"}
     ]
   }

Characteristics:
├─ Schema-free (some posts have images, some don't)
├─ Flexible structure (nested comments)
├─ High read volume (feeds)
├─ Global distribution (users worldwide)

Cosmos DB benefits:
├─ Scale automatically during peak hours
├─ Read from nearest region (low latency)
├─ Session consistency (users see their posts)
└─ JSON documents fit naturally
```

### Example 2: IoT Telemetry

```
Collection: SensorData
├─ Partition key: deviceId
├─ Documents:
   {
     "id": "telemetry-001",
     "deviceId": "sensor-123",
     "timestamp": "2024-01-15T10:15:30Z",
     "temperature": 22.5,
     "humidity": 65,
     "pressure": 1013.25
   }

High volume:
├─ Millions of sensors
├─ Continuous data stream
├─ Data retention: Years of historical data

Cosmos DB advantages:
├─ Automatic scaling to handle volume
├─ Multi-region replication for disaster recovery
├─ TTL (Time To Live) for automatic cleanup
└─ Analytical queries on historical data
```

### Example 3: E-Commerce Catalog

```
Collection: Products
├─ Partition key: category
├─ Schema flexibility:
   Electronics:
   {
     "id": "prod-001",
     "name": "Laptop",
     "category": "Electronics",
     "specs": {
       "cpu": "Intel i7",
       "ram": "16GB",
       "storage": "512GB SSD"
     }
   }
   
   Books:
   {
     "id": "prod-002",
     "name": "Azure Guide",
     "category": "Books",
     "author": "John Doe",
     "isbn": "978-0-123456-78-9"
   }

Cosmos DB benefits:
├─ Different product types, same collection
├─ No need for separate databases
├─ Global availability for international customers
└─ Flexible schema accommodates new product types
```

## Best Practices

- Choose partition key carefully (even distribution)
- Use Session Consistency for most applications
- Monitor RU consumption and optimize queries
- Use autoscale for variable workloads
- Enable indexing selectively to reduce RU cost
- Use TTL for automatic data cleanup
- Replicate to multiple regions for high availability
- Design queries to minimize cross-partition queries
- Test performance with realistic data volume

## Summary

Azure Cosmos DB is ideal for applications requiring flexible data models, global distribution, and automatic scaling. Its JSON document model, multi-region support, and performance guarantees make it perfect for modern cloud applications from social media to IoT to e-commerce. Understanding consistency models, partitioning strategies, and throughput management is key to building cost-effective, high-performance Cosmos DB solutions.
