---
title: "Serverless APIs? This will help you choose the right one!"
seoTitle: "Serverless APIs? This will help you choose the right one!"
seoDescription: "Discover how to choose the right AWS Serverless API service, focusing on Amazon API Gateway, AWS AppSync, and AWS Lambda Function URLs"
datePublished: Mon Oct 14 2024 18:22:43 GMT+0000 (Coordinated Universal Time)
cuid: cm29cdobq000309lch403fgo3
slug: serverless-apis-this-will-help-you-choose-the-right-one
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1728835436902/cec93864-927d-46d7-9213-ea317bca614b.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1728917086342/66079cfd-50a8-4ad6-afbe-132945d84877.png
tags: aws, apis, serverless, api-gateway, appsync, api-basics, lambda-function-urls

---

APIs are the core component that is bringing in the integrations with Server or backend with the client that could be a front-end or another service or even a third-party application that is invoking them.

![API architecture in a nutshell](https://cdn.hashnode.com/res/hashnode/image/upload/v1728906392000/88be7065-84a9-4c9d-9a4f-6be5a27240b1.png align="center")

Application Programming Interface (APIs) are not only external facing and consumed by other systems or clients but APIs are broadly used for inter-service and layer communications.

Are you someone building APIs with AWS Serverless stack? Architecting them would mean that you primarily work with 3 specific AWS Services - **Amazon API Gateway, AWS AppSync** and **AWS Lambda Function URLs**.

# API Considersations

Before looking into what these 3 API services from AWS offer, let’s understand how do we identify key features that can an architect/developer should know before making a choice of API service.

## API Type

The key deciding factor is what kind of API are you trying to build.

* **RESTful APIs**: Stateless architecture that uses the standard HTTP protocol and methods (GET, POST, PUT, DELETE) for the CRUD operations. It is one of the widely used API type for modern APIs.
    
* **GraphQL**: Query language based API server which allows to request the data that is required by the client and support complex queries with real-time updates with subscriptions.
    
* **Websockets**: The protocol for bidirectional communication which uses full-duplex channels over a TCP connection and used in real-time systems where low latency is crucial.
    

## API Interactions

The data exchange works between the client and backend in the real world.

* **Unidirectional**: Communication flows in one direction, either from the client to the server or vice versa. For example, a client requests data from a server without expecting any further communication.
    
* **Bidirectional:** Allows communication in both directions, enabling continuous interaction between the client and server. This is essential for applications needing real-time updates, such as chat apps.
    

## API Response

The way how the API is expected to respond to the request.

* **Synchronous APIs:** The client waits for a response after making a request. This blocking behavior is suitable for operations requiring immediate feedback.
    
* **Asynchronous APIs:** The client can continue executing other tasks while waiting for a response. This non-blocking behavior enhances efficiency and scalability.
    

## API Response Structure

The structure of API responses can vary:

* **Standardized Formats:** Many APIs return responses in standardized formats like JSON or XML.
    
* **Customizable Structures:** Some APIs allow customization of response formats based on client needs.
    

## Supported content-type

APIs can support various content types including:

* Standard content-types like `application/json`, `application/xml`, `text/html` and more.
    
* Custom content-types defined by the API layer or application.
    

## Authentication and Authorization

Support for various methods of authentication and authorization for the APIs, making it more secure.

* **API keys**: Keys which are passed to the API via headers that grants access to API actions.
    
* **IAM roles**: Roles that use polices to grant access to API actions and resources.
    
* **Identity providers**: Identity providers such as Amazon Cognito, Okta and others which authentication the users and authorizes them with tokens passed to API layers.
    
* **Custom Lambda authorizers**: Lambda functions that works as the authorizer by providing access to APIs with valid bearer tokens.
    

# Amazon API Gateway

Amazon API Gateway is a fully managed AWS Service for the API layer with RESTful APIs and Websocket APIs. API Gateway supports various integrations with Lambda Functions, ECS and also direct integrations with other AWS Services with the Velocity Template Language (VTL).

API Gateway also facilitates creation and managing of API keys with different rate-limit and throttling capabilities that not only ensures the security but also ensures the application is not abused with excessive API invocations.

| Protocol | HTTPs, WebSocket |
| --- | --- |
| API Type | RESTful, WebSocket |
| Security | API keys, IAM, Cognito, Custom authorizers |
| Caching | Various caching options available |
| Offline capability | N/A |
| Integration type | REST APIs, Lambda functions, selective AWS Services |
| API operation | CRUD with HTTP methods |
| Direct integration with AWS services with transformation | Mapping request and response templates |
| Throttling and rate-limits | Granular control with usage plan |

# AWS AppSync

AWS AppSync is a fully managed GraphQL API server that allows clients (front-end mobile/web) and went GraphQL clients to query data from multiple data sources where data sources integrate with multiple AWS Services directly with Velocity Template Language (VTL) and also JavaScript resolvers making it easier to design queries, mutations and subscriptions from AppSync really easy!

| Protocol | HTTPs, WebSocket |
| --- | --- |
| API Type | GraphQL |
| Security | API keys, IAM, Cognito |
| Caching | Simple caching option available |
| Offline capability | Supported with AppSync resolvers and SDK |
| Integration type | Data Sources - REST APIs, Lambda functions, AWS Services |
| API operation | GraphQL operations - Query, Mutation and Subscription |
| Direct integration with AWS services with transformation | AppSync resolvers with VTL and JS runtime - both unit resolvers and pipeline resolvers |
| Throttling and rate-limits | Limited throttling options for the APIs |

Here is [a series on AppSync](https://blog.theserverlessterminal.com/series/appsync) that explains about various features of AppSync and also architectures leveraging AppSync.

# AWS Lambda Function URLs

AWS Lambda Functions now supports URLs that can trigger the Lambda function directly without any API Gateway making is ideal for internal APIs and for public APIs it also supports authentication with IAM roles. Although the API schema is defined and structured in the core logic of Lambda function, this supports different content-types defined by the Lambda function.

The added value of Lambda function URLs is enabling streaming responses from Lambda function which improves the Time To First Byte (TTFB), performance and better user experience when used for web and multimedia content. Read more about [Streaming Responses via Lambda function](https://blog.theserverlessterminal.com/streaming-responses-via-aws-lambda).

These Lambda functions URLs can use CloudFront distributions to make them available on the Edge.

| Protocol | HTTPs |
| --- | --- |
| API Type | RESTful in nature |
| Security | IAM roles |
| Caching | N/A |
| Offline capability | N/A |
| Integration type | Lambda function based integrations |
| API operation | Defined by Lambda function |
| Direct integration with AWS services with transformation | N/A |
| Throttling and rate-limits | N/A |

# When to choose

| Use-case | Amazon API Gateway | AWS AppSync | AWS Lambda Function URL |
| --- | --- | --- | --- |
| Building RESTful APIs which uses different HTTP methods and request/response transformation | ✅ Ideal for this. | ❌ Follows GraphQL but handles request/response transformations in VTL/JS resolvers and pipeline resolvers. | ✅ Lambda fURLs would require more management and overhead to maintain the routes and request/response transformation in Lambda function logic. |
| APIs which streams the responses with mulit-media content that requires longer load time | ❌ It may timeout based on data payload/size. | ❌ Streaming cannot be supported with queries, workaround with subscription is possible but not cost friendly and management efforts. | ✅ Ideally with response streaming. |
| APIs for mobile applications which is a chat heavy workload | ❌ Possible with websockets but managing connections and connection timeouts is a hassle so for a chat, it’s no. | ✅ AppSync Subscriptions is best fit for this use-case. | ❌ Works on the principle of request-response or streaming. |
| APIs that perform CRUD operations on a DynamoDB table | ✅ Possbile with direct integrations. | ✅ Possbile with direct integrations. In terms of performance, AppSync is faster and more reliable. | ✅ Possbile with Lambda function using AWS SDK. |
| APIs with different monetized API keys for different usage purposes | ✅ A simple configuration with API Keys, Usage Plans with rate-limit and throttling set. | ❌ Doesn’t support rate-limiting and throttling which makes it hard to track usage based on keys. | ❌ Supports only IAM authentication and tracking based on it is not possible. |
| Preflight validation of input parameters for the API | ✅ Supported with models. | ✅ Follows a strict GraphQL schema. | ❌ Manually in the Lambda function, preflight is not available. |
| APIs with multiple data sources and aggregating response | ❌ Ideally performed in the compute layer. | ✅ AppSync supports merged APIs and pipeline resolvers help with aggregating the response. | ❌ Lambda function can perform it but not built for this case. |

# Wrap up

In a nutshell, how do you decide API Gateway v/s AppSync v/s Lambda function URLs.

| AWS Service | Amazon API Gateway | AWS AppSync | AWS Lambda Function URL |  |
| --- | --- | --- | --- | --- |
| Protocol | HTTPs, WebSocket | HTTPs, WebSocket | HTTPs |  |
| API Type | RESTful, WebSocket | GraphQL | RESTful in nature |  |
| Security | API keys, IAM, Cognito, Custom authorizers | API keys, IAM, Cognito | IAM roles |  |
| Caching | Various caching options available | Simple caching option available | \- |  |
| Offline capability | \- | Supported with AppSync resolvers and SDK | \- |  |
| Integration type | REST APIs, Lambda functions, selective AWS Services | Data Sources - REST APIs, Lambda functions, AWS Services | Lambda function based integrations |  |
| API operation | CRUD with HTTP methods | GraphQL operations - Query, Mutation and Subscription | Defined by Lambda function |  |
| Direct integration with AWS services with transformation | Mapping request and response templates | AppSync resolvers with VTL and JS runtime - both unit resolvers and pipeline resolvers | \- |  |
| Throttling and rate-limits | Granular control with usage plans | Limited throttling options for the APIs | \- |  |
| Pricing | No of requests per month, additional for caching | Priced for queries, data changes and real-time updates separately | As per Lambda function execution, addition data cost for response streaming |  |