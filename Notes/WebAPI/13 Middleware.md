# 13. Middleware

Middleware is a piece of code that sits between the request and the response. It can perform tasks like:

- Handling HTTP requests before they reach your API logic.
- Modifying the request or response (e.g., logging, authentication).
- Performing checks or altering how requests should be processed.

Middleware components are executed in the order they are added to the pipeline.

## How Middleware Works

When a request comes to the server, the middleware pipeline is executed from top to bottom, one middleware component after the other. Each component in the pipeline can either:

1. Process the request and pass it to the next middleware.
2. Return a response directly and stop the request from being passed further down the pipeline.

## Adding Middleware to the Pipeline

In ASP.NET Core 6+ (minimal hosting model), middleware is typically configured in `Program.cs`.

## Custom Middleware

A custom middleware is a class that has an `Invoke` or `InvokeAsync` method, which takes a `HttpContext` as a parameter and returns a `Task`.

```csharp
using Microsoft.AspNetCore.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public class RequestLoggingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<RequestLoggingMiddleware> _logger;

    public RequestLoggingMiddleware(RequestDelegate next, ILogger<RequestLoggingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Log the incoming request
        _logger.LogInformation("Request Path: {Path}", context.Request.Path);

        // Call the next middleware in the pipeline
        await _next(context);

        // Log the outgoing response
        _logger.LogInformation("Response Status Code: {StatusCode}", context.Response.StatusCode);
    }
}
```

- Logs the request path when an HTTP request is received.
- Calls the next middleware in the pipeline (`await _next(context)`).
- Logs the HTTP status code when the response is sent.

## Exception Handling Middleware Example

```csharp
public class ExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<ExceptionHandlingMiddleware> _logger;

    public ExceptionHandlingMiddleware(RequestDelegate next, ILogger<ExceptionHandlingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context); // Proceed with the request pipeline
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred.");
            await HandleExceptionAsync(context, ex);
        }
    }

    private Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        context.Response.ContentType = "application/json";
        context.Response.StatusCode = StatusCodes.Status500InternalServerError;

        var result = JsonSerializer.Serialize(new { message = "An unexpected error occurred." });
        return context.Response.WriteAsync(result);
    }
}
```

## Registering Custom Middleware

In `Program.cs`, register this custom middleware like this:

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();

var app = builder.Build();

// Add custom logging middleware
app.UseMiddleware<RequestLoggingMiddleware>();

app.MapControllers();

app.Run();
```

## Built-in Middleware in ASP.NET Core

1. Error Handling Middleware (`UseExceptionHandler`)

For global error handling, use the `UseExceptionHandler` middleware.

```csharp
app.UseExceptionHandler("/Home/Error");
```

This catches exceptions thrown anywhere in the pipeline and redirects the user to an error handling page.

2. CORS Middleware (`UseCors`)

For enabling Cross-Origin Resource Sharing (CORS), configure and enable CORS middleware.

```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAll",
        builder => builder.AllowAnyOrigin().AllowAnyMethod().AllowAnyHeader());
});

app.UseCors("AllowAll");
```

This middleware allows you to control which domains can access your Web API.

3. Authentication Middleware (`UseAuthentication` and `UseAuthorization`)

For protecting your API endpoints, use authentication and authorization middleware. Configure these in `Program.cs` or `Startup.cs`.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

These two middlewares are crucial for securing your Web API endpoints.

4. Static File Middleware (`UseStaticFiles`)

If you want to serve static files like HTML, CSS, or JavaScript, use the `UseStaticFiles` middleware.

```csharp
app.UseStaticFiles(); // Serve static files from wwwroot folder
```

5. Routing Middleware (`UseRouting` and `UseEndpoints`)

To handle incoming HTTP requests and route them to the appropriate controller, use `UseRouting` and `UseEndpoints`.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
});
```

## Order of Middleware Execution

The order in which middleware is added to the pipeline is very important because it determines the sequence of execution. For example:

- Logging Middleware should typically be added first to capture all requests.
- Authentication Middleware should be added before Authorization Middleware.
- Exception Handling Middleware should be added before any other middleware to catch any unhandled exceptions.

```csharp
var app = builder.Build();

// Error handling middleware (should be first for global error handling)
app.UseExceptionHandler("/Home/Error");

// Logging Middleware
app.UseMiddleware<RequestLoggingMiddleware>();

// CORS middleware
app.UseCors("AllowAll");

// Authentication and Authorization middleware
app.UseAuthentication();
app.UseAuthorization();

// Routing and endpoint mapping
app.UseRouting();
app.MapControllers();

app.Run();
```

