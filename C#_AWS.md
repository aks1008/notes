# AWS Configuration in C#

## Table of Contents

1. [AWS Authentication Options](#aws-authentication-options)
2. [AWS Configuration in .NET](#aws-configuration-in-net)
3. [S3 Integration Example](#s3-integration-example)
4. [AWS Configuration Comparison](#aws-configuration-comparison)
5. [AWS Lambda Deployment](#aws-lambda-deployment)
6. [AWS Lambda Entry Point](#aws-lambda-entry-point)
7. [Local AWS CLI Setup](#local-aws-cli-setup)
8. [AWS .NET Mock Lambda Test Tool](#aws-net-mock-lambda-test-tool)
9. [CloudFormation Stack Management](#cloudformation-stack-management)

---

## AWS Authentication Options

### 1. Environment Variables
- Set `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`

### 2. appsettings.json
- Store credentials in configuration file

### 3. AWS Credentials File
- Linux/macOS: `~/.aws/credentials`
- Windows: `%USERPROFILE%\.aws\credentials`

### 4. IAM Roles or Federated Identity

### C# Configuration Code
```csharp
// Add AWS options
services.AddDefaultAWSOptions(Configuration.GetAWSOptions());

// Configure credentials
awsOptions.Credentials = new BasicAWSCredentials(
    Configuration["AWS:AccessKey"],
    Configuration["AWS:SecretKey"]
);
```

---

## AWS Configuration in .NET

`Configuration.GetAWSOptions()` reads AWS settings (region, credentials) from configuration (e.g., appsettings.json).

`AddDefaultAWSOptions(awsOptions)` makes options available to all AWS service clients via `AddAWSService<T>()`.

### Development Environment
- Reads `appsettings.dev.json`
- Checks for region and credentials
- Automatically available through `AddDefaultAWSOptions`

### Production Environment
- ASPNETCORE_ENVIRONMENT is "Production"
- Skips AccessKey/SecretKey from configuration
- Uses AWS SDK default credential provider chain:
  1. IAM Role for Service (recommended)
  2. Environment Variables
  3. Shared Credentials File
  4. AWS SSO, ECS container credentials

---

## S3 Integration Example

```csharp
var awsOptions = Configuration.GetAWSOptions();
services.AddDefaultAWSOptions(awsOptions);
services.AddAWSService<IAmazonS3>(); // singleton

public class MyService
{
    private readonly IAmazonS3 _s3;
    
    public MyService(IAmazonS3 s3)
    {
        _s3 = s3;
    }
    // Use _s3 for S3 operations
}
```

---

## AWS Configuration Comparison

### With AddDefaultAWSOptions (Preferred)
**Benefits:**
- Centralized configuration
- Consistent settings for all AWS clients
- Easy to change region/credentials in one place

### Without AddDefaultAWSOptions
- Requires individual configuration per client
**Drawbacks:**
- Scattered or implicit configuration
- Harder to manage across multiple clients
- Risk of inconsistent configuration

---

## AWS Lambda Deployment

### Template Selection
- **HTTP API (API Gateway + Lambda):** Use `serverless.AspNetCoreWebAPI` template
- **Background functions (S3, SQS, etc.):** Use `lambda.EmptyFunction` template

### Deployment
- Direct deployment from Visual Studio requires two pieces of information

### Production Deployment
- Typically happens via Terraform + GitHub Actions
- Steps: `dotnet publish`, then `zip`
- Terraform uses `zip` for resource creation
- All these steps occur through GitHub Actions

### Lambda Cold Start
- Should be minimized
- Avoid including unnecessary packages in the .NET Core zip file to reduce cold start time

### S3 Bucket for Deployment
- Lambda deployment packages (compiled code and dependencies) are often too large for direct API uploads
- A deployment tool uploads packages to an S3 bucket
- CloudFormation references this S3 location for Lambda function deployment
- Requires write access to the specified S3 bucket

---

## AWS Lambda Entry Point

### Request Flow
```
Client
-> API Gateway
-> Lambda Function (LambdaEntryPoint)
-> ASP.NET Core Middleware & Routing (Startup.cs)
-> Controller Action (e.g., ValuesController)
-> Response flows back through the same path
```

### LambdaEntryPoint Class
```csharp
namespace AWSLambda;

/// <summary>
/// This class extends from APIGatewayProxyFunction which contains the method FunctionHandlerAsync which is the
/// actual Lambda function entry point. The Lambda handler field should be set to
///
/// AWSLambda::AWSLambda.LambdaEntryPoint::FunctionHandlerAsync
/// </summary>
public class LambdaEntryPoint : Amazon.Lambda.AspNetCoreServer.APIGatewayProxyFunction
{
    // The base class must be set to match the AWS service invoking the Lambda function. If not Amazon.Lambda.AspNetCoreServer
    // will fail to convert the incoming request correctly into a valid ASP.NET Core request.
    //
    // API Gateway REST API                        -> Amazon.Lambda.AspNetCoreServer.APIGatewayProxyFunction
    // API Gateway HTTP API payload version 1.0    -> Amazon.Lambda.AspNetCoreServer.APIGatewayProxyFunction
    // API Gateway HTTP API payload version 2.0    -> Amazon.Lambda.AspNetCoreServer.APIGatewayHttpApiV2ProxyFunction
    // Application Load Balancer                   -> Amazon.Lambda.AspNetCoreServer.ApplicationLoadBalancerFunction
    //
    // Note: When using the AWS::Serverless::Function resource with an event type of "HttpApi" then payload version 2.0
    // will be the default and you must make Amazon.Lambda.AspNetCoreServer.APIGatewayHttpApiV2ProxyFunction the base class.

    protected override void Init(IWebHostBuilder builder)
    {
        builder
            .UseStartup<Startup>();
    }
}
```

### MongoDB Integration Example
```csharp
using MongoDB.Driver;
// ...
client = new MongoClient(connectionString);
var filter = Builders<TestDTO>.Filter.In("first_name", firstName);
var data = await mongoConnection.GetMongoClient.GetCollection<TestDTO>(collectionName).Find(filter).ToListAsync();
```

---

## Local AWS CLI Setup

### Installation
- Install the AWS CLI for Windows, Mac, or Linux from https://aws.amazon.com/cli/

### Configuration
Run `aws configure` in a terminal:
- Enter `AWS Access Key ID`
- Enter `AWS Secret Access Key`
- Enter `default region name` (e.g., `eu-west-1`)
- Enter `default output format` (just press Enter)

---

## AWS .NET Mock Lambda Test Tool

### Overview
- `Amazon.Lambda.TestTool-8.0` is the official tool for local testing and debugging .NET 8 Lambda functions
- Mimics Lambda's behavior on a local machine
- Provides a local web UI
- Supports breakpoints and step-through debugging in Visual Studio

### Installation Commands
```bash
dotnet tool install -g Amazon.Lambda.TestTool-8.0
dotnet lambda-test-tool-8.0
```

---

## CloudFormation Stack Management

### Stack Name Purpose
- Identifies application resources as a group
- Creates/updates stack based on `serverless.template`
- Manages Lambda, API Gateway, IAM roles as single unit

### CloudFormation Overview
AWS service for provisioning and managing resources as a single unit ("stack").
The stack name identifies application resources (Lambda, API Gateway, IAM roles, etc.).
Deploys/updates stacks based on the `serverless.template` file.

----

# AWS Dot Net Core Setup

To configure AWS in a .NET Core (Web API or Console) project in 2026, the industry has standardized on the **Options Pattern** using the `AWSSDK.Extensions.NETCore.Setup` package. This approach allows you to inject AWS clients directly into your controllers and services.

Here is the step-by-step guide to a production-ready configuration.

---

### 1. Install the Required Packages

You need the base setup extension and the specific package for the service you want to use (e.g., S3).

```bash
dotnet add package AWSSDK.Extensions.NETCore.Setup
dotnet add package AWSSDK.S3

```

---

### 2. Configure `appsettings.json`

Instead of hardcoding credentials, you should store the region and profile here.

> **FAANG Security Note:** Never put `AccessKey` or `SecretKey` in this file. In production, the SDK automatically pulls credentials from **IAM Roles** (on EC2/Lambda) or **Environment Variables**.

```json
{
  "AWS": {
    "Region": "us-east-1",
    "Profile": "development-profile" 
  }
}

```

---

### 3. Register Services in `Program.cs`

In .NET Core, you register the AWS configuration once, and it will handle the lifecycle of all your service clients.

```csharp
using Amazon.S3;

var builder = WebApplication.CreateBuilder(args);

// 1. Get AWS options from appsettings.json
var awsOptions = builder.Configuration.GetAWSOptions();

// 2. Register the default options
builder.Services.AddDefaultAWSOptions(awsOptions);

// 3. Register specific service clients (Dependency Injection)
builder.Services.AddAWSService<IAmazonS3>();
// builder.Services.AddAWSService<IAmazonDynamoDB>(); // Example for other services

var app = builder.Build();

```

---

### 4. Inject the Client into your Controller

Now that it's registered, you don't need to `new` up the client. Just ask for it in the constructor.

```csharp
[ApiController]
[Route("[controller]")]
public class S3Controller : ControllerBase
{
    private readonly IAmazonS3 _s3Client;

    public S3Controller(IAmazonS3 s3Client)
    {
        _s3Client = s3Client;
    }

    [HttpGet]
    public async Task<IActionResult> ListBuckets()
    {
        var response = await _s3Client.ListBucketsAsync();
        return Ok(response.Buckets);
    }
}

```

---

### 5. Advanced: Using Secrets Manager for App Config

In a FAANG-level architecture, you don't even store "API Keys" or "Connection Strings" in `appsettings.json`. You use **AWS Secrets Manager** to inject them into your configuration at startup.

**The Setup:**

1. Install `Amazon.Extensions.Configuration.SystemsManager`.
2. Update your host builder:

```csharp
builder.Configuration.AddSystemsManager("/my-web-api/"); 
// This automatically loads all secrets under that path into builder.Configuration

```

---

### Summary Checklist for Interviews

* **How are credentials handled?** Mention the **Credential Resolution Order**: It checks `Environment Variables` → `Shared Credentials File` → `IAM Instance Profile`.
* **Why use `AddAWSService`?** It ensures clients are registered as **Singletons** (best practice), as they are thread-safe and re-usable.
* **Region Management:** Always prefer setting the region in configuration rather than hardcoding it in the C# class constructor.

## S3 Presigned URL 

Generating a pre-signed URL in S3 is a "side-channel" operation. This means the SDK doesn't actually talk to AWS to create the link; it uses your local credentials to perform a mathematical signature on a string.

In .NET Core, you use the `GetPreSignedURL` method from the `IAmazonS3` client.

### 1. Basic Implementation (Download URL)

To allow a user to download a private file, you generate a `GET` URL.

```csharp
using Amazon.S3;
using Amazon.S3.Model;

public string GetDownloadUrl(IAmazonS3 s3Client, string bucketName, string objectKey)
{
    var request = new GetPreSignedUrlRequest
    {
        BucketName = bucketName,
        Key = objectKey,
        Verb = HttpVerb.GET,
        Expires = DateTime.UtcNow.AddMinutes(15) // URL valid for 15 mins
    };

    return s3Client.GetPreSignedURL(request);
}

```

### 2. Implementation for Upload (Upload URL)

If you want a frontend (like React or a mobile app) to upload a file directly to S3 without passing the bytes through your API, you use a `PUT` verb.

```csharp
var request = new GetPreSignedUrlRequest
{
    BucketName = "my-bucket",
    Key = "uploads/user-123.jpg",
    Verb = HttpVerb.PUT,
    ContentType = "image/jpeg", // Must match the header sent by the client
    Expires = DateTime.UtcNow.AddMinutes(30)
};

string uploadUrl = s3Client.GetPreSignedURL(request);

```

### 3. Critical Interview "Gotchas"

When discussing this in an interview, keep these architectural points in mind:

* **Signature Version 4 (SigV4):** Some regions (and newer features like KMS encryption) **require** SigV4. By default, the .NET SDK might use SigV2 in older codebases. You should explicitly set it if you encounter "Invalid Signature" errors:
```csharp
AWSConfigsS3.UseSignatureVersion4 = true;

```


* **The 7-Day Limit:** The maximum expiration time for a pre-signed URL created with IAM user credentials is **7 days**. If you use temporary credentials (like an IAM Role on an EC2 instance), the URL will expire when the role's credentials expire (usually 1–12 hours).
* **No Network Call:** Emphasize that `GetPreSignedURL` is a **local** operation. It does not check if the bucket or file actually exists. It just generates the "permission slip."

### Summary Table

| Use Case | HTTP Verb | Purpose |
| --- | --- | --- |
| **Download** | `GET` | Grant temporary access to a private file. |
| **Upload** | `PUT` | Allow direct upload to bypass your server's bandwidth limits. |
| **Delete** | `DELETE` | Allow a client to remove an object without IAM keys. |

Changing the HTTP verb (the `Verb` property in C#) is what tells S3 whether the URL is a "Key" to open a file (`GET`) or a "Permission Slip" to overwrite one (`PUT`).

However, for a FAANG interview, you need to understand that the verb is **locked into the signature**. If you generate a `GET` URL and the user tries to `PUT` a file to it, S3 will reject it with a `403 Forbidden (SignatureDoesNotMatch)` because the signature calculation includes the verb itself.

---

### The Verb "Menu"

| Verb | Operation | Interview Context (Trade-offs) |
| --- | --- | --- |
| **`GET`** | **Download** | Used to share private objects without making the bucket public. |
| **`PUT`** | **Upload** | **Simpler but Risky.** It doesn't allow you to limit the file size. A user could theoretically upload a 5TB file and stick you with the bill. |
| **`POST`** | **Upload** | **Complex but Secure.** Use `createPresignedPost` (different from `GetPreSignedURL`) to enforce file size limits (e.g., "max 10MB") and specific file types. |
| **`DELETE`** | **Remove** | Rarely used for clients, but useful for temporary "Cleanup" access. |
| **`HEAD`** | **Metadata** | Used to check if a file exists or get its size without downloading the whole thing. |

---

### Pro-Level: The "Header" Trap

When you change the verb to `PUT` for an upload, you often need to include metadata (like `Content-Type`).

**Here is the golden rule:** If you include a property in your C# code when generating the URL, the client **must** send that exact same header when using the URL.

```csharp
// 1. YOUR SERVER (C#)
var request = new GetPreSignedUrlRequest {
    Verb = HttpVerb.PUT,
    ContentType = "image/png" // You SIGNED this header
};

// 2. THE CLIENT (JavaScript/React)
await fetch(presignedUrl, {
    method: "PUT",
    headers: {
        "Content-Type": "image/png" // MUST MATCH exactly or it fails
    },
    body: fileBlob
});

```

### Summary for Interview

> "While changing the HTTP verb defines the operation, the verb is a **signed component** of the URL. In production, I prefer `PUT` for simplicity in small uploads, but I'd recommend `POST` policies if we need to enforce **Content-Length restrictions** to prevent 'Denial of Wallet' attacks where a user uploads massive files."

### Multi Part Upload

In a FAANG interview, you aren't just expected to know *how* to do a multipart upload, but *why* and *when* to use it.

A **Multipart Upload** allows you to break a single large file into smaller "chunks" (parts) and upload them independently. S3 then reassembles them into one object once all parts are received.

---

### 1. When to use it? (The "Golden Rules")

* **Required:** For any file larger than **5 GB** (S3 won't accept a single `PUT` larger than this).
* **Recommended:** For any file larger than **100 MB**.
* **Why?** * **Parallelism:** You can upload multiple parts at once, significantly increasing speed.
* **Resilience:** If one 5MB chunk fails, you only retry that chunk, not the whole 10GB file.
* **Pause/Resume:** You can stop the upload and resume it hours later.



---

### 2. The 3-Step Lifecycle

In .NET, you typically use the **TransferUtility** (high-level) for ease, or the **Low-Level API** for maximum control.

#### Step 1: Initiate

You tell S3, "I'm starting an upload for `my-file.zip`." S3 returns a unique **UploadId**.

```csharp
var initRequest = new InitiateMultipartUploadRequest { 
    BucketName = "my-bucket", 
    Key = "large-video.mp4" 
};
var initResponse = await s3Client.InitiateMultipartUploadAsync(initRequest);
string uploadId = initResponse.UploadId;

```

#### Step 2: Upload Parts

You split your file into chunks (minimum **5 MB** each). For every chunk, you get an **ETag** (a unique ID for that specific part) from S3. You must keep track of these ETags and their part numbers.

```csharp
var uploadRequest = new UploadPartRequest {
    BucketName = "my-bucket",
    Key = "large-video.mp4",
    UploadId = uploadId,
    PartNumber = 1,
    PartSize = 5 * 1024 * 1024, // 5MB
    InputStream = partStream
};
var uploadResponse = await s3Client.UploadPartAsync(uploadRequest);
// Save uploadResponse.ETag and part number to a list

```

#### Step 3: Complete

Once all parts are uploaded, you send the list of ETags back to S3. S3 stitches the file together.

```csharp
var completeRequest = new CompleteMultipartUploadRequest {
    BucketName = "my-bucket",
    Key = "large-video.mp4",
    UploadId = uploadId,
    PartETags = mySavedETags // The list of (PartNumber, ETag)
};
await s3Client.CompleteMultipartUploadAsync(completeRequest);

```

---

### 3. FAANG Interview "Deep Dive" Topics

#### A. The "Unfinished Upload" Bill

If a multipart upload is initiated but never completed or aborted, the uploaded parts sit in S3 hidden from view, and **you are charged for that storage.**

* **Solution:** Mention you would set an **S3 Lifecycle Policy** to automatically delete incomplete multipart uploads after 7 days.

#### B. S3 Transfer Acceleration

For global users (e.g., someone in Japan uploading to a US-East bucket), multipart upload alone might still be slow.

* **Solution:** Combine Multipart Upload with **S3 Transfer Acceleration**, which routes the parts through AWS Edge Locations (CloudFront network) for faster speeds.

#### C. Data Integrity (MD5)

How do you know the file wasn't corrupted during the upload of 1,000 different parts?

* **Solution:** You can provide a `Content-MD5` hash for each part. S3 will verify it and reject the part if the hashes don't match.

---

### 4. Comparison Summary

| Feature | Single PUT Upload | Multipart Upload |
| --- | --- | --- |
| **Max Size** | 5 GB | 5 TB |
| **Speed** | Limited to 1 connection | Parallel (Multiple threads) |
| **Failure** | Must restart whole file | Only retry the failed part |
| **Complexity** | Very Low | Higher (Must manage state/ETags) |