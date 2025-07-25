---
title: "The Serverless SWAG!"
datePublished: Mon Apr 19 2021 17:45:03 GMT+0000 (Coordinated Universal Time)
cuid: cldueumh200g8donv9kgb65bk
slug: the-serverless-swag
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675784541886/85876eeb-467d-4fa5-831c-4af4888b1b32.jpeg

---

Serverless architecture has been on trending application architecture for the last couple of years with giant streaming applications such as Netflix, Disney+ have adopted AWS serverless architecture for their workloads. What is that makes serverless an adaptable architecture and also why does it interest the traditional server-based architecture/ monolithic architecture applications to migrate to serverless?

![Serverless services by AWS vs GCP vs Azure](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784535423/96ae4f4d-c261-46f2-bea9-164fa7cd9445.jpeg)With reference to <https://logz.io/blog/serverless-guide/>

#### What is the serverless SWAG?
* **S** - Server management reduced.
* **W** - Worthwhile
* **A** - Application focused.
* **G** - Going global.

####Server management reduced
Yes, with serverless as the name suggests, we as developers will not be focused on the server management or maintenance of the server which of-course is a huge deal in a server-based architecture. For eg, on AWS, any server is hosted on an EC2 Instance, and all the patches and upgrades such as the OS, security patches, network patches and the load etc have to be managed by an IT admin, in the serverless world, we are focused on the service to leverage and also code to write. 
![](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784537978/227e6994-0bfc-4e74-9bf2-9497047bcc3e.jpeg)
Like it is commonly said, the serverless world includes servers which is running your code; just that you wouldn't have to manage the servers, let the cloud provider manage things for you - such as auto scaling, load balancing, etc.

####Worthwhile
Serverless architecture includes cutting edge technologies which gives you goosebumps and you get to choose the right configurations and architecture design which leverage the right service/technology to optimize your compute and also making the application cost effective as you run the services **on-demand** and you only pay for what you use and for the duration you use. 
![AWS Summit debunking the myths](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784539580/b0951f2a-2302-4463-9589-86974896b280.jpeg)

####Application focused
As we know that we don't have to worry about the under-laying servers where our code runs, we are more application focused. Where the developer of the application is only focused on building the application and choosing the right service for the application. Most of the cloud providers provide a way to develop in the language (Python, NodeJS, Java, GO, etc) of your choice. And for a backend developer to get started on serverless architecture is the only time taken to write the code and provision the end-points. For eg, you have a web application which needs an authentication setup and an API which does certain things, as a backend developer on AWS serverless architecture all you have to do is, setup a AWS Cognito User Pool, write the AWS Lambda function (in the language of your choice) which does the needed actions, configure API Gateway resources to direct requests to Lambda function and voila!! The backend is setup! (will go detailed on getting started on AWS Serverless Architecture in future posts).

####Going global
We look at applications such as Netflix, Uber, FaceBook etc which are available in any part of the world, the big question for most of the newbies/cloud enthusiasts, is that *"how to make it globally available with low latency!?"* Well, in serverless architecture even though you develop/deploy/publish your application in one of the data centers, you can ensure that the application is available throughout the globe. **HOW**!? well on AWS its the magic of global networks and caching and computing nearer to the user with [CloudFront](https://aws.amazon.com/cloudfront/) (CDN network which caches the files nearer to the user) and also [Lambda@Edge](https://aws.amazon.com/lambda/edge/#:~:text=Lambda%40Edge%20is%20a%20feature,multiple%20locations%20around%20the%20world) which is a feature of CloudFront which lets you compute nearer to the user making it more faster and easily available to the user.

Well, now that's a SWAG to carry on!! 