## Common Scenarios for Middleware Usage

- Authentication and Authorization: Adding middleware to check for valid tokens (e.g., JWT) and enforcing role-based access.
- Logging: Logging incoming requests, responses, and errors.
- Error Handling: Handling unhandled exceptions globally and returning consistent error responses.
- CORS: Enabling cross-origin requests from specific domains or enabling global cross-origin sharing.
- Compression: Enabling response compression to reduce the size of the response (e.g., using GZIP).
- Performance Monitoring: Adding middleware to monitor the time taken by requests.

## List of Built-In Middleware in ASP.NET Core

1. `UseRouting()`
   - Purpose: Defines the routing middleware that is responsible for matching incoming requests to the correct endpoint (controller/action).
   - Usage:

```csharp
app.UseRouting();
```

2. `UseEndpoints()`
   - Purpose: Maps routes (controllers, Razor Pages, etc.) to the request pipeline. This is typically used with `UseRouting()`.
   - Usage:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();  // Maps controllers to routes
});
```

3. `UseAuthentication()`
   - Purpose: Adds authentication middleware to the request pipeline. It validates the user’s identity.
   - Usage:

```csharp
app.UseAuthentication();
```

4. `UseAuthorization()`
   - Purpose: Adds authorization middleware to enforce access control based on the authenticated user’s claims/roles.
   - Usage:

```csharp
app.UseAuthorization();
```

5. `UseStaticFiles()`
   - Purpose: Serves static files (e.g., HTML, CSS, JS, images) from the `wwwroot` folder or other directories configured.
   - Usage:

```csharp
app.UseStaticFiles();
```

6. `UseCors()`
   - Purpose: Enables Cross-Origin Resource Sharing (CORS) by specifying which domains can access your API.
   - Usage:

```csharp
app.UseCors("AllowAll");  // Use a predefined CORS policy
```

7. `UseExceptionHandler()`
   - Purpose: Catches unhandled exceptions globally and redirects to an error page or API endpoint.
   - Usage:

```csharp
app.UseExceptionHandler("/Home/Error");
```

8. `UseDeveloperExceptionPage()`
   - Purpose: Displays detailed error information in the browser, only in the development environment.
   - Usage:

```csharp
app.UseDeveloperExceptionPage();
```

9. `UseHttpsRedirection()`
   - Purpose: Redirects HTTP requests to HTTPS.
   - Usage:

```csharp
app.UseHttpsRedirection();
```

10. `UseResponseCaching()`
    - Purpose: Enables HTTP response caching, allowing responses to be cached for faster retrieval.
    - Usage:

```csharp
app.UseResponseCaching();
```

11. `UseSession()`
    - Purpose: Adds session middleware to manage session state across requests.
    - Usage:

```csharp
app.UseSession();
```

12. `UseHealthChecks()`
    - Purpose: Enables health check endpoints to monitor the health of your application.
    - Usage:

```csharp
app.UseHealthChecks("/health");
```

13. `UseLogging()`
    - Purpose: Provides built-in logging capabilities to log requests and responses.
    - Usage:

```csharp
app.UseLogging();
```

14. `UseCookiePolicy()`
    - Purpose: Configures how cookies are handled and adds cookie consent functionality.
    - Usage:

```csharp
app.UseCookiePolicy();
```

15. `UseRequestLocalization()`
    - Purpose: Configures request localization for handling culture-specific content (for example, handling different languages based on the request).
    - Usage:

```csharp
app.UseRequestLocalization();
```

16. `UseResponseCompression()`
    - Purpose: Compresses response content to save bandwidth and improve performance.
    - Usage:

```csharp
app.UseResponseCompression();
```

17. `UseSpa()`
    - Purpose: Used when building Single-Page Applications (SPA), to configure client-side routing and static file serving.
    - Usage:

```csharp
app.UseSpa(spa =>
{
    spa.Options.SourcePath = "ClientApp";
    if (env.IsDevelopment())
    {
        spa.UseReactDevelopmentServer(npmScript: "start");
    }
});
```

18. `UseMiddleware<CustomMiddleware>()`
    - Purpose: A placeholder for custom middleware created by the developer. Custom middleware can perform a variety of tasks like logging, auditing, custom headers, etc.
    - Usage:

```csharp
app.UseMiddleware<CustomMiddleware>();
```

19. `UsePathBase()`
    - Purpose: Adds a base path (prefix) for all URLs in the app. Useful when deploying the app under a specific path.
    - Usage:

```csharp
app.UsePathBase("/myapp");
```
