# 06. Azure App Service

Tags: azure, cloud, web

Azure App Service is a fully managed platform for hosting web applications, mobile backends, and RESTful APIs. It abstracts away infrastructure management, allowing developers to focus on application code while Azure handles scaling, security, and updates.

## Table of Contents

- [What is App Service](#what-is-app-service)
- [App Service plans](#app-service-plans)
- [Supported runtime stacks](#supported-runtime-stacks)
- [Deployment options](#deployment-options)
- [Authentication and authorization](#authentication-and-authorization)
- [Networking](#networking)
- [Monitoring and diagnostics](#monitoring-and-diagnostics)
- [Real-world examples](#real-world-examples)

## What is App Service

**Definition**: A fully managed platform-as-a-service (PaaS) for hosting web applications, mobile backends, and RESTful APIs.

**Service Model**: PaaS (Azure manages OS, runtime, middleware)

**Key Benefits**:
- No server management needed
- Automatic scaling
- Built-in CI/CD
- Custom domain support
- SSL/TLS support
- Security features
- Global distribution

## App Service Plans

An App Service plan defines the compute resources for your applications.

### Free and Shared Plans

```
Best For: Development, testing, low-traffic sites

Characteristics:
├─ Shared hardware with other customers
├─ Limited CPU and memory
├─ No auto-scaling
├─ Daily storage limit
└─ Free: Actually free, Shared: $10-20/month

Limitations:
├─ Not suitable for production
├─ No SLA guaranteed
├─ No custom domains (free tier)
└─ Limited scaling capability
```

### Basic Plan

```
Best For: Small production apps, internal applications

Characteristics:
├─ Dedicated compute instances
├─ Auto-scaling available
├─ Multiple instances
├─ Custom domains
├─ Basic monitoring
└─ Price: ~$55-80/month per instance

Scaling:
├─ Manual or automatic
├─ Up to 3 instances
└─ Suitable for small workloads
```

### Standard Plan

```
Best For: Production web applications, APIs

Characteristics:
├─ Dedicated high-performance instances
├─ Auto-scaling up to 10 instances
├─ Stage/Production slots
├─ Custom domains
├─ SSL support
├─ Advanced monitoring
└─ Price: ~$90-130/month per instance

Features:
├─ Deployment slots (staging/production)
├─ Traffic routing
├─ WebJobs
└─ Higher resource quotas
```

### Premium and Isolated Plans

```
Best For: Enterprise applications, high-traffic sites

Characteristics:
├─ Maximum performance
├─ Auto-scaling up to 30+ instances
├─ VNet integration
├─ Private endpoints
├─ Advanced security
└─ Price: ~$280+/month per instance

Enterprise features:
├─ Network isolation
├─ DDoS protection
├─ Custom security
└─ SLA: 99.95%
```

## Supported Runtime Stacks

### .NET Runtime

```csharp
// ASP.NET Core application
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpGet("{id}")]
    public async Task<Product> Get(int id)
    {
        return await _service.GetProductAsync(id);
    }
}

// Supported versions: .NET 6, 7, 8
// Deploy: Visual Studio, GitHub Actions, or Azure CLI
```

### Node.js Runtime

```javascript
// Express.js application
const express = require('express');
const app = express();

app.get('/api/products/:id', async (req, res) => {
    const product = await getProduct(req.params.id);
    res.json(product);
});

app.listen(process.env.PORT || 8080);

// Supported versions: Node 14, 16, 18, 20
```

### Python Runtime

```python
# Flask application
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/api/products/<int:id>')
def get_product(id):
    product = get_product_by_id(id)
    return jsonify(product)

# Supported versions: Python 3.8, 3.9, 3.10, 3.11
```

### Java Runtime

```java
// Spring Boot application
@RestController
@RequestMapping("/api/products")
public class ProductController {
    @GetMapping("/{id}")
    public Product getProduct(@PathVariable int id) {
        return productService.getProduct(id);
    }
}

// Supported: Java 8, 11, 17, 21
```

## Deployment Options

### Git Integration

```bash
# 1. Configure Git deployment
git remote add azure https://...

# 2. Push code
git push azure main

# 3. App Service automatically:
#    - Builds application
#    - Runs tests
#    - Deploys to production
```

### GitHub Actions

```yaml
name: Deploy to App Service

on:
  push:
    branches: [ main ]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build
        run: |
          dotnet build
          dotnet publish -c Release
      - name: Deploy
        uses: azure/webapps-deploy@v2
        with:
          app-name: myapp
```

### Azure Container Registry

```bash
# Push Docker image
docker build -t myapp:latest .
docker push myacr.azurecr.io/myapp:latest

# Deploy from registry
az webapp create --image myacr.azurecr.io/myapp:latest
```

### Visual Studio Publishing

```
Visual Studio:
├─ Right-click project
├─ Select "Publish"
├─ Choose "Azure App Service"
├─ Select subscription/resource group
├─ Click "Publish"
└─ Application deployed

Result: One-click deployment from IDE
```

## Authentication and Authorization

### Anonymous Access

```
Default: Not allowed
Result: 401 Unauthorized if not authenticated

Configuration:
├─ Accessible to public (no login required)
└─ Useful for public APIs
```

### Azure AD Authentication

```csharp
// Require Azure AD login
services.AddAuthentication(AzureADDefaults.BearerScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

[Authorize]
[HttpGet]
public IActionResult Get()
{
    var user = User.Identity.Name;
    return Ok($"Hello {user}");
}

// Result: User must login with Azure AD account
```

### Application Secrets

```csharp
// Store sensitive data in Key Vault
var credential = new DefaultAzureCredential();
var client = new SecretClient(vaultUri, credential);

var secret = await client.GetSecretAsync("db-password");
var password = secret.Value.Value;

// Result: Secrets not stored in code/configuration
```

## Networking

### Custom Domain

```
App Service URL: myapp.azurewebsites.net
└─ Works out of box

Custom domain: www.myapp.com
├─ Register domain
├─ Create DNS record (CNAME)
├─ Bind to App Service
└─ SSL certificate (managed)

Result: Professional domain name
```

### VNet Integration

```
App Service in VNet:
└─ Private connectivity to:
   ├─ SQL Database
   ├─ Storage accounts
   ├─ On-premises networks (via VPN)
   └─ Other resources

Benefits:
├─ Network isolation
├─ Private IP addresses
├─ No public exposure (if needed)
└─ Compliance requirements met
```

### SSL/TLS

```
HTTPS enabled:
├─ Automatically for azurewebsites.net
├─ Managed certificates (free)
├─ Auto-renewal
└─ Custom domain: Upload certificate or use managed

Result: Secure communication (encrypted)
```

## Monitoring and Diagnostics

### Application Insights

```csharp
// Instrument application
var telemetry = new TelemetryClient();

public async Task<Product> GetProductAsync(int id)
{
    var startTime = DateTime.Now;
    try
    {
        var product = await _service.GetAsync(id);
        telemetry.TrackPageView("GetProduct");
        return product;
    }
    catch (Exception ex)
    {
        telemetry.TrackException(ex);
        throw;
    }
}

// Monitor:
├─ Request rate
├─ Response time
├─ Exception frequency
└─ Custom events
```

### Logs

```
Application logs:
├─ Application logging (file system or blob storage)
├─ Web server logs (IIS logs)
├─ Detailed error logs
└─ Failed request trace logs

Access logs:
├─ Azure Portal (Live Metrics)
├─ Log Analytics queries
└─ Application Insights
```

### Alerts

```
Example alerts:
├─ CPU > 80%
├─ Response time > 2 seconds
├─ Error rate > 5%
└─ Memory usage > 90%

Actions:
├─ Email notification
├─ SMS alert
├─ Scale up (auto-scaling)
└─ Webhook notification
```

## Real-World Examples

### Example 1: ASP.NET Core REST API

```csharp
// API for e-commerce platform
[ApiController]
[Route("api/[controller]")]
public class OrdersController : ControllerBase
{
    [HttpPost]
    [Authorize]
    public async Task<IActionResult> CreateOrder(CreateOrderRequest request)
    {
        var order = await _orderService.CreateAsync(request);
        return CreatedAtAction(nameof(GetOrder), new { id = order.Id }, order);
    }

    [HttpGet("{id}")]
    [Authorize]
    public async Task<IActionResult> GetOrder(int id)
    {
        var order = await _orderService.GetAsync(id);
        return Ok(order);
    }
}

// Deployment:
// 1. Push to GitHub
// 2. GitHub Actions builds and tests
// 3. Deploys to App Service
// 4. Application live immediately

// Monitoring:
// - Application Insights tracks requests
// - Performance alerts configured
// - Auto-scaling based on load
```

### Example 2: Node.js Web Application

```javascript
// Express.js web application
const express = require('express');
const app = express();

// Middleware
app.use(express.static('public'));
app.set('views', 'views');
app.set('view engine', 'ejs');

// Routes
app.get('/', (req, res) => {
    res.render('index', { title: 'Home' });
});

app.get('/api/products', async (req, res) => {
    const products = await getProducts();
    res.json(products);
});

// Start server
const port = process.env.PORT || 3000;
app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});

// Deployment: Azure CLI
// az webapp up --name myapp --resource-group mygroup --runtime "node|18"

// Auto-scaling: 
// - Minimum 1 instance
// - Scale to 5 instances if CPU > 70%
// - Scale down if CPU < 30%
```

### Example 3: Multi-Stage Deployment

```
Deployment Slots:
├─ Production slot (live: www.myapp.com)
└─ Staging slot (testing: myapp-staging.azurewebsites.net)

Workflow:
1. Deploy new version to Staging slot
2. Run smoke tests
3. Warm up instances
4. Swap Staging ↔ Production
5. Old version automatically rolled back if needed

Result:
├─ Zero-downtime deployment
├─ Easy rollback capability
└─ Production always stable
```

## Best Practices

- Use managed identity for security
- Enable Application Insights for monitoring
- Configure auto-scaling based on metrics
- Use deployment slots for safe deployments
- Store secrets in Key Vault
- Enable logging and diagnostics
- Use SSL/TLS certificates
- Monitor performance metrics
- Set up alerts for critical issues
- Scale horizontally (multiple instances) not just vertically

## Summary

Azure App Service abstracts away infrastructure management, allowing developers to focus on building applications. Whether hosting traditional web apps, APIs, or containerized applications, App Service provides a secure, scalable, and reliable platform with built-in CI/CD, monitoring, and security features essential for production applications.
