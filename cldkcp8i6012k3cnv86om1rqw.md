---
title: "Step Functions to check if you have public S3 buckets"
seoTitle: "Step Functions to check if you have public S3 buckets"
seoDescription: "Use Step Functions to check for public S3 buckets in AWS. Ensure your buckets are not publicly accessible for security purposes"
datePublished: Fri Mar 11 2022 06:08:43 GMT+0000 (Coordinated Universal Time)
cuid: cldkcp8i6012k3cnv86om1rqw
slug: step-functions-to-check-if-you-have-public-s3-buckets
canonical: https://dev.to/awscommunity-asean/step-functions-to-check-if-you-have-public-s3-buckets-5dje
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675176269702/0cf25033-3e81-4171-9640-6edb1a2b0be9.jpeg
tags: aws, serverless, stepfunction, aws-step-functions

---

[Amazon S3](https://aws.amazon.com/s3/) provisions buckets which can be public or private, having public buckets has certain concerns as anybody off the internet can access your *S3 bucket* and the *objects* it holds. According to [security best practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html) mentioned by AWS, one of the best practice is to **ensure your S3 buckets are not publicly accessible**. And this can be achieved by a *Account*/*Organization* level setting and also during creation of buckets to *block public access by default*.

If by any chance, any *S3 bucket* is set with *public* access you could use different ways to identify and make it private. 
+ AWS Console
+ AWS CLI
+ AWS S3 SDK
+ AWS S3 APIs

### Understanding the workflow
In this blog-post, we will be using [Step Functions](https://aws.amazon.com/step-functions/) with [Amazon S3](https://aws.amazon.com/s3/) SDK integration of `ListBuckets`, `GetPublicAccessBlock` and `PutPublicAccessBlock`. And also [Simple Notification Service](https://aws.amazon.com/sns/) SDK `Publish` to notify which bucket policy had *public access*.
![State machine diagram](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176251932/367c57e0-15c6-41e6-a684-6893ad21a721.png)
#### ListBuckets state
We will first use `ListBuckets` SDK API to list all the buckets in that account. 
![Using ListBuckets state](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176253312/3208c253-4edf-4d8f-b488-7dcc3e67dfc0.png)
#### GetPublicAccessBlock state
And with the `Map` flow of Step Functions, we will loop through each of the bucket to `GetPublicAccessBlock`, this returns the set of parameters `BlockPublicAcls`, `BlockPublicPolicy`, `IgnorePublicAcls`, `RestrictPublicBuckets` which would be set to `false` if the bucket has *public access*.
![Using GetPublicAccessBlock state](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176254624/ab8eb20f-e1be-43a6-b6a8-be71c86b990e.png)
If there is an error which occurs, this step has a catch block enabled to pass though and continue with the next items.
#### Choice state
For checking this condition we will use `Choice` with the multiple *and* statements.
![Using Choice for the conditional branching](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176257075/70e249f7-7e01-4977-ab14-44c5e6c24f67.png)
If the condition does not satisfies, we will proceed to the *pass* state.
```JSON
{
     "And": [
         {
              "Variable": "$.res.PublicAccessBlockConfiguration.BlockPublicAcls",
                    "BooleanEquals": false
         },
         {
              "Variable": "$.res.PublicAccessBlockConfiguration.BlockPublicPolicy",
                    "BooleanEquals": false
         },
         {
              "Variable": "$.res.PublicAccessBlockConfiguration.IgnorePublicAcls",
                    "BooleanEquals": false
         },
         {
              "Variable": "$.res.PublicAccessBlockConfiguration.RestrictPublicBuckets",
                    "BooleanEquals": false
         }
     ],
     "Next": "SNS Publish"
}
```
#### SNS Publish state
If the conditions are satisfied, the *bucket* would be *publicly accessible*. The JSON payload would be published to a SNS topic to capture and process it accordingly.
![Using SNS Publish state](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176259382/a2c059d4-966a-45a5-9b6b-5ac4548ccca6.png)
#### PutPublicAccessBlock state
After notifying the SNS Topic, we would also be changing the bucket to *block all public access* with the `PutPublicAccessBlock ` SDK API.
![Using PutPublicAccessBlock state](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176261669/b218334c-b6f5-498e-ba9d-00fe32122426.png)

The complete state machine is as follows -
```JSON
{
  "Comment": "State machine to identify public buckets and make them private",
  "StartAt": "ListBuckets",
  "States": {
    "ListBuckets": {
      "Type": "Task",
      "Parameters": {},
      "Resource": "arn:aws:states:::aws-sdk:s3:listBuckets",
      "Next": "Map",
      "ResultPath": "$.s3ListBuckets"
    },
    "Map": {
      "Type": "Map",
      "End": true,
      "Iterator": {
        "StartAt": "GetPublicAccessBlock",
        "States": {
          "GetPublicAccessBlock": {
            "Type": "Task",
            "Parameters": {
              "Bucket.$": "$.s3JSON.Name"
            },
            "Resource": "arn:aws:states:::aws-sdk:s3:getPublicAccessBlock",
            "ResultPath": "$.res",
            "Catch": [
              {
                "ErrorEquals": [
                  "States.ALL"
                ],
                "Next": "Pass"
              }
            ],
            "Next": "Choice"
          },
          "Choice": {
            "Type": "Choice",
            "Choices": [
              {
                "And": [
                  {
                    "Variable": "$.res.PublicAccessBlockConfiguration.BlockPublicAcls",
                    "BooleanEquals": false
                  },
                  {
                    "Variable": "$.res.PublicAccessBlockConfiguration.BlockPublicPolicy",
                    "BooleanEquals": false
                  },
                  {
                    "Variable": "$.res.PublicAccessBlockConfiguration.IgnorePublicAcls",
                    "BooleanEquals": false
                  },
                  {
                    "Variable": "$.res.PublicAccessBlockConfiguration.RestrictPublicBuckets",
                    "BooleanEquals": false
                  }
                ],
                "Next": "SNS Publish"
              }
            ],
            "Default": "Pass"
          },
          "SNS Publish": {
            "Type": "Task",
            "Resource": "arn:aws:states:::aws-sdk:sns:publish",
            "Parameters": {
              "Message.$": "$",
              "TopicArn": "arn:aws:sns:us-east-1:228628157461:ErrorNotification"
            },
            "Next": "PutPublicAccessBlock",
            "ResultPath": "$.sns"
          },
          "PutPublicAccessBlock": {
            "Type": "Task",
            "End": true,
            "Parameters": {
              "Bucket.$": "$.s3JSON.Name",
              "PublicAccessBlockConfiguration": {
                "BlockPublicAcls": true,
                "BlockPublicPolicy": true,
                "IgnorePublicAcls": true,
                "RestrictPublicBuckets": true
              }
            },
            "Resource": "arn:aws:states:::aws-sdk:s3:putPublicAccessBlock"
          },
          "Pass": {
            "Type": "Pass",
            "End": true
          }
        }
      },
      "ItemsPath": "$.s3ListBuckets.Buckets",
      "Parameters": {
        "s3Index.$": "$$.Map.Item.Index",
        "s3JSON.$": "$$.Map.Item.Value"
      },
      "ResultPath": "$.s3Buckets"
    }
  }
}
```
### Workflow execution
The workflow execution works without any *specific input* to the state machine. For the execution, the state machine is running in *us-east-1* and the SDK works with buckets with the region *us-east-1*. 
As the state machine kicks off, all the buckets of the account is listed and each bucket is looped through to check for public access or not. If the bucket has public access, as per the flow SNS topic gets published and also the bucket is made *private* by *blocking all public access*.
![Looking at the execution of state machine](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176263056/caf511f5-4c7d-4b70-8cc4-0d446d9e761f.gif)
As part of the SNS topic, there is an email subscriber who gets notified as email JSON.
![Email subscriber to SNS Topic with email JSON](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176265363/ed0e6359-1cc2-40d0-8719-371b2cb8466e.png) 
Before the execution started, I had identified the *public bucket*.
![Publicly available bucket](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176266760/8f9f8dd4-54dc-4a7b-8551-8334db9b4d96.png)
After the execution of state machine, the same *publicly accessible* bucket is now *private*.
![Bucked changed to private](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176268174/b312af52-6d77-4532-b457-df9ad5d5f16e.png)
 
### Conclusion
Often times, managing and ensuring S3 buckets are *private* is a tedious task and with Step Functions and SDK integration with S3, we can ease the process. 

As we are nearing to 3-14 (March 14th), popularly know as **PI Day** it also marks S3's 16th birthday and there is a celebration happening [**AWS Pi Day 2022**](https://pages.awscloud.com/NAMER-field-OE-Pi-Day-2022-reg-event.html).