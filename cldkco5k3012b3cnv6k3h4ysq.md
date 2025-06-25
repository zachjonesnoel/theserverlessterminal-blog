---
title: "Building Serverless with SAM"
seoTitle: "Building Serverless with SAM"
seoDescription: "Learn to build Serverless applications with AWS Serverless Application Model (SAM). Understand SAM templates and SAM CLI features"
datePublished: Sun Jul 10 2022 18:23:50 GMT+0000 (Coordinated Universal Time)
cuid: cldkco5k3012b3cnv6k3h4ysq
slug: building-serverless-with-sam
canonical: https://dev.to/aws-builders/building-serverless-with-sam-396o
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675176219048/f9407767-684b-43f1-bc55-75e4c0d366cc.jpeg
tags: aws, serverless, sam, application-development-services

---

[AWS Serverless Application Model (SAM)](https://aws.amazon.com/serverless/sam/) is a framework for developing and deploying Serverless applications on AWS.

### Key takeaways from the blog

* [Understanding SAM and SAM template](#understand-sam)
    
* [Different features of SAM CLI](#sam-cli)
    

### Understanding SAM

![Understanding SAM CLI](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176210429/a11cfa9b-172e-4e88-ad97-541a7ef2c62b.png align="left")

[AWS Serverless Application Model (SAM)](https://aws.amazon.com/serverless/sam/) is an open-source framework which consists of -

* [**SAM templates**](#sam-template)
    
* [**SAM CLI**](#sam-cli)
    

SAM helps developers by providing different bootstrapped starting points when creating a new SAM application.

![SAM templates for quick start](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176212775/75df127d-f9a4-47ed-896a-e8c74c65d8bc.png align="left")

SAM CLI supports various programming runtimes as a choice for developers to get started with. The recent update being the NodeJS 16.x.

![SAM supported runtimes](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176214244/043018fa-5285-4bd2-8941-0fc8a5098679.png align="left")

### **SAM templates**

This is an abstracted version of [AWS CloudFormation templates](https://aws.amazon.com/cloudformation/resources/templates/) which promotes Infrastructure as Code (IaC) practice for your serverless applications and uses [AWS CloudFormation](https://aws.amazon.com/cloudformation/) under-the-hood for all AWS resources provisioning. *SAM Transforms* the template into *AWS CloudFormation template* so that it could be later deployed by creating a new CloudFormation stack or updating the existing CloudFormation stack.

*SAM template* also makes it easier to defined different AWS Serverless services with pre-defined resource types -

* `AWS::Serverless::Function`
    
* `AWS::Serverless::LayerVersion`
    
* `AWS::Serverless::Api`
    
* `AWS::Serverless::HttpApi`
    
* `AWS::Serverless::SimpleTable` an equivalent of `AWS::DynamoDB::Table`
    
* `AWS::Serverless::Application`
    
* `AWS::Serverless::StateMachine`.
    

*SAM template* uses various pre-managed AWS IAM policies for authorization to various resources. One of the managed policies for DynamoDB CRUD operations.

![Sample SAM template](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176216505/c798b7e8-d172-4885-8096-8c6c05cb6606.png align="left")

The above sample SAM Template describes a AWS Lambda function and AWS API Gateway REST API with a deployment stage.

### **SAM CLI**

The CLI is the tooling which facilitates developers to get started with Serverless application development and helps developers to build, deploy, test, monitor and also package the artifacts for *production ready* deployments using CI/CD pipelines. The SAM CLI has different CLI commands such as -

* `sam init` - The init command is used to create a new SAM application, the CLI command helps developers with an interactive questionnaire based walkthrough to setup a SAM app with steps involving choosing from a template, choice of language and type of Lambda function deployment. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-init.html).
    
* `sam build` - The build CLI command is used to build your SAM workstation with the template and validating it against the right resources. This builds the artifacts in `.aws-sam/build` directory which can be used to deploy into an AWS Account. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-build.html).
    
* `sam deploy` - The deploy CLI command is used to deploy the built artifacts to an AWS Account. This under-the-hood uses AWS CloudFormation to either deploy the resources with a *CreateStack* operation or *UpdateStack* operation. The `sam deploy` has different flags `sam deploy --guided` or `sam deploy --changeset` for either walking through the deploy with interactive questionnaire or deploying the changeset alone. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-deploy.html).
    
* `sam local generate-event` - Whenever working with [AWS Lambda functions](https://aws.amazon.com/lambda/) locally, you would need to generate different events to test the AWS Lambda function when the Lambda fn is getting invoked from a specific event source such as - Amazon S3 operations, AWS API Gateway, AWS SNS, AWS SQS and many more. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-local-generate-event.html).
    
* `sam local invoke` - The local invoke command invokes the specific Lambda function with an event and exits. This helps in testing Lambda function logic against the simulated event. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-local-invoke.html).
    
* `sam package` - The package CLI command creates a zip file of the code and dependences and uploads to the designated AWS S3 build artifacts bucket. This is internally performed by `sam deploy`. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-package.html)
    
* `sam pipeline bootstrap` - SAM helps in a CLI interactive way to setup CI/CD pipelines with SAM Pipelines. This setups different staging environments and the also CLI generates the needed CI/CD workflow template in the project directory based on the CI/CD provider. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-pipeline-bootstrap.html)
    
* `sam sync` - The [SAM Accelerate](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/accelerate.html) feature is used for immediate code deploys to development AWS Account and also real-time monitoring. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-sync.html).
    
* `sam logs` - The logs command is used to fetch the CloudWatch logs generated by AWS Lambda functions. You can find the documentation, [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-logs.html).
    
* `sam delete` - If you want to delete your SAM application you can delete it with this command, which uses a CloudFormation *DeleteStack* operation under-the-hood.
    

### Wrapping-up

SAM helps building Serverless apps easily and for developers who are familiar with AWS CloudFormation, it's even more easier with the *SAM template*, even otherwise *SAM init* facilities you to get started with one of the templates and build on top of it and also expand it with different *SAM CLI* commands. You can refer to my session on Building Serverless Apps with IaC at AWS UG Colombo April meet-up where I demonstrate how we can use IaC with AWS SAM.

%\[https://youtu.be/4mAaPF4L-ec?t=961\]

You can also refer to my AWS Summit India 2022 session about [Building Serverless Apps with SAM Accelerate and SAM Pipelines](https://speakerdeck.com/zachjonesnoel/build-serverless-apps-with-sam-accelerate-and-sam-pipelines-aws-summit-india-2022) slide deck.