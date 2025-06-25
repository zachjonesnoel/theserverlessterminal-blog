---
title: "Amazon Inspector can now scan AWS Lambda Functions"
seoTitle: "Amazon Inspector can now scan AWS Lambda Functions"
seoDescription: "Amazon Inspector now scans AWS Lambda Functions for vulnerabilities. Get automated scanning for Java, NodeJS and Python runtimes"
datePublished: Wed Nov 30 2022 12:21:10 GMT+0000 (Coordinated Universal Time)
cuid: cldkcmtu501374onv25sa8i08
slug: amazon-inspector-can-now-scan-aws-lambda-functions
canonical: https://dev.to/aws-heroes/amazon-inspector-can-now-scan-aws-lambda-functions-acg
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675176410019/d0a16da0-7518-4550-ad62-de760e84b444.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1675176468495/6a369abd-7cb2-48c8-82df-a6f89d69b471.png
tags: aws, security, serverless, aws-lambda, aws-inspector

---

[Amazon Inspector](https://aws.amazon.com/inspector/), a service focused on automated vulnerability scanner that continuously scans AWS workloads for vulnerabilities is [now supporting scanning for AWS Lambda functions](https://aws.amazon.com/about-aws/whats-new/2022/11/aws-amazon-inspector-support-aws-lambda-functions/).

Amazon Inspector supports scanning of AWS Lambda functions and Lambda layers with Java, NodeJS and Python runtimes.

### Need for vulnerability checks

Often times, we have code which depends on many packages from installed via different package managers which are prone to security leaks. Although, updating to new version could resolve it, you might have dependencies which are still prone to vulnerabilities. The best way to address is a regular scanning of your codebase to ensure there aren't serious issues.

Serverless specific, until now we had to depend on a third party tool to scan but now it's possible with Amazon Inspector

### Enabling Inspector

First off, you would have to enable Inspector for your AWS Account.

![Enabling Inspector for your account](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176135036/73472bed-cfcf-4d26-9ee1-ce0ff195e774.png align="left")

### Your first scan

Once enabled, you will need a few minutes for [Amazon Inspector](https://aws.amazon.com/inspector/) to scan across your resources across Amazon EC2 instances, Amazon ECR images and now AWS Lambda functions and Lambda layers.

After [Amazon Inspector](https://aws.amazon.com/inspector/) has scanned you can view the report on Inspector dashboard.

![Amazon Inspector dashboard of all vulnerabilities](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176137518/f9215346-9315-44bd-ab46-201446ddf91e.png align="left")

\[Fun Fact\] *As you can see, I don't have a single EC2 instance running on this AWS Account*.

### Scanned findings

Inspector found that *9 of my Lambda functions* had a vulnerabilities with critical, high and medium levels.

![Inspector findings by Lambda functions](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176139769/e06c64f1-9e07-4e03-b632-868d0ebf695b.png align="left")

If you click on one of the functions, you can find the summary for vulnerabilities in that specific AWS Lambda function or the vulnerability because of using an AWS Lambda layer.

![Summary for a Lambda function](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176142188/8ec82027-013e-4ae7-ac8f-a54d5035758b.png align="left")

![Findings for a Lambda function](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176143629/8e094ce9-2264-495c-b7b9-5532b4b80267.png align="left")

### Let's dive into the finding

One of the vulnerability is with [Axios NPM package](https://www.npmjs.com/package/axios).

![Vulnerability with Axios](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176145769/0b6c8153-4fdf-4cef-852a-cee148048768.png align="left")

This also gives details about axios package and the affected with fixed version.

![Affected packages](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176148127/546c6084-0fea-4bc8-a01c-153b5f030d54.png align="left")

Inspector provides you the complete details of the vulnerability along with the link to National Vulnerability Database (NVD) report.

![Vulnerability details](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176150260/d558e57d-b8f8-4839-a573-d7d55bf19844.png align="left")

Along the details, you can also find how to fix it with the available remedy.

![Remedy to fix the vulnerability](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176151757/6cb0b36c-b7b2-458e-b860-7741cf30df65.png align="left")

In this case, it's updating axios version.

Another way to understand the severity of the vulnerability, the score from National Vulnerability Database (NVD) and Inspector is available.

![Inspector score](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176154101/68173513-c984-47a0-a8ed-c93f7d4cf8d2.png align="left")

### Pricing

Amazon Inspector is available as part of free trial for 15 days. For Lambda scans alone, there is monthly based on average number of Lambda functions scanned per month and price is prorated based on total Inspector coverage hours for the month.

More details on [Amazon Inspector Pricing](https://aws.amazon.com/inspector/pricing).

### Action time!

Now it's time to scan your Lambda functions and layers with Amazon Inspector.

![Time for scans now](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176155497/f4141230-a4d1-4879-a92a-3122d46dd543.gif align="center")