# 15. Filters

In ASP.NET Web API, filters are powerful tools that allow you to execute logic at different stages of the request-response lifecycle. They can be used to perform common tasks like logging, validation, authentication, authorization, caching, exception handling, etc., across your API actions or controllers.

Filters are applied globally, at the controller level, or at the action level.

## Types of Filters in ASP.NET Web API

1. Authorization Filters
2. Action Filters
3. Exception Filters
4. Result Filters
5. Custom Filters

## 1. Authorization Filters

Authorization filters are executed first and are used for authentication and authorization checks. These filters are commonly used to ensure that the user making the request has the necessary permissions to access the resource.

Example: Applying an authorization check before allowing access to an action.

```csharp
namespace Syltram.Infrastructure.WebApi.Security
{
    public class SyltramAuthorizeAttribute : AuthorizeAttribute
    {
        protected override void HandleUnauthorizedRequest(HttpActionContext actionContext)
        {
            if (!IsAuthenticated(actionContext))
            {
                // 401
                base.HandleUnauthorizedRequest(actionContext);
            }
            else
            {
                // 403
                actionContext.Response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            }
        }

        private bool IsAuthenticated(HttpActionContext actionContext)
        {
            var principal = actionContext.RequestContext.Principal;
            return principal?.Identity != null && principal.Identity.IsAuthenticated;
        }
    }
}

[SyltramAuthorize(Roles = SecureItRoleClaims.ViewNetwork)]
public class CityController : BaseController
{
    public IHttpActionResult Get()
    {
        return Ok("Authorized access to products");
    }
}
```

## 2. Action Filters

Action filters are executed before and after an action method is invoked. You can use action filters to manipulate the request or response, such as logging, modifying headers, or validating model state before the action executes.

Action filters can be used for:

- Pre-action logic (before the action executes) using `OnActionExecuting`
- Post-action logic (after the action executes) using `OnActionExecuted`

```csharp
public class CustomActionFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(HttpActionContext actionContext)
    {
        // Logic before the action is executed
        Debug.WriteLine("Before action execution");
    }

    public override void OnActionExecuted(HttpActionExecutedContext actionExecutedContext)
    {
        // Logic after the action is executed
        Debug.WriteLine("After action execution");
    }
}

[CustomActionFilter]
public class ProductsController : ApiController
{
    public IHttpActionResult Get()
    {
        return Ok("Action executed");
    }
}
```

## 3. Exception Filters

Exception filters handle errors that occur during the execution of an action. They allow you to catch exceptions globally, log them, and return a custom response to the client.

```csharp
public class CustomExceptionFilter : ExceptionFilterAttribute
{
    public override void OnException(HttpActionExecutedContext actionExecutedContext)
    {
        // Log the exception
        var exception = actionExecutedContext.Exception;
        Debug.WriteLine("Exception occurred: " + exception.Message);

        // Return a custom error response
        actionExecutedContext.Response = new HttpResponseMessage(HttpStatusCode.InternalServerError)
        {
            Content = new StringContent("An error occurred during the request.")
        };
    }
}
```

## 4. Result Filters

Result filters are executed after the action has executed and just before the response is sent back to the client. These filters are useful when you want to manipulate the response or apply post-processing logic to the response before it's returned to the client.

```csharp
public class CustomResultFilter : ActionFilterAttribute
{
    public override void OnActionExecuted(HttpActionExecutedContext actionExecutedContext)
    {
        // Modify the response before it's sent back to the client
        if (actionExecutedContext.Response != null)
        {
            actionExecutedContext.Response.Headers.Add("X-Custom-Header", "Custom value");
        }

        base.OnActionExecuted(actionExecutedContext);
    }
}
```

## 5. Custom Filters

Sometimes you need filters that don't fall into one of the built-in categories like authorization, action, exception, or result filters. In this case, you can create custom filters by implementing the `IFilter` interface or by extending one of the built-in filter types.

```csharp
public class LoggingFilter : IActionFilter
{
    public bool AllowMultiple => false;

    public Task<HttpResponseMessage> ExecuteActionFilterAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
    {
        // Log incoming request
        Debug.WriteLine("Request: " + actionContext.Request.RequestUri);

        return actionContext.ActionExecuted(context =>
        {
            // Log outgoing response
            Debug.WriteLine("Response: " + context.Response.StatusCode);
        });
    }
}
```

## Registering Filters Globally

In the `Configure` method of your `Startup.cs` file, register the filter by adding it to the filters collection of `HttpConfiguration`.

```csharp
public class Startup
{
    public void Configuration(IAppBuilder app)
    {
        // Create a new HttpConfiguration instance
        HttpConfiguration config = new HttpConfiguration();

        // Register the custom filter globally
        config.Filters.Add(new CustomActionFilter());

        // Other Web API configurations (e.g., routing)
        config.MapHttpAttributeRoutes();

        // Use Web API with OWIN
        app.UseWebApi(config);
    }
}
```

## Summary of Filter Types

1. Authorization Filter
   - Interface: `IAuthorizationFilter`
   - Method: `OnAuthorization(HttpActionContext actionContext)`
   - Purpose: Used for authentication and authorization logic to ensure the user has permission to access the resource.

2. Action Filter
   - Interface: `IActionFilter` (or derived class `ActionFilterAttribute`)
   - Methods:
     - `OnActionExecuting(HttpActionContext actionContext)` – Called before the action method is executed.
     - `OnActionExecuted(HttpActionExecutedContext actionExecutedContext)` – Called after the action method is executed.
   - Purpose: Used for logic before and after action execution, such as logging or input validation.

3. Exception Filter
   - Interface: `IExceptionFilter`
   - Method: `OnException(HttpActionExecutedContext actionExecutedContext)`
   - Purpose: Used to handle exceptions globally, allowing you to log or return a custom response.

4. Result Filter
   - Interface: `IResultFilter` (or derived class `ActionFilterAttribute`)
   - Methods:
     - `OnResultExecuting(HttpActionContext actionContext)` – Called before the result is executed.
     - `OnResultExecuted(HttpActionExecutedContext actionExecutedContext)` – Called after the result is executed.
   - Purpose: Used to modify or add logic to the response right before it's sent to the client.

5. Custom Filters
   - Interface: Can implement any of the above interfaces or extend filter attributes like `ActionFilterAttribute`, `ExceptionFilterAttribute`, etc.
   - Methods: Varies depending on the filter type.
   - Purpose: Create custom behavior for specific needs in your Web API, like logging, caching, etc.
