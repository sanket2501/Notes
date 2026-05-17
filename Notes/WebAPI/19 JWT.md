# 19. JWT

JWT (JSON Web Token) is a compact token format used for securely transmitting information between parties. It is commonly used for authentication and authorization.

## Structure of JWT

1. Header – Contains token type and signing algorithm.
2. Payload – Contains claims (like user ID, roles, etc.).
3. Signature – Ensures token integrity.

## Packages Used

| Package | Purpose |
| --- | --- |
| `Microsoft.AspNetCore.Authentication.JwtBearer` | Adds middleware to handle JWT authentication on the server. Validates tokens automatically for each request. |
| `System.IdentityModel.Tokens.Jwt` | Provides classes for creating, reading, and validating JWTs. |
| `Microsoft.IdentityModel.Tokens` | Provides cryptographic classes and token validation parameters. |

## JWT Creation Flow

1. When a user successfully logs in, the server creates a list of claims.
2. Claims can include:
   - `JwtRegisteredClaimNames.Sub` → User ID
   - `JwtRegisteredClaimNames.Email` → User email
   - `ClaimTypes.Role` → Roles assigned to the user
3. The token is signed using a symmetric key (`HmacSha256`).
4. A `JwtSecurityTokenHandler` converts the token to a string, ready to send to the client.

### Example

```csharp
public string GenerateToken(User user)
{
    var claims = new[]
    {
        new Claim(JwtRegisteredClaimNames.Sub, user.Id.ToString()),
        new Claim(JwtRegisteredClaimNames.Email, user.Email),
        new Claim(ClaimTypes.Role, string.Join(",", user.Roles))
    };

    var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("yourSecretKey"));
    var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

    var token = new JwtSecurityToken(
        issuer: "yourIssuer",
        audience: "yourAudience",
        claims: claims,
        expires: DateTime.Now.AddMinutes(30),
        signingCredentials: creds);

    return new JwtSecurityTokenHandler().WriteToken(token);
}
```

## Token Storage (Client-Side)

Purpose: Store JWT safely on the client and retrieve it for subsequent requests.

Flow:

1. Client receives JWT from the server.
2. Stores JWT either:
   - Cookies (`HttpOnly`, `Secure`) → prevents JS access
   - Local storage (if cookies aren’t used)

```csharp
public void SetToken(string token)
{
    var cookieOptions = new CookieOptions { HttpOnly = true, Secure = true };
    _httpContextAccessor.HttpContext.Response.Cookies.Append("AccessToken", token, cookieOptions);
}

public string GetToken()
{
    _httpContextAccessor.HttpContext.Request.Cookies.TryGetValue("AccessToken", out var token);
    return token;
}
```

## Attaching Token to Requests

Purpose: Automatically attach JWT to the `Authorization` header for all outgoing HTTP requests.

Flow:

1. Before sending any HTTP request, `SendAsync` reads JWT from `ITokenHelper`.
2. Adds header: `Authorization: Bearer <token>`.
3. Requests are sent to the server with token for authentication.

```csharp
protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
{
    string accessToken = _tokenHelper.GetToken();
    if (!string.IsNullOrEmpty(accessToken))
    {
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    }
    return await base.SendAsync(request, cancellationToken);
}
```

## Token Validation (Server-Side)

Location: `Program.cs` or `Startup.cs`

Purpose: Validate incoming JWTs for authentication and authorization.

Flow:

1. Add `AddJwtBearer` middleware to pipeline.
2. Configure `TokenValidationParameters`:
   - `ValidateIssuer` → checks issuer of token
   - `ValidateAudience` → checks audience
   - `ValidateLifetime` → checks expiry
   - `ValidateIssuerSigningKey` → verifies signature
3. Server automatically rejects invalid/expired tokens.

```csharp
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = "yourIssuer",
            ValidAudience = "yourAudience",
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("yourSecretKey"))
        };
    });
```

## Complete Flow Summary

| Step | Description |
| --- | --- |
| 1 | User logs in → sends credentials to API. |
| 2 | API validates credentials → generates JWT via TokenServices. |
| 3 | JWT returned to client → stored in cookies/local storage via ITokenHelper. |
| 4 | Client makes API requests → TokenDelegatingHandler adds JWT to Authorization header. |
| 5 | Server validates JWT → middleware checks signature, expiry, issuer, audience. |
| 6 | Access granted or denied based on token validity and claims (e.g., roles). |
