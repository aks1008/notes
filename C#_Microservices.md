# C# Microservices Architecture & Related Topics

## Table of Contents

1. [Microservices Architecture](#microservices-architecture)
   - [Core Concepts](#core-concepts)
   - [Service Communication](#service-communication)
   - [Data Management per Service](#data-management-per-service)
   - [Containerization and Orchestration](#containerization-and-orchestration)
   - [Challenges to Consider](#challenges-to-consider)
2. [API Gateway](#api-gateway)
   - [YARP (Yet Another Reverse Proxy) - Modern Approach](#yarp-yet-another-reverse-proxy---modern-approach)
   - [Ocelot - Traditional Approach](#ocelot---traditional-approach)
   - [Why Use API Gateway in .NET?](#why-use-api-gateway-in-net)
3. [gRPC Communication](#grpc-communication)
   - [Overview](#overview)
   - [Performance Comparison](#performance-comparison)
   - [HTTP/2 Advantages](#http2-advantages)
   - [gRPC Implementation in .NET 8](#grpc-implementation-in-net-8)
   - [When to Use Which?](#when-to-use-which)
4. [Apache Kafka](#apache-kafka)
   - [Overview](#overview-1)
   - [Architecture Flow](#architecture-flow)
   - [Components](#components)
   - [Benefits in .NET Applications](#benefits-in-net-applications)
   - [Use Cases](#use-cases)
   - [Kafka Implementation in .NET Core](#kafka-implementation-in-net-core)
5. [Resilience & Rate Limiting](#resilience--rate-limiting)
   - [Polly v8 - Resilience Pipelines](#polly-v8---resilience-pipelines)
   - [Core Patterns](#core-patterns)
   - [Implementation](#implementation)
   - [Retry vs Circuit Breaker](#retry-vs-circuit-breaker)
   - [Circuit Breaker State Machine](#circuit-breaker-state-machine)
   - [Rate Limiting](#rate-limiting)
   - [Four Core Algorithms](#four-core-algorithms)
   - [Implementation](#implementation-1)
   - [Global vs. Partitioned Limits](#global-vs-partitioned-limits)
   - [Tiered Rate Limiting Example](#tiered-rate-limiting-example)
   - [Best Practices](#best-practices)

---

## Microservices Architecture

### Core Concepts

- **Microservices**: Architectural style that structures an application as a collection of loosely coupled, independently deployable services
- **Cloud providers**: Azure, AWS, etc.
- **Containerization**: Docker and containerization
- **Orchestration**: Kubernetes for container orchestration
- **Event-driven architecture**: Message buses and event-driven architecture
- **API Gateway**: API gateways and service discovery

### Service Communication

#### Synchronous Communication

- **HTTP**: RESTful APIs
- **gRPC**: High-performance RPC framework

#### Asynchronous Communication (Event Bus)

- **RabbitMQ**: Message broker
- **Azure Service Bus**: Cloud messaging service
- **Amazon SQS**: Simple Queue Service
- **Libraries**: MassTransit or Rebus libraries

### Data Management per Service

- **SQL**: Entity Framework Core
- **NoSQL**: MongoDB/Redis

### Containerization and Orchestration

- **Docker**: Container platform
- **Kubernetes (K8s)**: Container orchestration

### Challenges to Consider

- **Complexity**: Managing 20 services is harder than managing one
- **Data Consistency**: Since each service has its own DB, use Saga Pattern or Eventual Consistency instead of traditional SQL transactions
- **Observability**: Need centralized logging (ELK Stack or Seq) to track a single request as it travels through multiple services

---

## API Gateway

### YARP (Yet Another Reverse Proxy) - Modern Approach

Microsoft actively supports and recommends YARP for most new projects.

#### YARP vs Ocelot Comparison

| Feature | Ocelot | YARP |
| :---- | :---- | :---- |
| Creator | Open Source Community | Microsoft |
| Performance | Good (Standard Middleware) | Excellent (Built for high-speed proxying) |
| Philosophy | Feature Rich (Built-in Auth, Rate Limiting) | Extensible (Plugs into existing ASP.NET pipeline) |
| Configuration | JSON-based (ocelot.json) | .NET Configuration (appsettings.json or Code) |
| Maintenance | Community-driven (less active) | Actively maintained by Microsoft |

#### YARP Implementation

```csharp
dotnet add package Yarp.ReverseProxy

// Program.cs

var builder = WebApplication.CreateBuilder(args);

// Load gateway configuration from appsettings.json

builder.Services.AddReverseProxy()

    .LoadFromConfig(builder.Configuration.GetSection("ReverseProxy"));

var app = builder.Build();

app.MapReverseProxy(); // Enable proxy middleware

app.Run();

// appsettings.json

{

  "ReverseProxy": {

    "Routes": {

      "orderRoute": {

        "ClusterId": "orderCluster",

        "Match": { "Path": "/orders/{**catch-all}" }

      }

    },

    "Clusters": {

      "orderCluster": {

        "Destinations": {

          "destination1": { "Address": "http://ordering-service:8080/" }

        }

      }

    }

  }

}
```

### Ocelot - Traditional Approach

```csharp
dotnet add package Ocelot

// Program.cs

builder.Configuration.AddJsonFile("ocelot.json", optional: false, reloadOnChange: true);

builder.Services.AddOcelot();

await app.UseOcelot();

// ocelot.json

{

  "Routes": [

    {

      "UpstreamPathTemplate": "/gateway/writers",

      "UpstreamHttpMethod": [ "Get" ],

      "DownstreamPathTemplate": "/api/writers",

      "DownstreamScheme": "https",

      "DownstreamHostAndPorts": [

        {

          "Host": "localhost",

          "Port": 5002

        }

      ]

    }

  ],

  "GlobalConfiguration": {

    "BaseUrl": "https://localhost:5003"

  }

}
```

#### Ocelot Key Features

- **Rate Limiting**: Restrict client calls within time window
- **Caching**: Cache responses to reduce load
- **Request Aggregation**: Combine multiple microservice responses
- **Authentication**: Centralize JWT validation

### Why Use API Gateway in .NET?

- **Security (BFF Pattern)**: Centralize JWT validation at gateway
- **Rate Limiting**: Protect internal services from being overwhelmed
- **Request Aggregation**: Combine multiple service responses into one
- **Protocol Translation**: Accept REST (HTTP/1.1) but talk to internal services via gRPC (HTTP/2)

---

## gRPC Communication

### Overview

- **gRPC (Google Remote Procedure Call)**: Gold standard for internal service-to-service communication
- **REST**: Great for public-facing APIs (browsers understand it easily)
- **gRPC**: Designed for raw speed, efficiency, and strict contracts between services

### Performance Comparison

| Feature | REST (HTTP/1.1) | gRPC (HTTP/2) |
| :---- | :---- | :---- |
| Data Format | Text (JSON/XML) - bulky | Binary (Protobuf) - tiny and fast |
| Payload Size | Large (includes keys like "name") | Small (uses numeric tags instead of keys) |
| Multiplexing | Requests are sequential (blocking) | Parallel (multiple requests on 1 connection) |
| Streaming | Client-to-Server only | Bi-directional (real-time flow) |
| Contract | Optional (Swagger/OpenAPI) | Strict (Defined in .proto files) |

### HTTP/2 Advantages

- **Multiplexing**: Multiple requests/responses over single TCP connection
- **Header Compression (HPACK)**: Compress repetitive headers
- **Server Push**: Server can proactively send data to client's cache

### gRPC Implementation in .NET 8

```csharp
#### Step 1: Define Contract (.proto file)

syntax = "proto3";

option csharp_namespace = "MyMicroservice.Grpc";

service Greeter {

  // A Unary (Single request/response) call

  rpc SayHello (HelloRequest) returns (HelloReply);

}

message HelloRequest {

  string name = 1; // '1' is binary tag, not value

}

message HelloReply {

  string message = 1;

}

#### Step 2: Server Implementation

public class GreeterService : Greeter.GreeterBase

{

    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)

    {

        return Task.FromResult(new HelloReply { 

            Message = "Hello " + request.Name 

        });

    }

}

#### Step 3: Register in Program.cs

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddGrpc(); // Register gRPC services

var app = builder.Build();

app.MapGrpcService<GreeterService>(); // Map the service

app.Run();
```

### When to Use Which?

- **Use gRPC for**: Internal communication between .NET microservices, high-performance real-time data (stock tickers, chat), environments with limited bandwidth (IoT/Mobile)
- **Use REST for**: Public APIs, browser-based clients, scenarios where human-readability (JSON) is more important than raw speed

---

## Apache Kafka

### Overview
 
 - **Event-driven approach**
 - **Distributed streaming platform**
 - **Publish-subscribe model**
 - **High throughput, fault tolerance, and event sequencing capabilities**
 
```text
+-----------+      +------------+      +------------+
| Producer  |  --> | Kafka Topic|  --> | Consumer(s)|
+-----------+      +------------+      +------------+
```
 
 - We have producers that create new events or data messages and publish them to specific topics in Kafka.
 
 - The central component is Kafka Cluster, comprising multiple Brokers for high availability, fault tolerance,
   data replication and parallelism. The cluster receives these messages and stores them. The Kafka Cluster of this streaming
   data platform works as a center where producers send data, while consumers receive such information. Various brokers in Kafka
   Cluster can store multiple topics. Multiple brokers in a cluster can handle both producer and consumer traffic.
 
 - Zookeeper plays a vital role of managing and coordinating Kafka Brokers. It helps maintain a list of brokers,
   topics and partitions. It helps consumers to read messages in some order, and more importantly, operate leader elections for partition.
 
 - The final element represented in the diagram is the Consumers. Consumers subscribe to one or more topics in Kafka Cluster.
   The consumer reads and processes data in those topics. It can organize consumers into consumer groups to optimize this process,
   enabling load balancing across the system. The consumers act as end receivers of the streaming channel.

```text
+-------------------+              +-------------------+
| Producer(s)       |              | Producer(s)       |
| (send messages)   |              | (send messages)   |
+-------------------+              +-------------------+
          \                                  /
           \                                /
            v                              v

+-------------------------------------------------------+
|                     Kafka Cluster                     |
|   +----------+   +----------+   ...   +----------+     |
|   | Broker 1 |   | Broker 2 |         | Broker N |     |
|   +----------+   +----------+         +----------+     |
|                                                       |
|   Topic A              Topic B              Topic C    |
|   (Partitions          (Partitions          (Partitions|
|    across brokers)      across brokers)      across brokers)
+-------------------------------------------------------+

        +---------------------------+     +---------------------------+
        | Zookeeper                 |     | Zookeeper                 |
        | (manages and coordinates) |     | (ensemble for HA/failover)|
        +---------------------------+     +---------------------------+

+-------------------------------------------------------+
|                      Consumers                         |
|   +------------------+     +------------------+        |
|   | Consumer Group 1 |     | Consumer ...     |        |
|   |  - Consumer 1    |     |                  |        |
|   |  - Consumer 2    |     |                  |        |
|   +------------------+     +------------------+        |
+-------------------------------------------------------+
```
 
 - Confluent.Kafka - NuGet package

### Architecture Flow
 
 UI (Angular) --> API Gateway --> .NET Producer Service (sends to Kafka) --> Kafka --> Consumer Services
 
```text
Producer(s)               Producer(s)
 
(send messages)          (send messages)
 
    |                            |
 
    v                            v
 
---------------- Kafka Cluster -------------------
 
   |          |              |                |              |
 
 [Broker]   [Broker]   [Broker]   ....   [Broker]
 
    |               |                          |
 
 Topic A      Topic B                Topic C
 
(Partition 0)  (Partition 0)         (Partition 0)
 
(Partition 1)  (Partition 1)         (Partition 1)
 
(across brokers)  (across brokers)   (across brokers)
```

### Components

- **Producers**: Create events/data messages and publish to specific topics
- **Kafka Cluster**: Multiple brokers with load balancing, stores messages
- **Partitions**: Divide topics across multiple brokers for replication and parallelism
- **Zookeeper**: Manages and coordinates Kafka brokers, handles leader elections
- **Consumers**: Subscribe to topics, consume and process data, organized in consumer groups

### Benefits in .NET Applications

- **Scalability**: Handle high-volume data streams
- **Fault Tolerance**: Built-in replication and partitioning
- **Real-time Processing**: Event-driven architecture
- **Microservices Communication**: Decoupled service communication
- **Data Persistence**: Durable message storage

### Use Cases

- Real-time analytics
- Log aggregation
- Event sourcing
- Microservices communication
- IoT data processing
- Stream processing

### Kafka Implementation in .NET Core

#### Prerequisites

1. **Kafka Server Setup**: Install and run Apache Kafka (typically via Docker or direct installation)
2. **Create Topics**: Set up topics for your message streams

# Example: Create a topic using Kafka command line tools

kafka-topics.sh --create --topic user-events --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1

#### Step 1: Install NuGet Packages

# Main Kafka client library

dotnet add package Confluent.Kafka

# For dependency injection and configuration

dotnet add package Microsoft.Extensions.Hosting

dotnet add package Microsoft.Extensions.Configuration

#### Step 2: Configure Kafka Settings

// appsettings.json

{

  "Kafka": {

    "BootstrapServers": "localhost:9092",

    "GroupId": "my-consumer-group",

    "Topics": {

      "UserEvents": "user-events",

      "OrderEvents": "order-events"

    }

  }

}

#### Step 3: Create Kafka Configuration Model

public class KafkaSettings

{

    public string BootstrapServers { get; set; }

    public string GroupId { get; set; }

    public TopicsSettings Topics { get; set; }

}

public class TopicsSettings

{

    public string UserEvents { get; set; }

    public string OrderEvents { get; set; }

}

#### Step 4: Implement Kafka Producer Service

using Confluent.Kafka;

using Microsoft.Extensions.Options;

using System.Text.Json;

public interface IKafkaProducerService

{

    Task ProduceAsync<T>(string topic, T message);

    Task ProduceUserEventAsync(UserEvent userEvent);

}

public class KafkaProducerService : IKafkaProducerService, IDisposable

{

    private readonly IProducer<Null, string> _producer;

    private readonly KafkaSettings _settings;

    public KafkaProducerService(IOptions<KafkaSettings> settings)

    {

        _settings = settings.Value;

        

        var config = new ProducerConfig

        {

            BootstrapServers = _settings.BootstrapServers,

            // Enable acknowledgments from all replicas

            Acks = Acks.All,

            // Retry configuration

            MessageSendMaxRetries = 3,

            RetryBackoffMs = 1000,

            // Performance settings

            BatchSize = 16384,

            LingerMs = 10

        };

        _producer = new ProducerBuilder<Null, string>(config).Build();

    }

    public async Task ProduceAsync<T>(string topic, T message)

    {

        try

        {

            var serializedMessage = JsonSerializer.Serialize(message);

            var kafkaMessage = new Message<Null, string>

            {

                Value = serializedMessage,

                Headers = new Headers

                {

                    { "message-type", System.Text.Encoding.UTF8.GetBytes(typeof(T).Name) }

                }

            };

            var deliveryResult = await _producer.ProduceAsync(topic, kafkaMessage);

            Console.WriteLine($"Message delivered to {deliveryResult.TopicPartitionOffset}");

        }

        catch (ProduceException<Null, string> ex)

        {

            Console.WriteLine($"Delivery failed: {ex.Error.Reason}");

            throw;

        }

    }

    public async Task ProduceUserEventAsync(UserEvent userEvent)

    {

        await ProduceAsync(_settings.Topics.UserEvents, userEvent);

    }

    public void Dispose()

    {

        _producer?.Dispose();

    }

}

#### Step 5: Implement Kafka Consumer Service

using Confluent.Kafka;

using Microsoft.Extensions.Hosting;

using Microsoft.Extensions.Options;

using System.Text.Json;

public class KafkaConsumerService : BackgroundService

{

    private readonly IConsumer<Ignore, string> _consumer;

    private readonly KafkaSettings _settings;

    private readonly ILogger<KafkaConsumerService> _logger;

    public KafkaConsumerService(

        IOptions<KafkaSettings> settings,

        ILogger<KafkaConsumerService> logger)

    {

        _settings = settings.Value;

        _logger = logger;

        var config = new ConsumerConfig

        {

            BootstrapServers = _settings.BootstrapServers,

            GroupId = _settings.GroupId,

            AutoOffsetReset = AutoOffsetReset.Earliest,

            EnableAutoCommit = false, // Manual commit for better control

            // Security settings (if needed)

            // SecurityProtocol = SecurityProtocol.SaslSsl,

            // SaslMechanism = SaslMechanism.ScramSha256,

            // SaslUsername = "your-username",

            // SaslPassword = "your-password"

        };

        _consumer = new ConsumerBuilder<Ignore, string>(config).Build();

    }

    protected override async Task ExecuteAsync(CancellationToken stoppingToken)

    {

        // Subscribe to multiple topics

        var topics = new[] { _settings.Topics.UserEvents, _settings.Topics.OrderEvents };

        _consumer.Subscribe(topics);

        _logger.LogInformation("Kafka consumer started. Listening to topics: {Topics}", string.Join(", ", topics));

        while (!stoppingToken.IsCancellationRequested)

        {

            try

            {

                var consumeResult = _consumer.Consume(stoppingToken);

                

                await ProcessMessageAsync(consumeResult);

                

                // Manually commit offset

                _consumer.Commit(consumeResult);

            }

            catch (ConsumeException ex)

            {

                _logger.LogError($"Consume error: {ex.Error.Reason}");

            }

            catch (OperationCanceledException)

            {

                _logger.LogInformation("Kafka consumer stopping...");

                break;

            }

        }

    }

    private async Task ProcessMessageAsync(ConsumeResult<Ignore, string> consumeResult)

    {

        try

        {

            var messageType = consumeResult.Message.Headers.FirstOrDefault(h => h.Key == "message-type")?.GetValue();

            var messageValue = consumeResult.Message.Value;

            _logger.LogInformation($"Received message from topic {consumeResult.Topic}: {messageValue}");

            // Route message based on topic or message type

            switch (consumeResult.Topic)

            {

                case var topic when topic == _settings.Topics.UserEvents:

                    await HandleUserEventAsync(messageValue);

                    break;

                case var topic when topic == _settings.Topics.OrderEvents:

                    await HandleOrderEventAsync(messageValue);

                    break;

                default:

                    _logger.LogWarning($"Unknown topic: {consumeResult.Topic}");

                    break;

            }

        }

        catch (Exception ex)

        {

            _logger.LogError($"Error processing message: {ex.Message}");

        }

    }

    private async Task HandleUserEventAsync(string message)

    {

        var userEvent = JsonSerializer.Deserialize<UserEvent>(message);

        _logger.LogInformation($"Processing user event: {userEvent.UserId} - {userEvent.Action}");

        

        // Your business logic here

        await Task.CompletedTask;

    }

    private async Task HandleOrderEventAsync(string message)

    {

        var orderEvent = JsonSerializer.Deserialize<OrderEvent>(message);

        _logger.LogInformation($"Processing order event: {orderEvent.OrderId} - {orderEvent.Status}");

        

        // Your business logic here

        await Task.CompletedTask;

    }

    public override void Dispose()

    {

        _consumer?.Close();

        _consumer?.Dispose();

        base.Dispose();

    }

}

#### Step 6: Define Message Models

public class UserEvent

{

    public string UserId { get; set; }

    public string Action { get; set; } // Created, Updated, Deleted

    public DateTime Timestamp { get; set; }

    public Dictionary<string, object> Metadata { get; set; }

}

public class OrderEvent

{

    public string OrderId { get; set; }

    public string Status { get; set; } // Created, Processing, Shipped, Delivered

    public decimal Amount { get; set; }

    public DateTime Timestamp { get; set; }

    public string CustomerId { get; set; }

}

#### Step 7: Register Services in Program.cs

// Program.cs

using Microsoft.Extensions.Configuration;

// Add configuration

builder.Services.Configure<KafkaSettings>(

    builder.Configuration.GetSection("Kafka"));

// Register Kafka services

builder.Services.AddSingleton<IKafkaProducerService, KafkaProducerService>();

builder.Services.AddHostedService<KafkaConsumerService>();

// Example usage in a controller

[ApiController]

[Route("api/[controller]")]

public class UsersController : ControllerBase

{

    private readonly IKafkaProducerService _kafkaProducer;

    public UsersController(IKafkaProducerService kafkaProducer)

    {

        _kafkaProducer = kafkaProducer;

    }

    [HttpPost]

    public async Task<IActionResult> CreateUser([FromBody] CreateUserRequest request)

    {

        // Create user logic here...

        

        // Publish event to Kafka

        var userEvent = new UserEvent

        {

            UserId = request.UserId,

            Action = "Created",

            Timestamp = DateTime.UtcNow,

            Metadata = new Dictionary<string, object>

            {

                { "email", request.Email },

                { "source", "api" }

            }

        };

        await _kafkaProducer.ProduceUserEventAsync(userEvent);

        

        return Ok(new { Message = "User created and event published" });

    }

}

#### Step 8: Advanced Features

##### Schema Registry Integration

// For Avro/JSON Schema support

// dotnet add package Confluent.SchemaRegistry

// dotnet add package Confluent.SchemaRegistry.Serdes

public class AdvancedKafkaProducerService

{

    private readonly IProducer<string, UserEvent> _producer;

    public AdvancedKafkaProducerService()

    {

        var config = new ProducerConfig

        {

            BootstrapServers = "localhost:9092"

        };

        var schemaRegistryConfig = new SchemaRegistryConfig

        {

            Url = "http://localhost:8081"

        };

        var schemaRegistry = new CachedSchemaRegistryClient(schemaRegistryConfig);

        var avroSerializer = new AvroSerializer<UserEvent>(schemaRegistry);

        _producer = new ProducerBuilder<string, UserEvent>(config)

            .SetValueSerializer(avroSerializer)

            .Build();

    }

}

##### Transaction Support

public async Task ProduceTransactionallyAsync(string topic, IEnumerable<object> messages)

{

    using var transaction = _producer.BeginTransaction();

    

    try

    {

        foreach (var message in messages)

        {

            var serialized = JsonSerializer.Serialize(message);

            var kafkaMessage = new Message<Null, string> { Value = serialized };

            await _producer.ProduceAsync(topic, kafkaMessage);

        }

        

        await transaction.CommitAsync();

    }

    catch

    {

        await transaction.AbortAsync();

        throw;

    }

}

#### Best Practices

1. **Error Handling**: Implement proper error handling and retry logic
2. **Serialization**: Use efficient serialization (Avro, Protobuf) for production
3. **Monitoring**: Add metrics and logging for consumer lag and throughput
4. **Security**: Use SASL/SSL for secure communication in production
5. **Consumer Groups**: Use appropriate consumer group strategies for load balancing
6. **Idempotence**: Enable idempotent producers for exactly-once semantics
7. **Testing**: Use testcontainers or embedded Kafka for integration testing

#### Testing Kafka Integration

// Integration test example

[Test]

public async Task Should_Produce_And_Consume_Message()

{

    // Arrange - Use testcontainers or embedded Kafka

    var producer = new KafkaProducerService(settings);

    var message = new UserEvent { UserId = "123", Action = "Created" };

    

    // Act

    await producer.ProduceUserEventAsync(message);

    

    // Assert - Verify message was consumed

    // Implementation depends on your test setup

}

---

## Resilience & Rate Limiting

### Polly v8 - Resilience Pipelines

- More performant and integrates natively with .NET 8/9 through "Microsoft.Extensions.Http.Resilience" package

### Core Patterns

- **Retry**: Automatically tries an operation again when it fails (best for "blips")
- **Circuit Breaker**: "Trips" and stops all requests to a failing system for a set time to prevent overloading

### Implementation

# Install NuGet Package

dotnet add package Microsoft.Extensions.Http.Resilience

// Program.cs

builder.Services.AddHttpClient("MyService")

.AddStandardResilienceHandler(options =>

{

    // Customize Retry strategy

    options.Retry.MaxRetryAttempts = 3;

    options.Retry.BackoffType = DelayBackoffType.Exponential;

    

    // Customize Circuit Breaker strategy

    options.CircuitBreaker.FailureRatio = 0.5; // Break if 50% of requests fail

    options.CircuitBreaker.SamplingDuration = TimeSpan.FromSeconds(30);

});

### Retry vs Circuit Breaker

| Feature | Retry | Circuit Breaker |
| :---- | :---- | :---- |
| Goal | Fixes transient errors | Prevents cascading failures |
| Analogy | Try calling again in a second | Stop calling; line is down |
| When to use | Network glitches, 503 errors | Total service outages, high latency |
| Risk | Can hammer a dying service | Prevents any calls (even if it might work) |

### Circuit Breaker State Machine

- **Closed**: Normal operation; requests flow through
- **Open**: Failure threshold reached; requests fail fast immediately without calling the service
- **Half-Open**: Waiting period ended; allows a trial request to see if the service recovered

### Rate Limiting

- Built-in middleware that restricts the number of requests a user or client can make to your API within a specific timeframe
- Critical security and stability feature to prevent "noisy neighbors," brute-force attacks, and server resource exhaustion

### Four Core Algorithms

1. **Fixed Window**
     
   - Uses a fixed time frame (e.g., 10 requests every 1 minute)
   - Counter resets exactly at the end of that window
   - Best for: Simple API limits where "bursts" at the very end aren't a major concern

   

2. **Sliding Window**
     
   - Divides time frame into segments, "slides" forward
   - Prevents double-burst of traffic at the boundary of fixed reset
   - Best for: Preventing spikes at the turn of the minute

   

3. **Token Bucket**
     
   - Tokens added to "bucket" at a steady rate, each request consumes one token
   - Allows occasional bursts while maintaining a consistent average rate
   - Best for: Allowing bursts of high traffic over the long term

   

4. **Concurrency**
     
   - Limits the number of simultaneous active requests
   - Doesn't look at time at all
   - Best for: Protecting heavy-processing endpoints (image manipulation, PDF generation)

### Implementation

// Program.cs

using System.Threading.RateLimiting;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddRateLimiter(options =>

{

    // Define a "Fixed Window" policy named "fixed"

    options.AddFixedWindowLimiter(policyName: "fixed", opt =>

    {

        opt.PermitLimit = 5;            // Max 5 requests

        opt.Window = TimeSpan.FromSeconds(10); // Per 10 seconds

        opt.QueueLimit = 2;             // Queue up to 2 extra requests

        opt.QueueProcessingOrder = QueueProcessingOrder.OldestFirst;

    });

    // Optional: Customize rejection response

    options.RejectionStatusCode = StatusCodes.Status429TooManyRequests;

});

var app = builder.Build();

app.UseRateLimiter(); // Must be called after UseRouting

app.MapGet("/api/data", () => Results.Ok("Success!"))

   .RequireRateLimiting("fixed"); // Apply the policy here

app.Run();

### Global vs. Partitioned Limits

- **Global Limiter**: Limits every single request coming to the server, regardless of who is calling
- **Partitioned Limiter**: Most common approach, limits requests per User ID or per IP Address

### Tiered Rate Limiting Example

builder.Services.AddRateLimiter(options =>

{

    options.AddPolicy("TieredPolicy", httpContext =>

    {

        // 1. Identify the user (e.g., via a claim in a JWT)

        var userTier = httpContext.User.FindFirst("tier")?.Value ?? "Free";

        var userName = httpContext.User.Identity?.Name ?? "Anonymous";

        // 2. Return a partition based on the user's name/ID

        if (userTier == "Premium")

        {

            return RateLimitPartition.GetFixedWindowLimiter(

                partitionKey: userName,

                factory: _ => new FixedWindowRateLimiterOptions

                {

                    PermitLimit = 100, // High limit

                    Window = TimeSpan.FromMinutes(1)

                });

        }

        return RateLimitPartition.GetFixedWindowLimiter(

            partitionKey: userName,

            factory: _ => new FixedWindowRateLimiterOptions

            {

                PermitLimit = 10, // Low limit

                Window = TimeSpan.FromMinutes(1)

            });

    });

    // Custom response for blocked users

    options.OnRejection = async (context, token) =>

    {

        context.HttpContext.Response.StatusCode = 429;

        await context.HttpContext.Response.WriteAsync("Rate limit exceeded. Upgrade to Premium for more requests!", token);

    };

});

#### User Tier Comparison

| User | Requests Allowed | Period | Benefit |
| :---- | :---- | :---- | :---- |
| Free | 10 | 1 Minute | Basic access, prevents server abuse |
| Premium | 100 | 1 Minute | High-speed integration for power users |
| Anonymous | 5 | 1 Minute | Highly restrictive to prevent bot scraping |

### Best Practices

- **Use Redis for Distributed Apps**: For multiple server instances, use Redis-based rate limiting
- **Inform Client**: Send headers back so developers know their status (X-RateLimit-Remaining)
- **Combine with Polly**: Use .NET Rate Limiting for incoming requests, Polly for outgoing HttpClient calls