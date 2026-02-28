# C# Identity Management & Security

## Table of Contents

1. [Authentication vs Authorization](#authentication-vs-authorization)
2. [Authentication: "Who are you?"](#1-authentication-who-are-you)
   - [Setting up JWT Authentication](#setting-up-jwt-authentication)
3. [Authorization: "What can you do?"](#2-authorization-what-can-you-do)
   - [Role-Based (Simple)](#a-role-based-simple)
   - [Claims-Based (Granular)](#b-claims-based-granular)
   - [Policy-Based (Advanced)](#c-policy-based-advanced)
4. [The Middleware Pipeline (Critical)](#3-the-middleware-pipeline-critical)
5. [Transport Layer Security (HTTPS)](#1-transport-layer-security-https)
   - [How the "Automatic" Part Works](#1-how-the-automatic-part-works)
   - [Your Responsibility as a Developer](#2-your-responsibility-as-a-developer)
   - [The One Trap: "Mixed Content"](#3-the-one-trap-mixed-content)
6. [SSL/TLS Certificates](#ssl-tls-certificates)
   - [In Development (Localhost)](#1-in-development-localhost)
   - [In Production (Manual Setup)](#2-in-production-manual-setup)
   - [In the Cloud (Azure / AWS / Docker)](#3-in-the-cloud-azure--aws--docker)
7. [Identity Management](#identity-management)
   - [ASP.NET Core Identity](#aspnet-core-identity)
   - [UserManager](#usermanager)
   - [RoleManager](#rolemanager)
   - [SignInManager](#signinmanager)
8. [Authentication Methods](#authentication-methods)
   - [Comparison Summary](#comparison-summary)
   - [Which one should you choose?](#which-one-should-you-choose)
   - [Cookie-based Authentication (The Web App Standard)](#1-cookie-based-authentication-the-web-app-standard)
   - [Token Based Authentication (The API Standard)](#2-token-based-authentication-the-api-standard)
   - [OAuth 2.0 / OpenID Connect](#3-oauth-20--openid-connect)
   - [API Key Authentication (Server-to-Server)](#4-api-key-authentication-server-to-server)
   - [Basic Authentication](#5-basic-authentication)
   - [Windows Authentication](#6-windows-authentication)
   - [Certificate-Based Authentication](#7-certificate-based-authentication)
   - [ASP.NET Core Identity (The "All-in-One")](#8-aspnet-core-identity-the-all-in-one)
9. [Web API Security Mechanisms](#web-api-security-mechanisms)
10. [Common Security Threats](#common-security-threats)
11. [Security Best Practices](#security-best-practices)
12. [Additional Security Features](#additional-security-features)

---

## Authentication vs Authorization

- **Authentication**: Who are you?
- **Authorization**: What can you do?

In .NET Core Web API, security is built on two distinct pillars: **Authentication** (identifying the user) and **Authorization** (deciding what they can do).

In modern APIs, this is typically handled via **JWT (JSON Web Tokens)** because they are stateless, meaning the server doesn't need to remember the user's session—the token itself carries all the necessary proof.

---

## 1. Authentication: "Who are you?"

Authentication is the process of verifying a user's identity. In a Web API, the most common flow is:

1. **Login**: User sends credentials to an identity endpoint.
2. **Token Issuance**: The server validates credentials and returns a signed **JWT**.
3. **Subsequent Requests**: The client includes this token in the `Authorization` header as a `Bearer` token.

### Setting up JWT Authentication

In your `Program.cs`, you register the authentication service:

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
            ValidIssuer = "YourIssuer",
            ValidAudience = "YourAudience",
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("YourSecretKey"))
        };
    });

```

---

## 2. Authorization: "What can you do?"

Once identity is confirmed, authorization checks if that identity has permission to access a specific resource. .NET Core offers three main levels of complexity:

### A. Role-Based (Simple)

Best for basic apps with fixed roles like "Admin" or "User".

```csharp
[Authorize(Roles = "Admin")]
[HttpGet("secure-data")]
public IActionResult GetAdminData() => Ok("Welcome, Admin.");

```

### B. Claims-Based (Granular)

Uses specific "claims" (key-value pairs) inside the token, such as `EmployeeId` or `ProjectAccess`.

```csharp
[Authorize(Policy = "EmployeeOnly")]

```

### C. Policy-Based (Advanced)

Allows you to group multiple requirements into a single "Policy". This is the gold standard for enterprise apps.

**Example Policy Registration:**

```csharp
builder.Services.AddAuthorization(options =>
{
    options.AddPolicy("AtLeast21", policy =>
        policy.RequireClaim("Age", "21", "22", "23"));
});

```

---

## 3. The Middleware Pipeline (Critical)

The order in which you register middleware matters. If you swap these, your authorization will fail because it won't know who the user is yet.

```csharp
var app = builder.Build();

app.UseAuthentication(); // 1. Identify the user
app.UseAuthorization();  // 2. Check their permissions

app.MapControllers();
app.Run();

```

---

## Comparison Table

| Feature | Authentication | Authorization |
| --- | --- | --- |
| **Question** | Who are you? | What can you do? |
| **Status Codes** | `401 Unauthorized` (Token missing/invalid) | `403 Forbidden` (User known, but no access) |
| **Method** | JWT, Cookies, OAuth | Roles, Policies, Claims |
| **Execution** | Runs first | Runs second |

---

You are absolutely right to be concerned. Sending raw credentials over the internet is like sending a postcard with your bank PIN written on the back—anyone handling that "mail" can read it.

However, in professional development, we don't send credentials "naked." We use several layers of security to make that risk virtually zero.

---

## 1. Transport Layer Security (HTTPS)

This is your primary defense. When you use **HTTPS** (TLS), a secure "tunnel" is created between the client and the server before any data is sent.

* **Encryption:** The username and password are encrypted *before* they leave the user's device.
* **Decryption:** Only your server has the private key to unlock that data.
* **The Result:** If a hacker intercepts the packet (a "Man-in-the-Middle" attack), they only see scrambled gibberish, not your password.

---

## 2. Never Store Plaintext Passwords

Even if the transmission is secure, the server must be secure too. You should **never** store a password as plain text in your database.

Instead, use a **One-Way Hash** with a **Salt**. Even if your database is hacked, the attacker only sees a unique string of random characters that cannot be "reversed" into the original password.

> **Tip:** In .NET, use `BCrypt.Net` or the built-in `PasswordHasher<TUser>` from ASP.NET Core Identity.

---

## 3. Best Practices to Reduce Risk

If you want to be truly "battle-hardened," follow these industry standards:

* **POST Requests only:** Never send credentials in a `GET` request. GET parameters appear in URL query strings, which get saved in browser history and server logs.
* **Request Rate Limiting:** Prevent "Brute Force" attacks (where a bot tries 1,000 passwords a second) by limiting how many login attempts can come from one IP address.
* **CORS (Cross-Origin Resource Sharing):** Configure your API so that it only accepts login requests from your specific trusted website or mobile app.
* **Multi-Factor Authentication (MFA):** Even if a password is stolen, the attacker can’t get in without a second code from the user's phone.

---

## 4. Modern Alternatives

If you still feel uneasy about handling passwords yourself, you can use **OpenID Connect / OAuth 2.0**.

Instead of your API seeing the password, the user logs in via a trusted provider like **Google, Microsoft, or Auth0**. They handle the "risky" part and simply send your API a signed token saying, *"Hey, this is definitely John Doe, you can let him in."*

---

The short answer is: **It is handled automatically by the browser and the web server**, but you (the developer) must enable and enforce it.

You don't need to write custom "encryption code" for the username and password strings themselves. Think of HTTPS as a **secure pipe**. Once the pipe is built, anything you throw into it is automatically protected.

---

## 1. How the "Automatic" Part Works

When a user clicks "Login" on a site using `https://`:

1. **The Handshake:** Before the password is sent, the Browser and Server perform a "TLS Handshake." They agree on a secret mathematical key.
2. **The Shielding:** The browser takes the entire HTTP request (Headers, URL, and the Body containing the password) and wraps it in an encrypted layer using that key.
3. **The Transit:** The data travels across the internet as unreadable "ciphertext."
4. **The Reveal:** Your Web Server (IIS, Kestrel, or Nginx) receives the packet, uses its private key to decrypt it, and hands the original, readable "Password123" to your .NET code.

---

## 2. Your Responsibility as a Developer

While the encryption is automatic, the **setup** is not. If you don't configure your environment correctly, the "pipe" stays open and insecure. You must do the following:

### A. Obtain an SSL/TLS Certificate

Your server needs a digital ID card (Certificate) to prove it is who it says it is.

* **Production:** Use a certificate from a provider like **Let's Encrypt** (free) or DigiCert.
* **Development:** .NET Core automatically creates a "Self-Signed" certificate for `localhost`.

### B. Enforce HTTPS in .NET

You must tell your API to reject any connection that isn't secure. In your `Program.cs`, you should have:

```csharp
var app = builder.Build();

// This redirects any http:// request to https:// automatically
app.UseHttpsRedirection(); 

app.MapControllers();
app.Run();

```

### C. Use the `[RequireHttps]` Attribute

For high-security APIs, you can force specific controllers to only respond over secure connections:

```csharp
[RequireHttps]
[ApiController]
[Route("[controller]")]
public class AuthController : ControllerBase { ... }

```

---

## 3. The One Trap: "Mixed Content"

The most common mistake is having a secure API (`https://api.com`) but a non-secure frontend (`http://myweb.com`).

* If the page itself isn't loaded via HTTPS, the browser might not encrypt the outgoing data correctly, or it might block the request entirely to protect the user.
* **Rule of Thumb:** Both the UI and the API must use HTTPS.

---

## Summary Table

| Task | Who does it? |
| --- | --- |
| **Encrypting the data** | The Browser (Automatic via TLS) |
| **Decrypting the data** | The Web Server (Automatic via TLS) |
| **Providing the Certificate** | The Developer (Manual setup) |
| **Enforcing HTTPS redirection** | The Developer (Code in `Program.cs`) |

It is a common point of confusion: `app.UseHttpsRedirection()` **does not actually look for a certificate.**

All that line of code does is act as a "traffic cop." It looks at an incoming request and says: *"Hey, you're coming in on Port 5000 (HTTP). You need to go to Port 5001 (HTTPS) instead."* It sends a `307 Temporary Redirect` message back to the browser.

The actual job of finding and using the certificate belongs to the **Web Server (Kestrel)**. Here is where Kestrel looks for it:

---

## 1. In Development (Localhost)

When you are running on your machine, Kestrel looks in your **OS Certificate Store**:

* **Windows:** It looks in `Manage User Certificates` -> `Personal` -> `Certificates`.
* **macOS:** It looks in your **Keychain Access**.
* **Linux:** It looks in a specific folder, typically `~/.dotnet/corefx/cryptography/x509stores/my/`.

If you haven't run `dotnet dev-certs https --trust`, this store is empty or the certificate isn't trusted, which is why you see "Your connection is not private."

---

## 2. In Production (Manual Setup)

In a real server environment, Kestrel won't find a certificate automatically. You have to tell it where the file is. You usually do this in `appsettings.json`:

```json
"Kestrel": {
  "Endpoints": {
    "Https": {
      "Url": "https://*:5001",
      "Certificate": {
        "Path": "C:\\certs\\my-api-cert.pfx",
        "Password": "YourStrongPassword"
      }
    }
  }
}

```

---

## 3. In the Cloud (Azure / AWS / Docker)

If you are hosting on a service like **Azure App Service**, you don't even need to configure this in code.

* **SSL Termination:** Azure handles the certificate for you. The request hits Azure's "Front Door" via HTTPS, Azure decrypts it, and then sends a plain HTTP request to your code.
* **Forwarded Headers:** In this case, `UseHttpsRedirection` looks at a special header (`X-Forwarded-Proto`) to know that the original user was safe.

---

## Summary: Who does what?

| Component | Responsibility |
| --- | --- |
| **`app.UseHttpsRedirection()`** | Sends the user from `http://` to `https://`. |
| **`launchSettings.json`** | Tells Visual Studio which ports to open. |
| **Kestrel (Web Server)** | Actually loads the certificate from the disk or OS store to encrypt the data. |

---

### How to verify your certificate is working:

Run your API and look at the **Console/Terminal** output. You should see a line that says:
`Now listening on: https://localhost:7001`

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
## Comparison Summary

| Method | Used For | Complexity | State |
| --- | --- | --- | --- |
| **JWT** | Web APIs / Mobile | Medium | Stateless |
| **Cookies** | Web Pages (MVC/Razor) | Low | Stateful |
| **Identity** | User Management | High | Flexible |
| **OIDC/OAuth** | Social/Enterprise Login | Medium | Stateless |
| **API Keys** | System Integration | Low | Stateless |

---

## Which one should you choose?

* If you are building an **Angular/React + Web API**: Use **JWT Bearer**.
* If you are building a **Razor Pages / MVC site**: Use **Cookies**.
* If you want **"Login with Google"**: Use **OpenID Connect**.

---
## Authentication Methods

In .NET Core, authentication is handled by the **Authentication Middleware**, but the "method" you choose depends on whether you are building a traditional website, a modern Single Page Application (SPA), or a mobile-to-API connection.

### 1. Cookie-based Authentication (The Web App Standard)

This is the traditional way to keep a user logged in. The server creates a unique session and stores a "Session ID" in an encrypted cookie in the user's browser.

* **Best For:** Server-side rendered apps (Razor Pages, MVC) or Blazor Server.
* **How it works:** Server validates credentials $\rightarrow$ Issues a secure `HttpOnly` cookie $\rightarrow$ Browser automatically sends this cookie with every request.
* **Pros:** Extremely easy to implement; browser handles the storage and security automatically.

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

### 2. Token Based Authentication (The API Standard)

JSON Web Tokens (JWT) are the industry standard for Web APIs. They are stateless, meaning the server doesn't store session data; instead, the token itself contains all the user info.

* **Best For:** Web APIs, Mobile Apps, SPAs (Angular/React).
* **How it works:** User logs in $\rightarrow$ Server sends a signed Token $\rightarrow$ Client sends Token in `Authorization: Bearer <token>` header for every request.
* **Pros:** Highly scalable; works across different domains.

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

These protocols allow users to "Sign in with..." another provider like Google, Microsoft Entra ID (Azure AD), or GitHub.

* **Best For:** Enterprise apps or apps that want to avoid managing passwords.
* **OIDC:** Handles the **Authentication** (Is this person John?).
* **OAuth 2.0:** Handles the **Authorization** (Can I access John's Google Calendar?).
* **Identity Providers (IdP):** Common choices include Microsoft Entra ID, Auth0, and Duende IdentityServer.

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
1. User → [GET /secure-data] → App ([Authorize] check)
2. App → Redirect to Identity Provider (Microsoft/Google)
3. User → Logs in at Identity Provider
4. Provider → Redirect to /signin-oidc with authorization code
5. App → Exchange code for tokens (ID + access token)
6. App → Sign in user, create session
7. App → Redirect user to /secure-data
8. User → Authenticated, receives data
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

### 4. API Key Authentication (Server-to-Server)

A simple, static string (like a password for a machine) passed in a header (e.g., `X-API-KEY`).

* **Best For:** Internal communication between two servers or background services.
* **Cons:** Not secure for browsers (easily stolen if exposed).

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

### 8. ASP.NET Core Identity (The "All-in-One")

This isn't just a method; it's a complete **Identity Management System**. It provides the database tables, UI, and logic for users, roles, and passwords.

* **Best For:** Apps that need to manage their own user database (Registration, Password Reset, MFA).
* **Key Features:**
* **2FA/MFA:** Built-in support for authenticator apps (TOTP).
* **Passkeys:** Modern support for FIDO2/biometric login.
* **MapIdentityApi:** A newer feature that automatically generates API endpoints for login and registration.
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
