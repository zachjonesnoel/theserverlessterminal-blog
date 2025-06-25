---
title: "Building Serverless apps with more configurations"
seoTitle: "Building Serverless apps with more configurations"
seoDescription: "This blog post explains the up-side of using direct integrations with AWS Services and how it could be achieved with just configurations!"
datePublished: Sat Feb 11 2023 18:19:46 GMT+0000 (Coordinated Universal Time)
cuid: cle0a8e3m000109mi2lxc0cwh
slug: building-serverless-apps-with-more-configurations
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1676106557912/6f1811b4-23db-4acd-bdd2-fdcb1b9c07cf.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1676106823967/8a25ef57-7a64-4066-89f9-4d9080271890.png
tags: aws, applications, serverless, stepfunction, aws-eventbridge

---

Applications built on the Serverless stack have existed for nearly a decade with [AWS Lambda functions](https://aws.amazon.com/lambda/) being launched in 2014. And ever since, the association of a Serverless application is always with a Lambda function.

![](https://lh4.googleusercontent.com/9gm9orKCYaUaeXPo1LwGtHd6gVWmvpLgmmzf4BG4BYaJDcWSocIWpUlgB_D_ygfwhPDswDMhv6slA2pgKb_hozhfsdoITEpqStW3vgdCtlvXErZ4B_0upnKkocNayhoodg-jNZjX9zKS1fA=s2048 align="center")

In this blog post, we will look into how Serverless apps can be built with just configurations and leveraging the features of AWS Services which can integrate with different services.

## Why do we need direct integrations?

The AWS Services with direct integrations capabilities improve the architectures by removing the *glue code* needed.

Just let us think about building REST APIs with [AWS API Gateway](https://aws.amazon.com/api-gateway/) which returns a particular set of data. For this, we would have to have an API Gateway stage in which `GET` method invoking a Lambda function which is either using `query` or `scan` to search the items on DynamoDB. The response from DyanmoDB SDK would then be returned to API Gateway from the Lambda function.

![Architecture of API Gateway + Lambda Fn + DynamoDB table](https://cdn.hashnode.com/res/hashnode/image/upload/v1676110093802/d6bab7fc-c0f7-4bf7-ab8e-d3117c0c88e6.png align="center")

Now, let's understand how direct integrations with this would help.

![Upside of direct integrations](https://cdn.hashnode.com/res/hashnode/image/upload/v1676113860200/4223d6b3-e74b-42d1-818f-d68e5d757624.png align="center")

### Cold Starts

Now that we are familiar with the downside of having a Lambda would be the Cold Starts that we would have to be mindful of and also have a workaround to overcome them. But when doing a direct integration with different AWS Services, since we would not have a Lambda function as the middle-man or the glue code, we would not have to worry about the problem of Cold Starts.

### Latency

With the removal of an additional component from the architecture, we are reducing the latency with the requests routed with a Lambda function and returning the results back. Although this would be in milliseconds, it is very accountable in a production environment where there would be 100s and 1000s of requests at a given time.

### Scalability

"Would it be highly scalable when we have concurrent requests that are coming in?" With Lambda the other question of concern is, "how can we handle concurrency and ensure the application is scalable proof?" When we do a direct integration this responsibility would be on the cloud provider and the AWS Service would ensure your application could scale better.

## Direct integrations with AWS Services

### AWS Step Functions

Serverless applications which would need orchestration and a dedicated workflow that would integrate with multiple AWS Services are a huge win! Starting from AWS re:Invent 2021, where Step Functions announced supporting AWS SDK-based integrations there have been over 300+ API actions that are currently been supported.

![AWS Step Functions with SDK support](https://cdn.hashnode.com/res/hashnode/image/upload/v1676118266752/fa681c17-e632-444b-a7e0-1390f9a5b5e1.png align="center")

There are a lot of possibilities that would open up with SDK-based integrations with AWS Step Functions, there is a [blog post series](https://blog.theserverlessterminal.com/series/aws-step-functions) about the same.

### Amazon EventBridge

Amazon EventBridge plays a vital role in event-driven, choreography-based Serverless architectures. While EventBridge supports different integrations with different `event sources` or `destinations` and also configuring HTTP endpoints as `API destinations`.

![Amazon EventBridge direct service integrations](https://cdn.hashnode.com/res/hashnode/image/upload/v1676119765020/b8fe7778-2c76-4825-a155-9ffdd3df2a07.png align="center")

Not only does it support AWS Services which can post events to the `event bus` but also with the new [EventBridge Pipes](https://aws.amazon.com/eventbridge/pipes/), it is possible to have configurations that can integrate service to service.

For instance, before DynamoDB Streams could only trigger a Lambda function but now with EventBridge Pipes, you can build integrations where the source of the event originating from DynamoDB Streams and then it could be targeted to invoke a Step Functions state machine execution.

### Amazon API Gateway

API Gateway can integrate with Velocity Template Langauge (VTL) which could be used for request and response resolver mapping and integrate with services like DynamoDB, Step Functions, Kinesis, and more.

![AWS API Gateway with direct integrations](https://cdn.hashnode.com/res/hashnode/image/upload/v1676131485450/0f9a31eb-41e8-4ac6-9a3b-4ea3258deb99.png align="center")

Like the above mentioned example, API Gateway can directly integrate with DynamoDB. Take a look at the [pattern from ServerlessLand](https://serverlessland.com/patterns/apigw-dynamodb). And the snippet of the same of how you can integrate an API Gateway to `query` DynamoDB.

```yaml
  MusicArtistMethodGet:
    Type: 'AWS::ApiGateway::Method'
    Properties:
      RestApiId: !Ref Api
      ResourceId: !Ref MusicArtistResource
      HttpMethod: GET
      ApiKeyRequired: true
      AuthorizationType: NONE
      RequestParameters:
        method.request.path.artist: true
      Integration:
        Type: AWS
        Credentials: !GetAtt APIGatewayRole.Arn
        IntegrationHttpMethod: POST
        Uri: !Sub 'arn:aws:apigateway:${AWS::Region}:dynamodb:action/Query'
        PassthroughBehavior: WHEN_NO_TEMPLATES
        RequestParameters:
          integration.request.path.artist: method.request.path.artist
        RequestTemplates:
          application/json: "{\"TableName\":\"Music\",\"IndexName\":\"Artist-Index\",\"KeyConditionExpression\":\"artist=:v1\",\"ExpressionAttributeValues\":{\":v1\":{\"S\":\"$util.urlDecode($input.params('artist'))\"}}}"
        IntegrationResponses:
          - StatusCode: '200'
            ResponseTemplates:
              application/json: "#set($inputRoot = $input.path('$'))\n{\n\t\"music\": [\n\t\t#foreach($field in $inputRoot.Items) {\n\t\t\t\"id\": \"$field.id.S\",\n\t\t\t\"artist\": \"$field.artist.S\",\n\t\t\t\"album\": \"$field.album.S\"\n\t\t}#if($foreach.hasNext),#end\n\t\t#end\n\t]\n}"
      MethodResponses:
        - StatusCode: '200'
```

### AWS AppSync

AppSync being a Serverless GraphQL offering, the `queries`, `mutations` and `subscriptions` can use data sources which are DynamoDB, Aroura, Open Search, or HTTP endpoints other than the expected AWS Lambda Functions.

![AppSync integrations with VTL resolvers](https://cdn.hashnode.com/res/hashnode/image/upload/v1676132514833/f42bd252-c681-400c-a258-64ad9285dbe9.png align="center")

These direct service integrations can leverage Velocity Template Langauge (VTL) or JavaScript resolvers. Additional to the function based resolvers, you can create pipeline resolvers that can not only integrate one but multiple AWS Services.

## Where to configure

These direct service configurations would make more sense when you are working with these Services from Infrastructure as Code (IaC) or Infrastructure from Code (IfC) approaches. Like the API Gateway example showcasing a `YAML` template, you can use AWS SAM where you can define the resources and then the integrations with resolver info.

Another simpler way to integrate services from AWS Step Functions is from the Workflow Studio on AWS Console, where you can define the task along with SDK integration by defining the parameters and results.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1676133110258/8821b068-880a-44ad-bd63-8b20ba877456.png align="center")

This generates a JSON structure for the State Machine which could be imported to your IaC project.

Similar to Workflow Studio, you can design Serverless apps that can generate SAM Template from AWS Application Composer. This currently supports EventBridge integrations with different Services.

## Sometimes you may need more than a configuration

Before wrapping up, yes! Sometimes you may need more than a configuration where you need to define your Lambda function or glue codes that process the data from these Services in an efficient way. The above AWS Services support direct integration with different AWS Services, there are some of which like Amazon SNS and Amazon SQS which could use configurations to trigger a Lambda function.