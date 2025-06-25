---
title: "Lambda functions over URLs"
seoTitle: "Lambda functions over URLs"
seoDescription: "Learn about AWS Lambda's new feature: Lambda URLs! Invoke Lambda functions directly with an HTTP API"
datePublished: Sun Apr 10 2022 17:46:28 GMT+0000 (Coordinated Universal Time)
cuid: cldkhj0va023b3cnv84ww5qne
slug: lambda-functions-over-urls
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675184377908/42a42a8c-0c6e-416c-9473-1b1b278800de.jpeg
tags: functions, aws, serverless, aws-lambda

---

[AWS Lambda functions](https://aws.amazon.com/lambda/) recently announced the launch of Lambda URLs where you can invoke the Lambda function directly with an HTTP API. You can read the announcement [here](https://aws.amazon.com/about-aws/whats-new/2022/04/aws-lambda-function-urls-built-in-https-endpoints/).

### Key take-aways

* [Setting up Function URL when creating a new Lambda Function](#setup)
    
* [CORS Configuration](#cors)
    
* [How it works](#how-it-works)
    

### Setting up Function URL when creating a new Lambda Function

Whenever creating a new Lambda function from AWS console, under the advance section you can enable *Enable function URL - new*. This setup accepts other configuration parameters such as one of authentication type for the Function URL -

* AWS IAM
    
* NONE, making the Function URL publicly accessible.
    

And also you can specify if the Function URL should be Cross-origin resource sharing (CORS) so that Lambda function URL can be invoked from any domain. This auto-generates a policy which allows the invocation of AWS Lambda function for `AuthType : NONE`.

```YAML
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "StatementId": "FunctionURLAllowPublicAccess",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "lambda:InvokeFunctionUrl",
      "Resource": "arn:aws:lambda:us-east-1:xxxxx:function:xxxx",
      "Condition": {
        "StringEquals": {
          "lambda:FunctionUrlAuthType": "NONE"
        }
      }
    }
  ]
}
```

The process of setting up Function URLs from AWS Console.

![Creating Function URL for a new Lambda function from Console](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184361298/30ee0d6f-8353-433e-bc47-511b50b2218e.gif align="left")

The same with SAM CLI is supported where we can configure the `AuthType` accepting the values `AWS_IAM` or `NONE`, `Cors` and `AllowOrigins` to allow specific origins or all with `AllowOrigin : *`.

```YAML
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: >
  function-url-demo

  Sample SAM Template for function-url-demo
  
# More info about Globals: https://github.com/awslabs/serverless-application-model/blob/master/docs/globals.rst
Globals:
  Function:
    Timeout: 3

Resources:
  HelloWorldFunction:
    Type: AWS::Serverless::Function # More info about Function Resource: https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md#awsserverlessfunction
    Properties:
      CodeUri: hello-world/
      Handler: app.lambdaHandler
      Runtime: nodejs14.x
      Architectures:
        - x86_64
      FunctionUrlConfig:
        AuthType: NONE
        Cors:
          AllowOrigins:
            - "*"

Outputs:
  # ServerlessRestApi is an implicit API created out of Events key under Serverless::Function
  # Find out more about other implicit resources you can reference within SAM
  # https://github.com/awslabs/serverless-application-model/blob/master/docs/internals/generated_resources.rst#api
  HelloWorldFunctionArn:
    Description: "Hello World Lambda Function ARN"
    Value: !GetAtt HelloWorldFunction.Arn
  HelloWorldFunctionIamRole:
    Description: "Implicit IAM Role created for Hello World function"
    Value: !GetAtt HelloWorldFunctionRole.Arn
```

Once the Lambda function is created, the boiler plate code and the Function URL is available on the console.

![Boiler plate code with Function URL](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184366189/f3c0e983-fbf9-4c3d-b51b-9172041a15f9.png align="left")

The complete *Function URLs* configurations are available under the *Function URL* menu in *Configuration* tab of the Lambda function.

![Function URL configurations on AWS Console](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184368652/f9ff44ba-b7a0-4c37-a409-db069682732a.png align="left")

### CORS Configuration

With Function URLs, you can allow access to any or specific domain(s). Once you enable the CORS configuration for your Lambda function URL the below settings are available. These CORS headers with `AllowOrigin` property could be configured as an array list items.

![Configuring different origins](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184370799/8cc28d05-3e08-473e-9a81-cd8087badeb2.png align="left")

We can also configure that specific headers are allowed or exposed also as array list items.

![Configuring headers which can be allowed or exposed](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184372880/e69d3607-f965-4108-b650-d87a869721b5.png align="left")

Since the invocation of Lambda function is over HTTP endpoint, we can also ensure that only the needed HTTP method is allowed for invocation.

![Configuring HTTP methods which can invoke Lambda functions](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184374092/51ab6f6f-22fa-460f-ac87-52f9d6b361ed.png align="left")

The invocation allows storing cookies of the credentials and also allowing the max-ages for cached requests.

![Configuring max-age and credentials storage in cookies](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184375336/ead9cdd4-c88c-4269-830d-4f2cd592b7cc.png align="left")

### How it works!?

Once the Lambda function with function URL enabled generates a invocation URL which the format -

```json
https://<url-id>.lambda-url.<region>.on.aws
```

Where the `url-id` is a uniquely identified ID for your Lambda function for that specific region.

The *Function URL* could be invoked similar to how any other REST APIs are invoked with *CURL*, *Postman* or for `GET` it would work on web browser as well.

Whenever a request is done, the parameters from headers or body with different content-type are taken as inputs similar to how *Proxy based API Gateway invocation*.

Sample input from invoking the *Function URL* via Postman.

```JSON
{
    "version": "2.0",
    "routeKey": "$default",
    "rawPath": "/favicon.ico",
    "rawQueryString": "",
    "headers": {
        "sec-fetch-mode": "no-cors",
        "referer": "https://uhpwurwzubchzjsqxxxxxxxxxxx.lambda-url.us-east-1.on.aws/",
        "sec-fetch-site": "same-origin",
        "accept-language": "en-US,en;q=0.9",
        "x-forwarded-proto": "https",
        "x-forwarded-port": "443",
        "x-forwarded-for": "xx.xx.xxx.xxx",
        "accept": "image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8",
        "sec-gpc": "1",
        "x-amzn-trace-id": "Root=1-62531267-32fd08fb47a0f72211d92c63",
        "host": "uhpwurwzubchzjsqspwroode2a0bqnbx.lambda-url.us-east-1.on.aws",
        "accept-encoding": "gzip, deflate, br",
        "sec-fetch-dest": "image",
        "user-agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.79 Safari/537.36"
    },
    "requestContext": {
        "accountId": "anonymous",
        "apiId": "uhpwurwzubchzjsqxxxxxxxxxxx",
        "domainName": "uhpwurwzubchzjsqxxxxxxxxxxx.lambda-url.us-east-1.on.aws",
        "domainPrefix": "uhpwurwzubchzjsqxxxxxxxxxxx",
        "http": {
            "method": "GET",
            "path": "/favicon.ico",
            "protocol": "HTTP/1.1",
            "sourceIp": "xx.xx.xxx.xxx",
            "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.79 Safari/537.36"
        },
        "requestId": "f2fb1ec9-5347-491d-88ea-15f90750cc01",
        "routeKey": "$default",
        "stage": "$default",
        "time": "10/Apr/2022:17:22:47 +0000",
        "timeEpoch": 1649611367689
    },
    "isBase64Encoded": false
}
```

And the Postman request is also successful which returns the response.

![Postman request for Lambda Function URL](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184376654/07d82131-3186-4e0d-b0b6-2c3f031b3cf4.png align="left")

To understand more, you can refer to the [documentation](https://docs.aws.amazon.com/lambda/latest/dg/lambda-urls.html).

### Common use-cases

* Service to service integrations
    
* Webhooks
    
* Lambda function serving as a mono-lambda function
    

### Conclusion

AWS Lambda functions with Function URLs enables faster and direct invocation of Lambda function but this is not a replacement to AWS API Gateway as the functionalities in terms of *Firewall* features, *throttling* which are not supported natively on Lambda function URL.