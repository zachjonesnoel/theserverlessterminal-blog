---
title: "Serverless Applications at Scale? Now Go Build with Best Practices"
seoTitle: "Serverless Applications at Scale? Now Go Build with Best Practices"
seoDescription: "Learn AWS Lambda best practices for scaling, runtime selection, managing cold starts, deployment, and performance optimization at PeerTalk meet-up re:Invent"
datePublished: Thu Jan 09 2025 10:55:42 GMT+0000 (Coordinated Universal Time)
cuid: cm5p7px2c000d09mtbvhre9oj
slug: serverless-applications-at-scale-now-go-build-with-best-practices
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736248240861/cacd83c5-d237-40b2-ba99-6e9854d80484.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1736420011465/a40f42d7-7e4f-429c-84b0-2796e9a05d14.png
tags: aws, best-practices, scalability, serverless, aws-lambda

---

At AWS re:Invent 2024, [Arshad Zackeriya](https://www.linkedin.com/in/arshad-zackeriya-713b821b/), [Darshit Pandya](https://www.linkedin.com/in/darshitpandya/), [Jones Zachariah Noel N](https://www.linkedin.com/in/jones-zachariah-noel-n/), [Pubudu Jayawardana](https://www.linkedin.com/in/pubudusj/), and [Sean Kendall](https://www.linkedin.com/in/sdkyyc/) hosted a PeerTalk meet-up about **Serverless application best practices at scale.** The discussion was very insightful although given the limited time and Serverless is a broad topic, we focused our discussion on **AWS Lambda at Scale**, with all of us sharing our expertise with AWS Lambda to be specific in the Serverless space to help the folks with their concerns and queries when building on AWS.

[AWS Lambda Functions](https://aws.amazon.com/lambda/) is the core of our compute layer. The reasons that weigh in for Lambda as a choice are scalability, a focus on business logic rather than infrastructure management, cost efficiency with a pay-as-you-go billing model, and the ability to bring your own programming (BYOP) language for the workloads. There is also a caveat that you should be aware of, how different best practices help with better architectures at scale.

# **Choice of runtime**

AWS Lambda makes it feasible to choose the runtime/programming language for the workload you are building. Some runtimes are managed by AWS, such as Java, Python, NodeJS, and a few more. When you want a specific runtime like PHP, you can run the custom runtime with the image, and you, as a developer, should manage the updates with the runtime.

![a man in a suit giving a thumbs up in front of a white board that says it depends on the situation](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfi1nFUv-GB6SL7D2OhcmhMD8Zf5TgKCupvx_qh0OhrbmooojtTEfnM0cqKP_H9T2bWrGQ9RfIyyEToxg0iZpCsQJRMhKdOQ2ymULVHGXD11m9TfOwJ5dH-bQapS7hX75Y_tD0Kqw?key=w3xiS6QZ-re-bh-OQeYYtwsT align="center")

The choice of the runtime depends on the workload and the requirement of the Lambda function. For example, a simple web application with CRUD could use NodeJS whereas for an application that is dependent on data and has extensive support with Python libraries, you may choose Python as your Lambda’s runtime. At times, it’s also considering the skillset of the team and the learning curve that could factor in for choosing a popular or performant runtime. For instance, if the team is well skilled in NodeJS and Python but knows that Rust is performant, one should not blindly start their Lambdas with Rust.

# **Cold starts are a thing!**

Even in 2024, with Lambda being around for a decade, Cold Starts with Lambda function is still something one has to factor in. Cold Starts occur when the Lambda function is not invoked for a period of time where the Lambda function is de-provisioned for larger scalability. When the Lambda function is invoked, there is an init phase that basically initializes the Lambda function with the respective runtime and runtime dependencies and additional code dependencies that add to the latency of the first invoked Lambda execution.

Suppose you aren’t aware of how Cold Start and Lambda performance vary with different runtimes. In that case, Maxime David has built a tool, [Lambda Perf,](https://maxday.github.io/lambda-perf/) which benchmarks Cold Start across multiple runtimes supported by the Lambda function.

![Different Cold Starts with runtimes and memory](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd3A5VcjWJck7WyKQT1f5U_DWL-Eqf_gidflZP_CRgcgQMzNkeeO3HXrXD6D4rwes1Q7ryn975Ampb96A0H7w7U2nbSBdMQaF61oRvVkUUe0pT44ACK_JwCdawxtO3jWIRx-of9?key=w3xiS6QZ-re-bh-OQeYYtwsT align="center")

# **SnapStart is coming in hot**

AWS Lambda's feature focused on reducing Cold Starts associated with Lambda functions with specific runtimes. At AWS re:Invent 2022, SnapStart made a debut with support for Java runtimes—Java 11 and extended to Java 17. After a worthwhile wait, AWS announced at AWS re:Invent 2024 that SnapStart now supports Python 3.12 or later and .NET 8 and later.

SnapStart addresses the Cold Start problem by creating a pre-initialized snapshot of the Lambda function’s environment during deployment that is used for subsequent invocations. This snapshot contains an image of all the necessary dependencies, resources, and configurations for that Lambda function to run.

![Lambda's SnapStart illustration](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdChA507k4IQMMOGPejzUqXWqw8KunaQJ3n810Q526FftCF3IXTXMHWcfaq3nlwPiuxuIClMTX5OZQfMLVr4hkLgbDyZs54jHHSdbTmcCSTjzPiQjOFfBQ2rZuDHe1DRFNItTXEsQ?key=w3xiS6QZ-re-bh-OQeYYtwsT align="center")

[Vadym Kazulkin](https://www.linkedin.com/in/vadymkazulkin/) has authored a whole series about [AWS SnapStart with Java blog series](https://dev.to/vkazulkin/series/24979) that benchmarks Cold Starts, reduced Cold Starts with SnapStart and end-to-end deployment with Java runtimes.

# **Is Lambdalith a good approach?**

Fat Lambdas or monolithic Lambdas often referred to as Lambdalith is the approach that many developers take to have all the business logic in one single Lambda function which could be invoked either by API Gateway / AppSync so that it’s one entry point for most of your executions. Unfortunately, not every invocation would need every bit of Lambda’s business logic or imported dependencies. This approach brings in cost efficiency and usage of frameworks such as Express with Node runtime (which is not the need of the hour for most cases) however, it introduces scalability issues where concurrency in production becomes a major concern with added Cold Starts due to large code and dependencies which are to be loaded during the initialization phase of the first invoked Lambda execution. Additionally, code management is also a concern given the Lambda function performs multiple tasks.

Some lessons from Dr. Werner’s keynote: we should bring in the simplexity while building our Lambda functions so that we have Lambda function for specific tasks which could give us the feasibility to have the needed dependencies alone for that specific Lambda function also configurations (reserved concurrency or timeouts or memory or runtime) based on the workload handled by the Lambda function.

# **Purpose fit dependencies with managing Lambda Layers**

As the previous section called out, breaking down the Lambda function based on the task. A similar approach to Lambda Layers which contain additional code dependencies and packages that could be shared across multiple Lambdas.

![Lambda Layer's illustration](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfNSFaJoW2cdirQ8BCAZmOaBUebvZBKX159ZXOd1E3LrfuaXD8bIgCI6LYYCP4ZEa4_ZLh4_6DBTCIXE-CKLkUGycOf5-Ew89WEwPmW8fp5l-DAUYfexJlGBfaui7kbYeHe_TM3?key=w3xiS6QZ-re-bh-OQeYYtwsT align="center")

Creating Lambda layers that are only required by the Lambda function enables the Lambda function to not only perform better but also help in code management. This approach could be achieved by understanding what’s the purpose of your Lambda layer and how are they reused by other Lambda functions. Keeping the size of Layers small avoids the bloating of Layers and Functions, especially during initialization and execution.

# **AWS Lambda Power Tuning for optimizing performance and cost**

[AWS Lambda Power Tuning](https://github.com/alexcasalboni/aws-lambda-power-tuning) tool is an AWS Step Function State Machine that runs multiple concurrent versions of the Lambda function with different memory allocations (128MB to 10GB) and later it analyses the execution logs with cost and time for that specific invocation and recommends the best possible configuration for optimized cost and best performance.

AWS Lambda Power Tuning tool helps with data-driven decisions to make the right choice of Lambda configurations with an automated approach. This tool can be integrated into your CI/CD Pipeline so that you can run the analysis based on the deployment of the Lambda function and later optimize the configuration for best performance and cost.

# **Choice of deployment - Canary v/s Blue-Green**

The deployment strategy depends on how your Lambda function is being invoked and the traffic patterns. The factors to consider would be how are you testing the feature/change end-to-end, if something is broken, what’s your rollback plan and the traffic at the time of deployment.

Canary is a strategy where you switch the changes and roll out to a subset of users. For instance, if the rollout is expected across regions/ AWS accounts, start with one region in one AWS account, and test for completeness. In a sunny day scenario, you would continue this with a region-by-region rollout.

Blue-Green is a strategy that makes the changes instantly between two environments. This brings into account that changes have to be tested in a different environment end-to-end. The best option for applications and workloads that require extensive testing before deployment with zero downtime and isolated testing.

Choosing between a Canary or Blue-Green deployment strategy is based on how your application is architected and tolerance with impact in production.

# **Wrap up!**

This blog talks about the specific factors we spoke about at PeerTalk meet-up, but the things to consider for best practices for a Serverless application at scale also include factors such as concurrency and approach to handling concurrency for Lambda function with reserved or provisioned when it’s in the deployed state in production. However, this blog focuses mostly on the factors and trade-offs during development and deployment.

![Serverless application best practices at scale PeerTalk Meet-up at re:Invent '24](https://cdn.hashnode.com/res/hashnode/image/upload/v1736417517491/ff3e2073-fd96-46a8-b705-18e1984dc25d.png align="center")

Thanks to [Arshad Zackeriya](https://www.linkedin.com/in/arshad-zackeriya-713b821b/), [Darshit Pandya](https://www.linkedin.com/in/darshitpandya/), [Jones Zachariah Noel N](https://www.linkedin.com/in/jones-zachariah-noel-n/), [Pubudu Jayawardana](https://www.linkedin.com/in/pubudusj/), and [Sean Kendall](https://www.linkedin.com/in/sdkyyc/) for sharing their expertise and also contributing to this blog.