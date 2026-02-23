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