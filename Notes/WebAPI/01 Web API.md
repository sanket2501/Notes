# 01. Web API

Modern business applications are built using various programming platforms, making it challenging for them to communicate with each other. **Web services solve this problem** by providing a common platform that allows different applications to interact, regardless of the programming languages they use.

## Types of web services

### SOAP (Simple Object Access Protocol)

1. A protocol that uses XML to request and exchange information between applications.
2. It’s platform- and language-independent, and often used in enterprise-level services.
3. It operates over HTTP, SMTP, or other protocols.

### REST (Representational State Transfer)

1. A lightweight, more flexible alternative to SOAP.
2. It’s based on stateless, client-server architecture and uses standard HTTP methods (GET, POST, PUT, DELETE).
3. It supports multiple formats like XML, JSON, or even HTML.

### GraphQL

1. A **query language** for APIs and a runtime for executing requests against your data.
2. Unlike REST, GraphQL lets clients request *only the fields they need*, making queries efficient and flexible for complex data shapes.

### gRPC (gRPC Remote Procedure Call)

1. A modern open-source **RPC framework** developed by Google.
2. Uses **Protocol Buffers** (binary serialization) for compact, fast messages.
3. Offers features like **bidirectional streaming**, making it suitable for high-performance scenarios.

## ASP.NET Core Web API

### Cross-Platform

1. ASP.NET Core is designed to run on Windows, Linux, and macOS, making it flexible for deployment on various platforms.

### RESTful APIs

1. You can easily build RESTful APIs using HTTP methods like GET, POST, PUT, DELETE, and PATCH.
2. It's designed to work with JSON by default, but you can also use other formats (e.g., XML, CSV).

### Routing

1. The routing system in ASP.NET Core allows you to map HTTP requests to specific controller actions based on the URL pattern, query parameters, and HTTP method.

### Middleware

1. ASP.NET Core uses a pipeline of middleware to handle HTTP requests and responses. You can add custom middleware to handle things like authentication, logging, or error handling.

### Model Binding

1. You can easily bind data from incoming requests (e.g., JSON, form data, query parameters) to your action method parameters and models.

### Swagger/OpenAPI Integration

1. With libraries like **Swashbuckle**, ASP.NET Core can automatically generate a **Swagger UI** and OpenAPI document, making it easier to test and explore endpoints.

### Authentication & Authorization

1. It supports different authentication mechanisms like JWT (JSON Web Tokens), OAuth, and API keys for secure API access.
2. You can define role-based or policy-based authorization for your API endpoints.

### Error Handling

1. Built-in tools for handling errors gracefully, including HTTP status codes, custom error messages, and global exception handling via middleware.

### Versioning

1. ASP.NET Core provides several strategies for API versioning (URL versioning, query string versioning, header versioning), allowing you to handle breaking changes over time.

### Common Tools & Libraries

1. **Entity Framework Core (EF Core):** For interacting with databases.
2. **AutoMapper:** For mapping between models and DTOs (Data Transfer Objects).
3. **Swashbuckle/Swagger:** To auto-generate API documentation.
4. **Serilog or NLog:** For logging and monitoring API calls.
5. **FluentValidation:** For request validation.
