---
title: "Intrinsic functions to level-up your Step Functions"
seoTitle: "Intrinsic functions to level-up your Step Functions"
seoDescription: "Discover AWS Step Functions intrinsic functions for serverless workflows: manipulate data using arrays, JSON objects, math operations, and more"
datePublished: Sun Sep 04 2022 10:20:41 GMT+0000 (Coordinated Universal Time)
cuid: cldrixn3y01h1ghnvai887xko
slug: intrinsic-functions-to-level-up-your-step-functions
canonical: https://dev.to/awscommunity-asean/intrinsic-functions-to-level-up-your-step-functions-2k84
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675610002996/5f61e5de-eb81-4473-9b8f-6b61f166c0c8.jpeg
tags: aws, serverless, aws-step-functions

---

[AWS Step Functions](https://aws.amazon.com/step-functions/)  enables you to orchestrate serverless workflows by integrating with different AWS services. Those AWS services would need inputs in a specific format and also produces output in a specific format and to do that we have mapping templates / resolvers. 

### Intrinsic functions
When working with different resolvers on [Amazon States Language (ASL)](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html), you can have in-built methods called [intrinsic functions](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html). These *intrinsic functions* help with several operations in your mapping templates where you can process and manipulate with *arrays, JSON objects, strings, math operations* and more.

Previously, there were 4 intrinsic functions - 
+ `States.Array`
+ `States.Format`
+ `States.JsonToString`
+ `States.stringToJson`

Just a couple of days back, [AWS Step Functions](https://aws.amazon.com/step-functions/) added 14 new intrinsic functions, you can read the announcement [here](https://aws.amazon.com/about-aws/whats-new/2022/08/aws-step-functions-14-new-intrinsic-features-process-data-workflows/).
<a name="new-infn"></a>

| Type | Intrinsic function |
| ---- | ---- |
| [Array](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#asl-intrsc-func-arrays) | `States.ArrayPartition`, `States.ArrayContains`, `States.ArrayRange`, `States.ArrayGetItem`, `States.ArrayUnique` |
| [Data encoding and decoding](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#asl-intrsc-func-data-encode-decode) | `States.Base64Encode`, `States.Base64Decode` |
| [Hash calculation](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#asl-intrsc-func-hash-calc) | `States.Hash` |
| [JSON](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#asl-intrsc-func-json-manipulate) | `States.JsonMerge` |
| [Math](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#asl-intrsc-func-math-operation) | `States.MathRandom`, `States.MathAdd` |
| [String](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#asl-intrsc-func-string-operation) | `States.StringSplit` |
| [UID generator](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#asl-intrsc-func-uuid-generate) | `States.UUID`|

Now we have 18 intrinsic functions. 

### What it looked like without *intrinsic functions*
Without the intrinsic functions, the need for any such data processing or manipulation required you to write code in your [AWS Lambda functions](https://aws.amazon.com/lambda/) which involved having a dedicated *Task* in the State Machine where it would invoke your Lambda function. 
And your architectures would have excessive resources, your workflows mostly included AWS Step Functions which is invoking AWS Lambda functions for all data manipulations. 

![Too many things to do!](https://cdn.hashnode.com/res/hashnode/image/upload/v1675609998033/78c44886-de4b-45c3-9936-f86d45769c4d.gif)

When [SDK integrations](https://aws.amazon.com/about-aws/whats-new/2021/09/aws-step-functions-200-aws-sdk-integration/) was announced, we started thinking about workflows that could easily integrate with other AWS services. Many of us thought of migrating our workflows in AWS Lambda to AWS Step Functions but there were a few challenges - 
+ We could use it only for direct SDK integrations without data processing and we could not eliminate Lambda functions completely.
+ From the *sustainability* angle, we still had our Lambda code which was executing whenever our State Machine executes. 
+ The State Machines had additional *Tasks*, which affected the pricing as it's with every transition of state. 

### First set of *intrinsic functions*
Intrinsic functions helped us reduce some of the basic parsing use-cases executed in Lambda functions and were limited to -
+ *Format the message with variables*.
+ *Parsing String to JSON and vice versa*.
+ *Working with arrays either creating or retrieving the array*. 

### What's possible now with *intrinsic functions*
With the [14 new intrinsic functions](#new-infn) introduced, we have use-cases which are now possible within Step Functions without having additional *Tasks*. Some of the use-cases possible - 
+ *Generating UUIDs when you are creating new records in your DynamoDB or messages which are posted to SQS queues or SNS topics.*
+ *Able to hash string with different hashing algorithms - `MD5`, `SHA-256` and more. Or even encoding and decoding string to `Base64`*
+ *Working with different array based manipulations, where you may have to get a specific item or a unique item in the array or partitioning your arrays.*
+ *Easier way to split string into an array.*
+ *Generating random numbers.*

![Can't wait to try them up!](https://cdn.hashnode.com/res/hashnode/image/upload/v1675610001139/f62fa690-2421-4c90-befb-5e2e0175c57c.gif)

But when using intrinsic functions, you would have to consider the [reserved characters](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#amazon-states-language-intrinsic-functions-escapes) which requires you to escape the characters with a backslash (`\`). And the [fields which support](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html#amazon-states-language-intrinsic-functions-states) them which gives you a better understanding of which state what can be performed. 