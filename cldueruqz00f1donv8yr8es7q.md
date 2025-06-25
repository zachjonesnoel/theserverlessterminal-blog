---
title: "Understanding AWS Amplify"
seoTitle: "Understanding AWS Amplify"
seoDescription: "AWS Amplify simplifies AWS app development with commands and interactive CLI walk-throughs for front-end and back-end"
datePublished: Sun Jul 04 2021 17:51:14 GMT+0000 (Coordinated Universal Time)
cuid: cldueruqz00f1donv8yr8es7q
slug: understanding-aws-amplify
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675784412683/b6ca1a54-7ce8-4969-9e82-4ecb9f4ebf09.jpeg
tags: aws, serverless, aws-amplify, awsamplify

---

AWS Amplify is a tool built for AWS native application development - both front-end and back-end development. Amplify makes development process easier for developers where a set of commands and interactive walk-through via CLI.

#### What is AWS Amplify?
[AWS Amplify](https://aws.amazon.com/amplify/) supports modern, popular web and mobile applications where the developers can create and connect to serverless backend hosted on AWS. 

> AWS Amplify is a set of tools and services that can be used together or on their own, to help front-end web and mobile developers build scalable full stack applications, powered by AWS. With Amplify, you can configure app backends and connect your app in minutes, deploy static web apps in a few clicks, and easily manage app content outside the AWS console.

> Amplify supports popular web frameworks including JavaScript, React, Angular, Vue, Next.js, and mobile platforms including Android, iOS, React Native, Ionic, Flutter. Get to market faster with AWS Amplify.

#### Features of AWS Amplify 

![Features of AWS Amplify](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784409809/9fcbd731-0ab6-42cc-b40a-1be5799c0442.png)

Amplify broadly classifies the features into four categories - 
+ Develop
+ Deliver
+ Manage
+ Tools

##### Develop
The key aspects which are used in the development phase of the project. 

+ Authentication - User management and the workflows such as sign-up, sign-in, forgot password with a managed user pool (directory) on AWS or external identity providers. 
{% link https://dev.to/zachjonesnoel/modern-apps-going-cognito-1o77 %}
{% link https://dev.to/awscommunity-asean/message-customization-on-cognito-2bme %}
+ Storage - Storage of data which includes both app/user data stored on AWS managed NoSQL database (DynamoDB) and also media files on a object storage service (S3).
+ GraphQL API - AWS managed serverless offering for GraphQL interface for API integration.
{% link https://dev.to/awscommunity-asean/aws-appsync-101-53lj %}
+ REST API - AWS managed API Gateway for deploying your REST APIs for integration.
+ DataStore - The data which is stored on the client side for caching, offline storage and synchronization with server. 
+ Interactions - Chatbot offering by AWS leveraging AWS LEX for building and integrating Chat bots.
+ Push Notifications - Notifications on the app which is sent to targeted audience. 
+ Functions - Functions which hold the business logic of the application.
+ PubSub - For all the communications over MQTT for broadcast, IoT data streaming.

| AWS Amplify feature | AWS Service |
| --------- |:---------:|
| Authentication  | [Amazon Cognito](https://aws.amazon.com/cognito/) |
| Storage | [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) , [Amazon S3](https://aws.amazon.com/s3/) | 
| GraphQL API | [AWS AppSync](https://aws.amazon.com/appsync/) | 
| REST API | [Amazon API Gateway](https://aws.amazon.com/api-gateway/) | 
| DataStore | Used by [AWS AppSync](https://aws.amazon.com/appsync/) client | 
| Interactions | [AWS Lex](https://aws.amazon.com/lex/) | 
| Push Notifications | [Amazon Pinpoint](https://aws.amazon.com/pinpoint/) | 
| Functions | [AWS Lambda](https://aws.amazon.com/lambda/) | 
| PubSub | [AWS IoT](https://aws.amazon.com/iot/) | 
| Predictions | [Amazon Translate](https://aws.amazon.com/translate/), [Amazon Transcribe](https://aws.amazon.com/transcribe/), [Amazon Comprehend](https://aws.amazon.com/comprehend/), [Amazon Rekognition](https://aws.amazon.com/rekognition/), [Amazon Textract](https://aws.amazon.com/textract/) | 

##### Deliver
The deliverable needs for the modern day application is the need of hosting the web application, registering domains, CI/CD workflows for the better release process. 

| AWS Amplify feature | AWS Service |
| --------- |:---------:|
| Hosting | [Amazon S3](https://aws.amazon.com/s3/), [Amazon CloudFront](https://aws.amazon.com/cloudfront/) |
| CI/CD | [AWS Amplify](https://aws.amazon.com/amplify/) console |
| Monitoring | [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) |

##### Manage
In re:Invent 2020, AWS launched [Amplify Admin UI](https://docs.amplify.aws/console/adminui/intro) which eases the management process where the user-management and content management can be handled. Amplify also provides a [sandbox environment](https://sandbox.amplifyapp.com/) to get started.

##### Tools
AWS Amplify provides [Amplify CLI](https://docs.amplify.aws/cli) and also Amplify provides dedicated SDKs on different frameworks - [JavaScript SDK](https://docs.amplify.aws/start/q/integration/vue?sc_icampaign=js-start&sc_ichannel=docs-home), [ReactJS](https://docs.amplify.aws/start/q/integration/react?sc_icampaign=react-start&sc_ichannel=docs-home), [VueJS](https://docs.amplify.aws/start/q/integration/vue?sc_icampaign=vue-start&sc_ichannel=docs-home), [Android](https://docs.amplify.aws/start/q/integration/android?sc_icampaign=android-start&sc_ichannel=docs-home), [iOS](https://docs.amplify.aws/start/q/integration/ios?sc_icampaign=ios-start&sc_ichannel=docs-home), [Flutter](https://docs.amplify.aws/start/q/integration/flutter?sc_icampaign=flutter-start&sc_ichannel=docs-home)


#### Things to do with Amplify
+ Develop full-stack serverless applications.
{% link https://dev.to/awscommunity-asean/aws-amplify-and-front-end-development-5geg %}
+ Host web applications. 
{% link https://dev.to/zachjonesnoel/amplify-your-web-hosting-3g4a %}
+ Managed CI/CD pipelines.
![CI/CD pipeline](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784411362/cd82d404-bafb-40db-8c49-dfc235246e5d.png)
+ Team collaborations with environments.
+ Building custom AWS resources with [CloudFormation Custom Resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-custom-resources.html).
+ Developing serverless containerized applications with Docker and [AWS Fargate](https://aws.amazon.com/fargate/).
+ Developing custom plugins with Amplify CLI.
+ Mocking and testing the backend service on the local system before deploying or pushing to your AWS Account.

#### Conclusion
AWS Amplify is a full feature packed tool for your application need. This is not only a developer's pal but also management's pal. Amplify not only eases integration with AWS serverless but also allows management process simplified. 

