---
title: "AWS Lambda powered up by AWS Graviton2"
datePublished: Sun Oct 03 2021 14:57:44 GMT+0000 (Coordinated Universal Time)
cuid: cldueoajk00eeccnvfcz7bfyc
slug: aws-lambda-powered-up-by-aws-graviton2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675784246350/963e3962-0daf-4c4e-a485-fce4fc8043dc.jpeg

---

[AWS Lambda](https://aws.amazon.com/lambda/) recently announced the launch of [AWS Graviton2 processor](https://aws.amazon.com/ec2/graviton/) (arm64 architecture) for Lambda functions which would not only make your Lambda function to execute faster but also optimizes the cost for execution. Graviton2 is an AWS built and owned processor.

You can read more about it from the [announcement blog](https://aws.amazon.com/blogs/aws/aws-lambda-functions-powered-by-aws-graviton2-processor-run-your-functions-on-arm-and-get-up-to-34-better-price-performance/).
{% twitter 1443337084786331652 %}

Lambda functions which are executing with Amazon Linux 2 runtime, now supports two architectures -
+ arm64 - 64-bit ARM architecture, for the AWS Graviton2 processor.
+ x86_64 – 64-bit x86 architecture, for x86-based processors.

All the existing Lambda functions would be running on **x86_64 architecture** and this architecture is the default architecture for any new Lambda function. 

#### Key takeaways from the blog
+ [Graviton2 (arm64) for Lambda functions.](#graviton2)
+ [Setting up Graviton2 (arm64) powered Lambda functions.](#setting-up-arm64)
+ [Performance with arm64 vs x86_64 architecture.](#arm64-vs-x86_64)

#### Graviton2 (arm64) for Lambda functions <a name="graviton2"></a>
The Gaviton2 based arm64 architecture makes the computation faster which results in faster execution of the Lambda function and in turn the cost for Lambda is also optimized. 

> AWS Lambda functions running on Graviton2, using an Arm-based processor architecture designed by AWS, deliver up to 34% better price performance compared to functions running on x86 processors. 

The **arm64 architecture** for Lambda functions is currently available in selected AWS Regions - 
+ US East (N. Virginia) us-east-1
+ US East (Ohio) us-east-2
+ US West (Oregon) us-west-2
+ Asia Pacific (Mumbai) ap-south-1
+ Asia Pacific (Singapore) ap-southeast-1
+ Asia Pacific (Sydney) ap-southeast-2
+ Asia Pacific (Tokyo) ap-northeast-1
+ EU (Frankfurt) eu-central-1
+ EU (Ireland) eu-west-1
+ EU (London) eu-west-2

The supported runtimes based on Amazon Linux 2 are -
+ NodeJS 12.x and 14.x
+ Python 3.8 and 3.9
+ Java 8 (`java8.al2`) and 11
+ .NET Core 3.1
+ Ruby 2.7
+ Custom runtime (`provided.al2`)

Graviton2 processor is well suited for Lambda functions which are processing heavy high-performance computations, video encoding, simulation workloads.

#### Setting up Graviton2 (arm64) powered Lambda function <a name="setting-up-arm64"></a>
When creating a new Lambda function from the web-console, you can select one of the architecture options - **x86_84** (default) or **arm64** (Graviton2 Processor).
![New Lambda Function](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784191649/2e0ae763-cd9c-4543-b31a-e9c5c2542390.png)
If you want to change the architecture for the existing Lambda function, navigate to the *Runtime settings*.
![Runtime settings](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784193796/2b938662-f275-413d-bc2a-96f2feac4e7c.png)
Click on *Edit* to change the settings.
![Edit runtime settings](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784195109/8a4c0394-84ff-4abb-89fc-f07e6946cfd4.png)

If you are trying to change an existing Lambda function from x86_64 to arm64 architecture, ensure to check the [function code compatibility with arm64 architecture](https://docs.aws.amazon.com/lambda/latest/dg/foundation-arch.html#foundation-arch-considerations) and also [suggested migration steps](https://docs.aws.amazon.com/lambda/latest/dg/foundation-arch.html#foundation-arch-steps).

Creating and updating Lambda functions with [AWS SAM](https://aws.amazon.com/serverless/sam/) and [AWS CDK](https://aws.amazon.com/cdk/) will soon support both the architectures.

Running Lambda functions with arm64 architecture on x86_64 machine
```bash
docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```

#### Performance with arm64 vs x86_64 architecture <a name="arm64-vs-x86_64"></a>
For determining the performance I would be reusing the DynamoDB operation performance SAM template from my previous blog post.  
{% link https://dev.to/awscommunity-asean/dynamodb-operations-scan-vs-query-with-cloudwatch-custom-metrics-2mik %} 
With some modifications - 
+ Change the number of `StepFunctionIndex` to 10.
+ Added `LambdaMemory` and `LambdaProcessor` parameters to the CloudWatch Custom Metrics. `LambdaMemory` is taken from Lambda's environment variable and `LambdaProcessor` is a value set explicitly as environment variable. 
```JavaScript
await cloudwatch.putMetricData({
        'MetricData': [{
            'MetricName': 'LambdaProcessorMeteric',
            'Dimensions': [{
                    'Name': 'OPERATION',
                    'Value': metric
                },
                {
                    'Name': 'LambdaMemory',
                    'Value': process.env.AWS_LAMBDA_FUNCTION_MEMORY_SIZE
                },
                {
                    'Name': 'LambdaProcessor',
                    'Value': process.env.lambda_fn_arch
                }
            ],
            'Unit': 'Milliseconds',
            'Value': value
        }, ],
        'Namespace': 'LambdaProcessMetrics'
    }).promise()
```
<!--
StepFunction invocation 1 : x86_64 architecture with 128MB Memory.
Average ![StepFunction invocation 4](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784197434/21c4ff31-0d6c-4c7b-8e09-15c7e5cafea1.png)
Minimum ![Minimum](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784198566/7a86033c-3344-4d23-97e3-bac219b2cbfa.png) 
Maximum ![Maximum](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784200781/c09cf5c1-535e-4423-b711-5dcd57b5edf4.png)

StepFunction invocation 2 : x86_64 architecture with 256MB Memory.
Average ![StepFunction invocation 4](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784202897/09ebeae5-0d4f-43cf-b023-deed0fc6f54a.png) 
Minimum ![Minimum](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784204054/3be50e55-de93-4b95-a229-cac751c89ebd.png) 
Maximum ![Maximum](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784205187/df71968f-5eb5-4c81-88b8-8cb629ce5c7a.png)

StepFunction invocation 3 : arm64 architecture with 128MB Memory.
Average ![StepFunction invocation 4](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784206351/f8923c8b-742f-4a4d-b1f1-382c9bce297a.png)
Minimum ![Minimum](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784208401/8c5071b1-cef7-414f-9a71-771400da3626.png)
Maximum ![Maximum](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784209878/0db435e2-a41b-43ad-b95a-7f23409c7a5c.png)

StepFunction invocation 4 : arm64 architecture with 256MB Memory.
Average ![StepFunction invocation 4](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784211234/5f3d25e8-34a7-4dd4-83fe-f62c9b4778cb.png)
Minimum ![Minimum](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784213233/e120ff44-9125-4dd3-86f7-00ed031981ef.png)
Maximum ![Maximum](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784215222/ccfc37d3-442e-4267-93d3-266d8648e627.png)
-->

arm64 architecture duration of several Lambda function invocations
![Duration](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784217506/f8801eeb-7dd6-4cbc-8fba-065893d35c4a.png)
arm64 architecture average duration of several Lambda function invocations
![Average duration](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784219478/bde58df6-a57c-4d48-a1f4-c42285f9e43d.png)
Duration graph
![Duration graph](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784221425/5b8048f0-a72c-458f-a8aa-030cfa8878c3.png)
 
x86_64 architecture duration of several Lambda function invocations
![Duration](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784222612/4cdbdb35-19c2-4bf8-97ac-cabb6e57a7bf.png)
x86_64 architecture average duration of several Lambda function invocations
![Average duration](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784224606/06ff5e19-8711-4489-a923-f9c148c526b0.png)
Duration graph
<!--![Duration graph](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784225822/1acd3767-9cac-4211-9ee9-282e298654cd.png)-->
![Duration graph](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784228194/e03c0fae-7170-4982-acdc-bfb3a30c595d.png)
 
All `query` operations with x86_64 and arm64 with 128MB and 256MB Memory
Average ![All query operations with x86_64 and arm64 with 128MB and 256MB Memory](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784229584/a2ed9032-7727-4c94-9b9c-2100395d4a7d.png)  
Minimum ![All query operations with x86_64 and arm64 with 128MB and 256MB Memory](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784230867/6f1fb19e-9bbf-49e7-b346-2d24303e311b.png)
Maximum ![All query operations with x86_64 and arm64 with 128MB and 256MB Memory](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784232978/a0c07d02-9d42-428e-8662-3e659f0c1cd6.png) 

All `scan` operations with x86_64 and arm64 with 128MB and 256MB Memory
Average ![All scan operations with x86_64 and arm64 with 128MB and 256MB Memory](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784234470/09e1536b-51e8-4914-9912-c6c29846ed30.png)
Minimum ![All scan operations with x86_64 and arm64 with 128MB and 256MB Memory](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784235929/c63b0b32-4462-4c3a-968d-40a0fbcad543.png)
Maximum ![All scan operations with x86_64 and arm64 with 128MB and 256MB Memory](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784237948/7b711d4b-9800-4fd9-92cf-ba6390f88294.png) 

All operations 
Average ![All operations](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784240178/cc302423-45c2-4d55-911c-4ca7d24bf222.png)
Minimum ![All operations](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784241736/0e604612-46fc-4785-82c2-4c5405bbf429.png) 
Maximum ![All operations](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784242926/615f08d9-3e37-44a8-8895-77895d2bed20.png)

#### Pricing
Lambda function with **arm64 architecture** is cheaper than the **x86_64 architecture**. 
>And also both the architectures are included under the 1M free requests per month and 400,000 GB-seconds of compute time per month, usable for functions powered by both x86, and Graviton2 processors, in aggregate.

![image](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784245028/c2ab45c3-50f5-4a3e-b85d-8dacfa9d5877.png)
You can know more about [Lambda pricing with different architectures](https://aws.amazon.com/lambda/pricing/).
For this demo, the complete execution cycle is part of the **Free Tier limit** inclusive of all StepFunction executions and Lambda function executions.

#### Conclusion
The operational and execution time with **x86_64** and **arm** has significant difference, the DynamoDB operations `Scan` and `Query` have different variations of execution time but the Lambda function as a whole, the time of duration is dropped which in-turn affects the billed execution time thus making the cost of Lambda function of **arm64** significantly lower than **x86_64**.
