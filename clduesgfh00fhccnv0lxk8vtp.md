---
title: "AWS Amplify and Front-end Development"
seoTitle: "AWS Amplify and Front-end Development"
seoDescription: "AWS Amplify simplifies front-end dev with low-code or no-code libraries for Auth, Storage, API, and more. Easy setup with CLI"
datePublished: Sat Jun 12 2021 16:43:52 GMT+0000 (Coordinated Universal Time)
cuid: clduesgfh00fhccnv0lxk8vtp
slug: aws-amplify-and-front-end-development
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675784440771/f7dffeb5-885b-45d1-a2f5-5d09b16a16fa.jpeg
tags: aws, frontend-development, serverless, awsamplify

---

AWS Amplify facilitates with a ton of services which makes the front end developers to integrate with AWS Serverless backend much more seemless and simple. The concept of "LOW-CODE or NO-CODE" is what drives AWS Amplify to be well adapted. Amplify provides libraries for different services such as - *Authentication, Storage, API, DataStore, Analytics, AI/ML, PubSub* to be readily available to the front-end developers.

#### Amplify setup

Amplify can be setup with on the local machines with AWS Amplify CLI. And the [installation guide](https://docs.amplify.aws/cli/start/install) will give a walk-through of installation and setup of Amplify CLI. Once you have the CLI installed, you can setup the user on AWS with the *amplify configure* command.

```javascript
$ amplify configure
Initializing new Amplify CLI version...
Done initializing new version.
Scanning for plugins...
Plugin scan successful
Follow these steps to set up access to your AWS account:

Sign in to your AWS administrator account:
https://console.aws.amazon.com/
Press Enter to continue

Specify the AWS Region
? region:  us-east-1
Specify the username of the new IAM user:
? user name:  jones
Complete the user creation using the AWS console
https://console.aws.amazon.com/iam/home?region=********************************policies&policies=arn:aws:iam::aws:policy%2FAdministratorAccess
Press Enter to continue

Enter the access key of the newly created user:
? accessKeyId:  ********************
? secretAccessKey:  ********************
This would update/create the AWS Profile in your local machine
? Profile Name:  JonesPersonal

Successfully set up the new user.
```

The user which gets created is granted with Administrator access and this credential could be used with another project on the same AWS Account.

#### Initializing your Amplify project

Once you have the Amplify setup with your credentials, you can initialize the project with *amplify init* and it prompts an interactive questionnaire to setup your project on cloud.

```javascript
$ amplify init
Note: It is recommended to run this command from the root of your app directory
? Enter a name for the project s3albumvue
The following configuration will be applied:

Project information
| Name: s3albumvue
| Environment: dev
| Default editor: Visual Studio Code
| App type: javascript
| Javascript framework: vue
| Source Directory Path: src
| Distribution Directory Path: dist
| Build Command: npm run-script build
| Start Command: npm run-script serve

? Initialize the project with the above configuration? Yes
Using default provider  awscloudformation
? Select the authentication method you want to use: AWS profile

For more information on AWS Profiles, see:
https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html

? Please choose the profile you want to use JonesPersonal
Adding backend environment dev to AWS Amplify Console app: ***********
⠴ Initializing project in the cloud...

CREATE_COMPLETE AuthRole   AWS::IAM::Role Sat Jun 12 2021 07:53:45 GMT+0000 (Coordinated Universal Time) 
CREATE_COMPLETE UnauthRole AWS::IAM::Role Sat Jun 12 2021 07:53:46 GMT+0000 (Coordinated Universal Time) 
⠼ Initializing project in the cloud...

CREATE_COMPLETE DeploymentBucket             AWS::S3::Bucket            Sat Jun 12 2021 07:53:55 GMT+0000 (Coordinated Universal Time) 
CREATE_COMPLETE amplify-s3albumvue-dev-75327 AWS::CloudFormation::Stack Sat Jun 12 2021 07:53:57 GMT+0000 (Coordinated Universal Time) 
✔ Successfully created initial AWS cloud resources for deployments.
✔ Initialized provider successfully.
Initialized your environment successfully.

Your project has been successfully initialized and connected to the cloud!
```

Once the initialization is done, you are good to start adding different AWS components to your front-end application.

Let's install the dependent packages for our project.

```json
npm install -g @aws-amplify/cli
```

For VueJS

```json
npm install aws-amplify @aws-amplify/ui-vue
```

For ReactJS

```json
npm install aws-amplify @aws-amplify/ui-react
```

#### Authentication with Amplify

{% link https://dev.to/zachjonesnoel/modern-apps-going-cognito-1o77 %} In my earlier blog, [Modern apps going Cognito](https://dev.to/zachjonesnoel/modern-apps-going-cognito-1o77) I mentioned how Cognito is seamlessly integrated with Amplify and how the low code, ready to use components addition can be easily added to your front-end application. Now let's get that configuration done! Adding authentication to your amplify project is achieved with the command *amplify add auth* and Amplify CLI gives you an interactive questionnaire and creates a Cognito User Pool with the configurations.

```javascript
$ amplify add auth
Using service: Cognito, provided by: awscloudformation
 
 The current configured provider is Amazon Cognito. 
 
 Do you want to use the default authentication and security configuration? Default configuration
 Warning: you will not be able to edit these selections. 
 How do you want users to be able to sign in? Email
 Do you want to configure advanced settings? No, I am done.
Successfully added auth resource s3albumvue229f70f3 locally
```

Once the Cognito User Pool is provisioned locally, we can push it to the cloud with *amplify push*.

![Amplify add auth pushed to cloud](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784433758/c86c1662-9960-46d7-b256-3adf9b66bcda.png align="left")

You would have to import the packages and also programmatically configure the AWS resources used in the project.

*main.js*

```javascript
import Vue from 'vue'
import App from './App.vue'
/* ---Amplify to be imported--- */
import Amplify from 'aws-amplify';
import awsconfig from './aws-exports';
import "@aws-amplify/ui-vue";
Amplify.configure(awsconfig);
/* ---Amplify to be imported--- */
Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')
```

The template for the Vue Authentication component in your application's login component or the App file.

*App.vue*

```javascript
<template>
  <div id="app">
    <amplify-authenticator>
      <amplify-sign-out></amplify-sign-out>
      <!-- Rest of the application components -->
    </amplify-authenticator>
  </div>
</template>
```

*amplify-authenticator* VueJS's UI component is the component provided by AWS Amplify which does sign-in, sign-up, forgot password, confirm user, resend confirmation code workflows with Cognito. And the UI is also customizable to the UI flavours your application needs.

VueJS Amplify UI component for sign-out was implemented with Auth API.

```javascript
async signOut() {
    try {
        await Auth.signOut();
    } catch (error) {
        console.log('error signing out: ', error);
}
```

*Demo of Authentication on Amplify*

![Authentication on Amplify](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784435259/c6fd3c39-ebe1-4611-8e65-90a5c0046ddd.gif align="left")

#### Storage on Amplify

Storage on Amplify refers to the storage of data values and also storage of files. Amplify's Storage library uses -

* DynamoDB - for the storage of data values, which is AWS managed NoSQL database offering.
    
* S3 - for the storage of files which is also AWS managed object storage, perfectly suited for files upto 5TB. This can be configured with *amplify add storage*, for this demo the project would use S3 based storage for files.
    

```javascript
$ amplify add storage
? Please select from one of the below mentioned services: Content (Images, audio, video, etc.)
? Please provide a friendly name for your resource that will be used to label this category in the project: ********
? Please provide bucket name: *******************
? Who should have access: Auth users only
? What kind of access do you want for Authenticated users? create/update, read, delete
? Do you want to add a Lambda Trigger for your S3 Bucket? No
Successfully added resource ******* locally
```

And this could be updated to the cloud with *amplify push*.

![Pushing storage to cloud](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784436731/118e2c6c-90e7-4db6-a4c7-8ec5e4bfbbee.png align="left")

When this is been pushed, it creates S3 bucket and also attaches a policy to the authenticated users which is done with a Lambda function which validates the policy to *Create, Update, Delete objects on S3*.

```html
<template>
    <amplify-s3-album />
</template>
```

*amplify-s3-album* VueJS component provides the ability to upload files to S3 bucket with Cogntio as the authentication.

*Demo of storage*

![Storage demo](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784438340/4d430ef3-7712-4b05-a72b-36fa7e04288d.gif align="left")

#### Hosting on Amplify

{% link https://dev.to/zachjonesnoel/amplify-your-web-hosting-3g4a %} In my earlier post, [Amplify your web hosting](https://dev.to/zachjonesnoel/amplify-your-web-hosting-3g4a) I had mentioned about how Amplify helps in hosting web-applications. Now with that background, we will be enabling hosting on this very project which is done with the command *amplify add hosting*. In this demo we will host it on S3 and also distribute it over CloudFront's CDN network.

```javascript
$ amplify add hosting
? Select the plugin module to execute Amazon CloudFront and S3
? Select the environment setup: PROD (S3 with CloudFront using HTTPS)
? hosting bucket name *************
Static webhosting is disabled for the hosting bucket when CloudFront Distribution is enabled.
```

This build the application and deploys on S3 bucket and also setups up a CloudFront distribution for us to access the application.

On *amplify publish*, this will create the needed AWS resources and also build the VueJS application and upload the dist folder to the selected destination (S3 bucket in this demo).

![Uploading build files](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784440035/4f269ebc-3d77-46a6-bb67-30e5f24731e9.png align="left")

#### Deployments and GitHub repositories

VueJS application hosted on S3 bucket with CloudFront distribution : https://dnbxim1kfj83r.cloudfront.net/ {% github zachjonesnoel/s3-album-vue/ %}

ReactJS application hosted on S3 bucket with static website hosting : http://s3album-20210523092113-hostingbucket-dev.s3-website-us-east-1.amazonaws.com {% github zachjonesnoel/s3-album-reactjs/ %}

The difference with VueJS app and ReactJS app is the way it is hosted and ReactJS is fully development with UI components provided by Amplify whereas VueJS is using sign-out API instead of the Vue UI Component as the component had some dependent npm package issue.