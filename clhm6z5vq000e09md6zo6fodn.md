---
title: "Re-think and Re-architect Your Application"
seoTitle: "Re-think and Re-architect Your Application"
seoDescription: "For applications to be scalable and effectiveness, we need to identify the right architecture that happens iteratively like how Prime Video did it."
datePublished: Fri May 12 2023 16:17:16 GMT+0000 (Coordinated Universal Time)
cuid: clhm6z5vq000e09md6zo6fodn
slug: re-think-and-re-architect-your-application
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1683994613015/af72e6f8-0ad6-4bc0-8870-5d3f87201f16.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1683994627050/6cb6eac8-db72-4eb6-a393-a808e8e8e87a.png
tags: aws, architecture, scalability, serverless, cost-optimisation

---

Recently, Amazon Prime Video revealed about moving their architecture from serverless to monolithic container-based architecture. Read more about [Scaling up the Prime Video audio/video monitoring service and reducing costs by 90%](https://www.primevideotech.com/video-streaming/scaling-up-the-prime-video-audio-video-monitoring-service-and-reducing-costs-by-90) by the Amazon Prime Video tech team.

## Initial architecture of Prime Video

Few pointers from the tech blog about the initial architecture,

* Distributed and microservices approach in their initial architecture.
    
* A serverless architecture primarily built on AWS Lambda functions and AWS Step Functions.
    
* Designed with the intent of Serverless being cost-effective and scalable because it was on-demand scaling up or scaling down of Lambda functions which resulted in keeping cost intact.
    

![Initial architecture of Prime Video defect detection system with Serverless](https://cdn.primevideotech.com/dims4/default/bf61eca/2147483647/strip/true/crop/1011x803+0+0/resize/1011x803!/quality/90/?url=https%3A%2F%2Famazon-k1-prod-entertainment.s3.amazonaws.com%2Fbrightspot%2Fb2%2Fd2%2Fdde535c6478d9c4b8c9891c4d93b%2F98191782.png align="left")

*Image courtesy: Prime Video blog post -* [*https://www.primevideotech.com/video-streaming/scaling-up-the-prime-video-audio-video-monitoring-service-and-reducing-costs-by-90*](https://www.primevideotech.com/video-streaming/scaling-up-the-prime-video-audio-video-monitoring-service-and-reducing-costs-by-90)

## Reasons why Serverless was a challenge

Prime Video is one of the popular OTT platforms, and the assumption here is that they would have peak traffic and the workload in this traffic times is what the architecture to hard test in terms of -

### Scalability

Yes, Serverless applications can scale well. But the main concern of scalability came in with orchestrating the Serverless workload with AWS Step Functions.

> The main scaling bottleneck in the architecture was the orchestration management that was implemented using AWS Step Functions. Our service performed multiple state transitions for every second of the stream, so we quickly reached account limits.

As quoted in the blog post. Often when building Serverless applications when they hit a peak moment, there are challenges -

* Managing the distributed architecture because you have too many microservices doing various dedicated tasks even though it's a State Machine defined.
    
* AWS Step Functions bring in limits in payload passed across states, the number of state machine executions where there should be workarounds in place to ensure the system doesn't breach the limits.
    

### Cost-effectiveness

Serverless applications are cost-effective because they run on demand and when certain events/requests are made.

> Besides that, AWS Step Functions charges users per state transition.

AWS Step Functions have a per-state transition pricing model, for instance, in `us-east-1` region that has $0.025 per 1000 state transitions, Prime Video would have a large number of state transitions happening as this state machine itself is used to detect if any defect is there in the video frames.

> The second cost problem we discovered was about the way we were passing video frames (images) around different components. To reduce computationally expensive video conversion jobs, we built a microservice that splits videos into frames and temporarily uploads images to an [Amazon Simple Storage Service (Amazon S3)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) bucket. Defect detectors (where each of them also runs as a separate microservice) then download images and processed it concurrently using AWS Lambda. However, the high number of Tier-1 calls to the S3 bucket was expensive.

The microservice which is used to split videos in frames uploads images to an S3 bucket and these images are processed to detect defects where there would be 1000s of frames in a single video which itself would have S3 bucket costs high with respect to storage, data transfer and the cost of high-performance Lambda functions which run in parallel.

## Reaction to the challenges

The reasons mentioned are very valid and the Prime Video team has taken some steps to address this -

### Re-think

It is important when you have scalability and cost-related concerns with your architectures that you start to take a step back and think - "*what could have been the possible best solution?"* or "*what kind of components suit this use-case".* Sometimes, it begins with the basic question of "why do we need XYZ service" when the challenges stand out.

The first approach to adapting Serverless is good but when the architecture is not meeting the need in this use-case, the approach taken to *re-think* says how flexible we have to be in our architecture.

### Re-architect

When we *re-think*, it is obvious that we get more clarity with what is the use case and what architectural approach and components would be the *best fit*. Once we identify the *best fit architectural approach or components*, re-architecting the complete workload would be the way to go.

In Prime Video's case, there was a shift from Serverless to a monolith because the system would always be processing images, and data transfer between different Services would make it an expensive workload but with the new architecture, the ECS task performs the orchestration of detecting deflects using the video buffer directly from Media Converter.

## Resonating my thoughts

When the internet broke out with Prime Video moving away from Serverless, me being a Serverless advocate was shocked "*why*"!

[![Image](https://pbs.twimg.com/media/Fvb86n3acAAj3Ln?format=jpg&name=medium align="left")](https://www.twitter.com/theslsterminal/status/1654787999484506114)

But reading and understanding the blog post, appreciate the architectural shift even when it means that Serverless architecture is revamped into a monolith.

### Serverless first, Serverless not always

It's good to approach with the Serverless mindset and building with Serverless but if that doesn't work for your workload then "*revert! revert! revert*!". Sometimes, Serverless may never be the right approach, and investing in that would mean that it would turn out to be expensive in the longer run.

### One size doesn't fit all

We assume that if any approach works for someone out there, it would work for me as well. As it's repeatedly mentioned, "*Serverless scales and is cost-effective*" it is true but also we need to understand if the Serverless approach or any other approach in general would be the best fit for the use case.

### Iterative building

Oftentimes, the use-case evolves or the usage evolves which translates to the architecture should be adaptive and get there it's always with different iterations. Take software development as the instance where we go through different revisions and iterations to get to production and if we see the architecture of the software being developed, that too would have gone through revisions. These revisions are where the learning happens, to identify if the architecture is well-architected or not.