---
title: "Allow only what your Lambda code needs"
seoTitle: "Allow only what your Lambda code needs"
seoDescription: "Optimize AWS Lambda Functions' security with least-privilege permissions and dedicated IAM execution roles for each function"
datePublished: Sun Jul 31 2022 17:55:00 GMT+0000 (Coordinated Universal Time)
cuid: cldkcnsum01253cnv4oo14zbm
slug: allow-only-what-your-lambda-code-needs
canonical: https://dev.to/aws-builders/allow-only-what-your-lambda-code-needs-59f2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675176203050/8bf8553b-1e7f-4127-91b8-56bf00174c8c.jpeg
tags: aws, aws-lambda, permissions, iam, execution-role

---

While executing [AWS Lambda functions](https://aws.amazon.com/lambda/) would need IAM permissions to access either for *READ* or *WRITE* actions. For this you need to map an [IAM Role](https://aws.amazon.com/iam/features/manage-roles/) with an associated IAM Policy.

As a best practice, we would follow *least privilege permission* but often times when you are in the development phase, you are testing multiple different things and end up having too many permissions.

### Understanding your Lambda's permission

![Lambda permission tab](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176195971/ed7065f5-270b-4e92-8336-de7d204de063.png align="left")

From AWS Lambda functions console, you can view and managed the permissions needed for your Lambda function. This is available in *Permissions* tab under *Configuration* tab. This hyperlinks to the IAM Role where you can modify the different permissions.

![Lambda functions console - permission by resource](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176198521/81a1d69c-ac23-410e-b026-51a3ad0ffea5.png align="left")

AWS Lambda functions console gives resource based overview of permissions with a summary of actions and resources. This will give you an understanding that this Lambda function can create *Amazon CloudWatch log groups*.

![Lambda function console - Resource based policy](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176201029/ffcf31a7-a1a3-49c3-b30f-3276137983f0.png align="left")

The permissions tab provides visibility on what other sources can trigger this specific Lambda function. When the source of Lambda function trigger is SNS, you can view the resource policy document which allows `lambda:InvokeFunction` action with the `AWS:SourceArn` of the SNS topic.

### Why least-privilege permission

Some questions that I've got in the past are -

> You have already access controlled your IAM users, why another level of access control for Lambda functions?

As legit the question sounds, it's really a lack of understanding of the concept of *least-privilege* itself. Well, there are certain operations which are done manually or viewed on the console which is where IAM user level privilege comes into play. But when IAM roles are used for accessing different AWS Services or Resources from AWS Lambda Functions programmatically with AWS SDK or different sources for Lambda triggers or Lambda destinations. When this is done, IAM roles for that specific Lambda function come into action! In this scenario, if IAM user doesn't have permission to delete a specific *SNS topic*, and your Lambda function's IAM role has a generic permission of `Allow: sns:*`, then with *AWS SDK*, the IAM user can deploy a Lambda function code which can use `deleteTopic()` with the *topic ARN* and successfully delete the topic.

> Why not have a generic IAM role for all Lambda functions?

This does make your maintenance of different IAM roles easier but what happens is when you have dangerous code which is not intended for that Lambda function comes into picture like in the above scenario where `deleteTopic()` method should not be in you Lambda code but ends up there. In such scenarios, IAM execution role of your Lambda becomes a defensive layer! So each Lambda function with a dedicated IAM execution role will only allow what is needed for that Lambda function.

> Should I go with managed Policies?

AWS Managed Policies are a great way to begin with, while this does address most of the cases but sometimes you would need have your own IAM policy defined.

```plaintext
ManagedPolicyArns:
        - arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
        - arn:aws:iam::aws:policy/AmazonDynamoDBFullAccess
        - arn:aws:iam::aws:policy/CloudWatchFullAccess
```

For most of your use-case, you may not need *full access* to different AWS resources so choosing which managed policy works for is the decision on your part.

```plaintext
Policies:
        -
          PolicyName: StepFunctionsInvokeLambda
          PolicyDocument: 
            Version: "2012-10-17"
            Statement: 
              - 
                Effect: Allow
                Action: lambda:InvokeFunction
                Resource: !GetAtt Iterator.Arn
```

### Conclusion

Recently, I did get some queries about why securing your Lambda code with appropriate IAM permissions which motivated me to author this blog post. Following the best practices approach, it is always good to go with *least privileges* and dedicated IAM execution role for each of your Lambda functions.