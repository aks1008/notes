# C# Identity Management & Security

## Table of Contents

1. [Authentication vs Authorization](#authentication-vs-authorization)
2. [Identity Management](#identity-management)
   - [ASP.NET Core Identity](#aspnet-core-identity)
   - [UserManager](#usermanager)
   - [RoleManager](#rolemanager)
   - [SignInManager](#signinmanager)
3. [Authentication Methods](#authentication-methods)
   - [Cookie-based Authentication](#1-cookie-based-authentication)
   - [Token Based Authentication](#2-token-based-authentication)
   - [OAuth 2.0 / OpenID Connect](#3-oauth-20--openid-connect)
   - [API Key Authentication](#4-api-key-authentication)
   - [Basic Authentication](#5-basic-authentication)
   - [Windows Authentication](#6-windows-authentication)
   - [Certificate-Based Authentication](#7-certificate-based-authentication)
4. [Web API Security Mechanisms](#web-api-security-mechanisms)
5. [Common Security Threats](#common-security-threats)
6. [Security Best Practices](#security-best-practices)
7. [Additional Security Features](#additional-security-features)

---

## Authentication vs Authorization

- **Authentication**: Who are you?
- **Authorization**: What can you do?

---

## Identity Management

### ASP.NET Core Identity

Following are main classes in ASP.NET Core Identity, which provides many methods for performing different operations on the identity database.

#### UserManager

This class in ASP.NET Core Identity is responsible for managing users in the application. It provides functionality for creating, updating, deleting, and retrieving user information from the ASP.NET Core identity database.

#### RoleManager

This class in ASP.NET Core Identity is responsible for managing roles in the application. It provides functionality for creating, updating, deleting, retrieving roles, and assigning roles to users.

#### SignInManager

This class in ASP.NET Core Identity handles sign-in and sign-out operations and provides functionalities for two-factor authentication and external authentication.

---

## Authentication Methods

### 1. Cookie-based Authentication

Stores authentication information in a browser cookie.
Common for traditional web applications (MVC, Razor Pages).
Example: ASP.NET Core Identity uses cookies by default.

```csharp
// if in Program.cs or Startup.cs
builder.Services.AddAuthentication("MyCookieAuth")
    .AddCookie("MyCookieAuth", options =>
    {
        options.LoginPath = "/Account/Login";
        options.LogoutPath = "/Account/Logout";
        options.Cookie.Name = "MyCookieAuth";
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    });

app.UseAuthentication();
app.UseAuthorization();
```

Used mostly for MVC or Razor
For API, use JWT or some bearer token
For cookie-based auth in a second server it works, but if distributed, state may be an issue unless handled (note says cookie auth is stateful)

#### Flow

User Browser -> Login Request -> ASP.NET Core App
Set Cookie (Auth)
Subsequent Request (with Auth Cookie) -> Response
Logout Request -> Remove Cookie

#### Identity & Claims

**Identity**: More representation of the user in the system, basic information such as username, user id, authentication type

**ClaimsIdentity**: Core representation of the user in the system, basic information: username, user id, authentication type

**Claims**: Key-value pairs that describe additional attributes or properties about the user - Roles, email, permissions, department, custom data, etc.

```csharp
[HttpPost("Login")]
public async Task<IActionResult> Login(string username, string password)
{
    // Dummy user validation for demo purposes
    if (username == "admin" && password == "password")
    {
        var claims = new List<Claim>
        {
            new Claim(ClaimTypes.Name, username),
            new Claim(ClaimTypes.Role, "Admin")
        };

        var claimsIdentity = new ClaimsIdentity(claims, "MyCookieAuth");
        var claimsPrincipal = new ClaimsPrincipal(claimsIdentity);

        await HttpContext.SignInAsync("MyCookieAuth", claimsPrincipal); // stored internally/encrypted via data protection
        return RedirectToAction("Dashboard");
    }

    ViewBag.Error = "Invalid credentials";
    return View();
}
```

**Key Concepts**:
- **Claim**: (Name, Role, etc.)
- **ClaimsIdentity**: Holds claims, knows how user was authenticated
- **ClaimsPrincipal**: Holds one or more identities
- **SignInAsync**: Serializes principal, issues auth cookie

### 2. Token Based Authentication

Uses tokens (usually JWT – JSON Web Token) for stateless authentication.
Common for API and SPAs (Single Page Applications).
The token is sent in the Authorization header with each request.
Store in local storage / session storage

#### Flow

Client (login) -> [POST] /api/login -> Server (validates credentials, generates JWT)
Client stores JWT
Client (API call) -> [GET] /api/data (Authorization: Bearer <JWT>) -> Server (validates JWT, returns data)

```csharp
Add package: Microsoft.AspNetCore.Authentication.JwtBearer
```

#### Program.cs Configuration

```csharp
var jwtKey = builder.Configuration["Jwt:Key"];
var jwtIssuer = builder.Configuration["Jwt:Issuer"];

builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = false,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,
        ValidIssuer = jwtIssuer,
        IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(jwtKey))
    };
});

builder.Services.AddAuthorization();

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();
```

#### appsettings.json

```json
"Jwt": {
  "Key": "super_secret_key_123!",
  "Issuer": "your-app"
}
```

#### Login Controller

```csharp
if (model.Username == "admin" && model.Password == "password")
{
    var token = GenerateJwtToken(model.Username);
    return Ok(new { token });
}
return Unauthorized();
```

#### JWT Token Generation

```csharp
private string GenerateJwtToken(string username)
{
    var jwtKey = _config["Jwt:Key"];
    var jwtIssuer = _config["Jwt:Issuer"];

    var claims = new[]
    {
        new Claim(JwtRegisteredClaimNames.Sub, username),
        new Claim(ClaimTypes.Role, "Admin"),
        new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
    };

    var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(jwtKey));
    var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

    var token = new JwtSecurityToken(
        issuer: jwtIssuer,
        audience: null,
        claims: claims,
        expires: DateTime.UtcNow.AddMinutes(30),
        signingCredentials: creds
    );

    return new JwtSecurityTokenHandler().WriteToken(token);
}
```

#### Protected Endpoint

```csharp
[HttpGet]
[Authorize]
public IActionResult TestMethod()
{
    return Ok("This is a Hello world");
}
```

**Important Notes**:
- Validation of token for protected endpoint should work with [Authorize] if set in Program.cs file
- Build configuration of "Jwt:Key" should be stored securely
- Key rotation often, token refresh, token expiration minimum
- SPA often benefits with JWT for token storage, CORS configuration, etc.

### 3. OAuth 2.0 / OpenID Connect

Industry standard protocols for delegated authentication and authorization.
Used for social login (Google, Facebook, Microsoft) and enterprise SSO.
OpenID Connect is an identity layer on top of OAuth 2.0.
Both are widely used for SSO, social login, and secure API access.

#### OAuth 2.0

- **Primarily for authorization** (granting access to resources, not authentication)
- Flow involves obtaining access tokens to access protected resources

#### OpenID Connect

- An **authentication layer** built on top of OAuth 2.0
- Contains two token values:
  - **ID Token**: JWT containing user identity info (authentication)
  - **Access Token**: used for API access (authorization)

#### Flow

1. User tries to log in to your app
2. App redirects user to the identity provider (e.g., Google, Azure AD)
3. User authenticates at provider
4. Identity provider redirects to your app with an authorization code
5. App exchanges the code for tokens (ID token, access token)
6. App uses the ID token to authenticate the user and access token for APIs

```
User -> [GET /test-data] -> App ([Authorize] check)
Not authenticated? -> Redirect to Identity Provider (e.g., Microsoft)

User logs in at provider

Provider redirects to /signin-oidc (callback) with code

App exchanges code for tokens, signs in user

App redirects user back to /test-data

User is now authenticated, response returns data
```

The middleware detects the user is not authenticated, generates a redirect URL to the identity provider (e.g., Microsoft, Google), and includes the original URL (e.g., /secure-data) as a return URL or encodes it in the state parameter.

#### Authenticate users via Microsoft (Azure AD)

**1. Register your application**

- Register your app in Azure Portal or the provider of your choice
- Note the Client ID and Client Secret
- Set the redirect URI to `https://localhost:5001/signin-oidc` (for local development)

**2. Install NuGet packages**

```powershell
dotnet add package Microsoft.AspNetCore.Authentication.OpenIdConnect
dotnet add package Microsoft.Identity.Web
dotnet add package Microsoft.AspNetCore.Authentication.Cookies
```

**3. Program.cs configuration**

```csharp
builder.Services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
})
.AddCookie()
.AddOpenIdConnect(options =>
{
    options.Authority = "https://login.microsoftonline.com/common/v2.0/";
    options.ClientId = builder.Configuration["AzureAd:ClientId"];
    options.ClientSecret = builder.Configuration["AzureAd:ClientSecret"];
    options.ResponseType = "code";
    options.SaveTokens = true;
    options.Scope.Add("openid");
    options.Scope.Add("profile");
    options.Scope.Add("email");
    options.CallbackPath = "/signin-oidc";
});
```

**4. Appsettings.json**

```json
"AzureAd": {
  "ClientId": "your-client-id",
  "ClientSecret": "your-client-secret"
}
```

**5. LoginController.cs**

```csharp
[Authorize]
public IActionResult TestCall()
{
    return View();
}

public IActionResult Login()
{
    return Challenge(new AuthenticationProperties { RedirectUri = "/" },
        OpenIdConnectDefaults.AuthenticationScheme);
}

public IActionResult Logout()
{
    return SignOut(new AuthenticationProperties { RedirectUri = "/" },
        OpenIdConnectDefaults.AuthenticationScheme,
        CookieAuthenticationDefaults.AuthenticationScheme);
}
```

**Callback URL**

- It tells the identity provider where to send the user after login
- The authentication middleware listens on this path to complete the authentication process
- If the callback path does not match, authentication will fail

### 4. API Key Authentication

Simple key (string) is sent with each request (usually in a header or query string).
Common for service-to-service or third-party API access.

API key authentication is a simple authentication method used to secure APIs. It involves issuing a unique key (a string) to each client, which must be included in API requests. The server validates the key to grant or deny access.

It's simple key validation authentication, not very secure for production scenarios without additional protections.

### 5. Basic Authentication

Sends a username and password with each HTTP request (basic header).
Not recommended for production unless used over HTTPS and with additional security.

Format: `Authorization: Basic base64(username:password)`

### 6. Windows Authentication

Uses the Windows user account of the client.
Common in intranet and enterprise environments.
Supported by IIS and Kestrel.

Windows Authentication is an authentication method that uses the credentials of a Windows user account to authenticate users. It is commonly used in intranet, enterprise, and internal web applications where users are part of an Active Directory (AD) domain.

```csharp
builder.Services.AddAuthentication(
    Microsoft.AspNetCore.Server.IISIntegration.IISDefaults.AuthenticationScheme);

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();
```

### 7. Certificate-Based Authentication

Uses client certificates to authenticate users or devices.
Common in high-security or service-to-service scenarios.

Certificate-Based Authentication is a security mechanism where clients (users, services) or devices authenticate themselves to a server using digital certificates instead of (or in addition to) usernames/passwords. This is often called mutual TLS (mTLS) where both client and server present certificates.

#### Use cases

- Service-to-service / API-to-API communication
- High-security environments (banking, healthcare, etc.)
- Scenarios where passwordless, strong authentication is required

#### Flow

```
Client (App) -> HTTPS Request -> Server (API)
Client presents certificate during TLS handshake
Server validates client certificate:
  - Issuer (CA)
  - Expiry/Revocation
  - Subject/Thumbprint
TLS connection established
Client sends API request over secure channel
Server authorizes and processes request
API response
```

#### Implementation

```csharp
builder.Services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
.AddCertificate(options =>
{
    options.AllowedCertificateTypes = CertificateTypes.All;
    options.ValidateCertificateUse = true;
    options.Events = new CertificateAuthenticationEvents
    {
        OnCertificateValidated = context =>
        {
            // custom validation logic (optional)
            var claims = new[]
            {
                new Claim(ClaimTypes.Name, context.ClientCertificate.Subject,
                    ClaimValueTypes.String, context.Options.ClaimsIssuer)
            };

            context.Principal = new ClaimsPrincipal(
                new ClaimsIdentity(claims, context.Scheme.Name));
            context.Success();
            return Task.CompletedTask;
        }
    };
});
```

#### Kestrel Configuration

```csharp
builder.WebHost.ConfigureKestrel(options =>
{
    options.ConfigureHttpsDefaults(httpsOptions =>
    {
        httpsOptions.ClientCertificateMode =
            ClientCertificateMode.RequireCertificate;
    });
});
```

---

## Web API Security Mechanisms

- **JWT Tokens**: Stateless authentication
- **OAuth 2.0**: Authorization framework
- **API Keys**: Simple authentication

---

## Common Security Threats

- **SQL Injection**: Parameterized queries, ORMs
- **XSS (Cross-Site Scripting)**: Input validation, output encoding
- **CSRF (Cross-Site Request Forgery)**: Anti-forgery tokens

---

## Security Best Practices

- **Principle of Least Privilege**: Minimum necessary permissions
- **Input Validation**: Validate all user input
- **Secure Communication**: HTTPS, secure headers

---

## Additional Security Features

- **Anti-Forgery Tokens**: `@Html.AntiForgeryToken()`
- **CORS**: Cross-Origin Resource Sharing
- **SQL Injection Prevention**: Parameterized queries
- **Authentication & Authorization**: JWT tokens, OAuth
