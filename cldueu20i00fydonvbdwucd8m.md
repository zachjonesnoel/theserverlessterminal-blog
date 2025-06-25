---
title: "Amplify your web hosting"
seoTitle: "Amplify your web hosting"
seoDescription: "Amplify on AWS offers diverse web-hosting options, such as EC2 instance, S3 static web-hosting, and Amplify CLI deployment"
datePublished: Sat May 08 2021 16:02:38 GMT+0000 (Coordinated Universal Time)
cuid: cldueu20i00fydonvbdwucd8m
slug: amplify-your-web-hosting
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675784515568/a151509f-e13d-4802-8470-1c81fdf2a8eb.jpeg
tags: aws, web-hosting, serverless, awsamplify

---

Web application hosting from the traditional server architecture, it was a common practice to start an EC2 Instance with various packages like Apache, Nginx etc. as the world moves to serverless architecture, AWS provides innovative ways to address web hosting.

#### What are the options for web hosting on AWS?

* Web server over EC2 instance - The traditional systems involve a virtual machine which is configured as your webserver.
    
* S3 Static web-hosting - Create a bucket and in the permissions enable static web hosting and deploy/upload your static HTML content or dynamic web application built on ReactJS or VueJS to the S3 bucket..
    
* Amplify web-hosting - similar to S3 static web-hosting, but seemless process of CI/CD comes into picture where [AWS Amplify](https://aws.amazon.com/amplify/hosting/) offers a web-hosting in multiple ways.
    

#### Amplify web-hosting

Amplify provides different options for you to setup your hosting such as -

* AWS Web Console
    
* Amplify CLI
    

#### How does Amplify web-hosting work?

![Originally from AWS Documentation](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784502803/30d79a5c-6846-4aea-8d90-3ea411ad5751.png align="left")

The working and underlying process of Amplify web-hosting is as simple as it looks. You can refer the original documentation from [AWS Amplify](https://aws.amazon.com/amplify/hosting/). All you have to do is navigate to AWS Amplify in the services and follow the steps below -

* Create an App from the web console - So to begin with, the whole process of deploying your web application code to the server would involve a CI/CD process where your code repository comes in very handy. To make things more accessible and less human involved, Amplify let's you connect to the code repository of your choice - GitHub, GitLab, BitBucket, CodeCommit and for someone who doesn't have it setup, you can even upload a zip file of the built source code which has to be deployed.
    
    ![Code repository connectors](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784505416/f6362c42-59eb-430a-8c52-2ac693a42755.png align="left")
    
* Configure build settings - Amplify detects the app you are trying to deploy if it's ReactJS app or VueJS or any other framework and it generates a build script and you also have the freedom to change the script to do your custom operations if needed.
    
    ![Build settings](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784507798/217152ec-987a-41ba-9f3c-43fc785d9dc9.png align="left")
    
    And for more flexibility and customization, we can even choose a docker image if we have additional packages which have to be installed during the build process.
    
    ![Advance options](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784509390/f62f963e-7ca4-49f0-95d1-634e2720f641.png align="left")
    
* Deploy your app - You can review the settings and build settings you have configured and deploy the web application on Amplify
    
    ![Review the settings](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784510811/053d91d8-0ce1-4c24-bc37-3b620b44d251.png align="left")
    
    Amplify uses its default container or the docker image specified and builds the app to deploy over Amplify's global content delivery network (CDN) and provides you an endpoint. And Voila! you have successfully completed deploying your web app to the cloud.
    

![Build process](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784513128/2ad2c4ff-302d-4250-a699-5e9e5251f4d5.png align="left")

The build completes up in 3mins 18secs and as you can see, the whole build log is available to see what all commands were executed during the build process. The complete build can be automated with the Git repository commits, and then the continuous deployment will take care of your web application deployments.

Now that we have a Amplify endpoint shared over Amplify's content delivery network, it is really hard for anyone to remember these URLs which are system generated, to address this and make your Amplify hosted web application available over custom domains, Amplify provisions domain management via Route53. Where you can purchase and also add records to a third party domain name provider. And then it is just a DNS redirection from Amplify's CDN to your own custom domain.

#### Amplify CLI deployment

From the console you are more restricted to the Git repository or the built code which is available to be deployed, [Amplify's CLI](https://docs.amplify.aws/cli/hosting) goes another step ahead to help to ease the process all from a CLI command.

In your Amplify project, you would have to run the following commands.

```json
amplify add hosting
amplify configure hosting
```

And follow the series of steps prompted. Then when you are publishing your Amplify project with the command -

```json
amplify publish
```

Amplify published it over a S3 S3 bucket with static web-hosting enabled. With the configuration settings, you can control staging and also availability of the web app over S3 URL or CloudFront's global CDN.

####Conclusion Amplify provides you with ways to amplify (strengthen) the whole process of web application hosting in the serverless world.

And everyone who is curious what's the VueJS project which was used from my GitHub repository do, that's [my page](https://www.zachjonesnoel.com/#/) which is completely on Amplify web-hosting.