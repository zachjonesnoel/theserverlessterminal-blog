---
title: "Why AWS Step Functions and SDK integrations"
seoTitle: "Why AWS Step Functions and SDK integrations"
seoDescription: "Learn how AWS Step Functions and SDK integrations can help you architect better Serverless applications with over 200 integrations"
datePublished: Sun Oct 23 2022 17:49:06 GMT+0000 (Coordinated Universal Time)
cuid: cldkcnheu01213cnva3354i3x
slug: why-aws-step-functions-and-sdk-integrations
canonical: https://dev.to/aws-builders/why-aws-step-functions-and-sdk-integrations-4eeh
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675176187532/68da57bd-0fc1-4cba-8051-ab50b43b86ee.jpeg
tags: aws, serverless, stepfunction, aws-step-functions

---

[AWS Step Functions](https://aws.amazon.com/step-functions/) helps you orchestrate your Serverless workflows and AWS Step Functions is expanding the way you integrate with multiple AWS Services with SDK integrations.

In this blog, we will look at how AWS Step functions and SDK integrations can help you architect better your Serverless applications.

### Why AWS Step Functions?

> This is the most common question I hear!

![Let's answer that!](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176165850/8a86574b-95c1-4a43-9931-4194a9be18ec.gif align="center")

#### Support of flow components

AWS Step Functions allows you to define your workflows with different components -

* Task - Single unit of work which can integrate with different AWS Services
    
* Choice - Conditional branching of flows
    
* Parallel - When you have to simultaneously have to execute different tasks
    
* Wait - Wait until *x* seconds
    
* Fail - Stop the execution with a failure
    
* Succeed - Stop the execution successfully
    
* Pass - For pass through to the next state
    
* Map - When working with loops
    

#### Integrates with different AWS Services

AWS Step Functions integrates with over 200+ AWS Services API Actions enabling you to build workflows which can in reality communicate with multiple combination of AWS Services either with SDK integration or with other Serverless services with HTTP optimised integration.

#### Workflow definitions

The workflows (state machines) are well defined in a JSON format called [Amazon States Language (ASL)](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html). ASL define the workflow using [different components](#flow-components) and the possible intrinsic functions.

```JSON
{
    "Comment": "State machine to recognize celebrities and write into DynamoDB",
    "StartAt": "RecognizeCelebrities",
    "States": {
      "RecognizeCelebrities": {
        "Type": "Task",
        "Parameters": {
          "Image": {
            "S3Object": {
              "Bucket.$": "$.detail.bucket.name",
              "Name.$": "$.detail.object.key"
            }
          }
        },
        "Resource": "arn:aws:states:::aws-sdk:rekognition:recognizeCelebrities",
        "Next": "ProcessEachCeleb",
        "ResultPath": "$.response.celebrities"
      },
      "ProcessEachCeleb": {
        "Type": "Map",
        "Iterator": {
          "StartAt": "DynamoDB PutItem",
          "States": {
            "DynamoDB PutItem": {
              "Type": "Task",
              "Resource": "arn:aws:states:::aws-sdk:dynamodb:putItem",
              "Parameters": {
                "TableName": "${DDBTable}",
                "Item": {
                  "pk": {
                    "S.$": "States.UUID()"
                  },
                  "sk": {
                    "S.$": "$.DocumentName"
                  },
                  "CelebrityName": {
                    "S.$": "$.CelebJSON.Name"
                  },
                  "MatchConfidence": {
                    "N.$": "States.Format('{}',$.CelebJSON.MatchConfidence)"
                  },
                  "Urls": {
                    "S.$": "States.ArrayGetItem($.CelebJSON.Urls,0)"
                  }
                }
              },
              "End": true
            }
          }
        },
        "ItemsPath": "$.response.celebrities.CelebrityFaces",
        "Parameters": {
          "CelebIndex.$": "$$.Map.Item.Index",
          "CelebJSON.$": "$$.Map.Item.Value",
          "DocumentName.$": "$.detail.object.key"
        },
        "ResultPath": "$.response.items",
        "End": true
      }
    }
  }
```

#### Serverless orchestration of workflows

When you want to build a workflow which has series of tasks that uses [different components](#flow-components) with the Amazon States Language (ASL) defining the state machine with different integrations to several AWS services.

#### Workflow Studio

AWS Step Functions provides [Workflow Studio](https://docs.aws.amazon.com/step-functions/latest/dg/workflow-studio.html) which is a low-code visual workflow designer. This is available in your AWS Console where you can create Standard or Express workflows with different combinations of tasks. With Workflow Studio, you can auto-generate the ASL as a JSON file which can be exported for your developing with different IaC practices.

#### API actions with sync and async

When [integrating with different AWS Services](#sf-integrations), some of the services' API actions expects you to just start the execution or `await` for response to be returned, in such scenarios, AWS Step Functions supports both the ways of invocations as part of the API actions integrations.

#### Intrinsic functions

[Intrinsic functions](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html) are functions available in Amazon States Language (ASL) which are focused on making integrations with different *Tasks* easier with pre-processing and post-processing the data.

Here is another blog-post which talks about how Intrinsic functions are helpful. {% embed https://dev.to/awscommunity-asean/intrinsic-functions-to-level-up-your-step-functions-2k84 %}

#### Pricing model for Step Functions

The pricing model for AWS Step Functions is based on each state machine execution which includes state transitions for Standard or Express workflows.

#####To summarise

![Benefits of AWS Step Functions](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176168769/3db6907d-f4c4-47c4-b777-8db78929fb4f.png align="left")

### Service integration patterns

When integrating with different AWS Services, we would have to integrate with one of the patterns -

* [Request / Response](#request-response)
    
* [Run a job (sync)](#sync)
    
* [Callback](#callback)
    

#### Request / Response

AWS Services API action which process the *request* and waits to return the *response* from the HTTP request.

![Request / Response example](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176171471/496f4edf-1901-4797-b6fe-5c7c82aaec40.png align="left")

In the above example, we are using DynamoDB's *Query* API action with SDK integration, the state machine will expect the response from *Query* API action.

#### Run a job (sync)

When working with batch tasks or synchronous tasks you would have to wait for a longer period of time until this is completed. In such scenarios, you could choose to *wait for the task to complete* which will pause the state machine execution until the task is completed. Once it is complete, the state machine execution resumes.

![Run a job (sync) example](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176173975/2fc9321a-1ba3-4c4a-b2d9-88c8a4f47cdf.png align="left")

The resource for sync based integrations are suffixed with `.sync`, for example when running an ECS task -

```plaintext
"Resource": "arn:aws:states:::aws-sdk:ecs:runTask.sync",
```

#### Callback

Callbacks work with a *task token* that has to be returned, until that *task token* is returned, state machine execution is paused. Whenever working with callbacks, the resource is suffixed with `.waitForTaskToken`.

![Callback example](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176175619/59711fff-40da-4c1f-b29c-7c804593711e.png align="left")

In the above example, the state machine defines that AWS Lambda functions is invoked with a *callback* integration pattern and it expects a *task token* returned from Lambda function to continue with the state machine execution.

### SDK Integrations

[AWS Step Functions](https://aws.amazon.com/step-functions/) announced the support of [SDK integrations with AWS Services' API actions](https://aws.amazon.com/about-aws/whats-new/2021/09/aws-step-functions-200-aws-sdk-integration/) last year with over 200+ services and it's been growing the list of service API actions supported.

![SDK integrations](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176178044/92a6b9e3-1fc2-4374-a5ad-7ee36f38a8b7.png align="left")

#### Single source of workflow

With Serverless architecture, you would be working with multiple Serverless services these would traditionally distributed over multiple Lambda functions. With AWS Step Functions, you can have the complete workflow in a single [Amazon States Language (ASL)](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html).

#### Direct service integrations

SDK integrations enable you to directly integrate with different AWS Services with the supported API actions. This helps developers to work with direct service integration with the API action's payload of both request and response.

#### Error handling

AWS Step Functions provides a good way to handle errors with *retry-catch* definitions on your *tast* state where you can anticipate the errors would come in.

```JSON
"Lambda Invoke": {
      "Type": "Task",
      "Resource": "arn:aws:states:::aws-sdk:lambda:invoke.waitForTaskToken",
      "OutputPath": "$.Payload",
      "Parameters": {
        "Payload.$": "$"
      },
      "Retry": [
        {
          "ErrorEquals": [
            "Lambda.ServiceException",
            "Lambda.AWSLambdaException",
            "Lambda.SdkClientException"
          ],
          "IntervalSeconds": 2,
          "MaxAttempts": 6,
          "BackoffRate": 2
        }
      ],
      "Next": "ECS RunTask",
      "Catch": [
        {
          "ErrorEquals": [
            "States.ResultPathMatchFailure",
            "States.Timeout",
            "States.BranchFailed"
          ],
          "Next": "Lambda Invoke"
        }
      ]
    },
```

#### Lambda-less

AWS Step functions being a low-code Serverless workflow orchestrator, you can define multiple AWS Services direct integrations which can reduce the usage of Lambda functions for working with several AWS Services.

#### Cost effective

With the reduction of Lambda functions in your architecture as Step Function tasks are directly integrated with the AWS Service using SDK based API actions, the cost of running your Serverless workflow would reduce. Given that based on the use-case you could even choose between *Standard* or *Express* workflows which helps in cost optimisation.

### AWS Step Functions and SDK integrations in action

When you have a workflow which needs integration with [AWS Rekognition](https://aws.amazon.com/rekognition/), [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) and the additional business logic on AWS Lambda functions, the architecture of using them in your Serverless application would be with AWS Lambda functions as seen in the below diagram.

![Workflow without Step functions](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176179712/e68d5ca8-00c7-432b-8988-fdba718c4451.png align="left")

When converting to an architecture using AWS Step Functions would mean that you can leverage the workflow based sequential flow. Again you would be using Lambda functions with the Lambda functions would in-turn invoke the respect AWS Service API action with SDKs.

![Workflow with Lambda functions](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176181750/caa2e9ca-ec0f-4d6c-9591-9d039ed12fdd.png align="left")

To step-up with the usage of SDK integrations on Step Functions and intrinsic functions, you can eliminate Lambda functions from your architecture and maintain your Serverless workflow completely on Step Functions.

![With Step Functions and SDK integration](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176183074/7f1307ae-5be7-4e02-8543-624c7220be58.png align="left")

When the workflow is executed, you can view the complete results on AWS Console which is available for both Standard and now available for Express workflows also.

![Execution details of the workflow](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176184448/e60a0168-aa35-4c64-8aa5-353a3c0ba77c.gif align="left")

This is based on the session presented at AWS APAC Community Summit 2022, you can also view the [slide deck](https://speakerdeck.com/zachjonesnoel/build-better-with-step-functions-and-sdk-integrations).

The GitHub repository with SAM template is available for the above example. {% github https://github.com/zachjonesnoel/celebgram %}

This series holds a lot of other Serverless workflows powered with Step Functions and SDK Integrations - https://dev.to/zachjonesnoel/series/16484