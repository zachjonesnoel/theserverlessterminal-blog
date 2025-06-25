---
title: "Cloudy with a Chance of Mind-Blown: AWS re:Invent 2023"
seoTitle: "Cloudy with a Chance of Mind-Blown: AWS re:Invent 2023"
seoDescription: "Looking back at the experience of AWS re:Invent and also some excitement of my first ever in-person re:Invent"
datePublished: Thu Dec 07 2023 08:14:36 GMT+0000 (Coordinated Universal Time)
cuid: clpux9ulc000i08l5c5vx4vou
slug: cloudy-with-a-chance-of-mind-blown-aws-reinvent-2023
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1701884806337/6d892154-dd32-4cf9-b322-a52cc8b4af21.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1701884749957/e58f78dd-0898-4ca1-b261-7302869c8e98.png
tags: aws, community, reinvent2023

---

Last week I experienced AWS re:Invent 2023 in person, it was a great time to connect with fellow Serverless Minds (a.k.a like-minded folks) and have casual chats and serious discussions about Serverless.

[Amazon PartyRock](https://partyrock.aws/) was launched a few days before re:Invent and guess what? I created an app to suggest a blog title with a few descriptions and the title of this blog is generated with PartyRock!

The theme of re:Invent was around Generative AI and Dr. Werner Vogels' keynote which emphasised cost and sustainability while being Frugal Architects. I remember how reInvent 2022 was about making everything "Serverless", just a year later we are still on track to lose the meaning of "Serverless" and making it all about "GenAI".

![Everyone talking about "Generative AI" - Image generated with Amazon PartyRock and Amazon Bedrock with Stability AI.](https://cdn.hashnode.com/res/hashnode/image/upload/v1701874243955/88ad1d5c-44f0-48e0-ba91-03b828b97620.png align="center")

*Everyone talking about "Generative AI" - Image generated using Amazon PartyRock and Amazon Bedrock with Stability AI.*

## Keynote(s)

Adam Selipsky's keynote had an announcement about a new AWS Service - [Amazon Q](https://aws.amazon.com/q/) which helps developers during development and also companion-based developer experience of using AWS Services.

Dr. Werner Vogels' keynote as I mentioned emphasized being "The Frugal Architect" with designing and architecting our applications and workloads on AWS with known pillars of The Well Architected Framework but also including cost-centric and sustainability-centric architects which go a long way!

![Werner's The Frugal Architect laws](https://cdn.hashnode.com/res/hashnode/image/upload/v1701875018299/720e2bd5-ded1-4226-b1fc-4eec48fd0a47.png align="center")

Werner's The Frugal Architect states laws that enable architects to be cost-efficient in the lens of design, measure, and optimize. As developers, architects, and techies who are building on the cloud, we must have a sense of cost and sustainability. It's time, we take a step back and evaluate our architectures so that we are building for tomorrow not just for today.

Werner's keynote announced the new [myApplications](https://aws.amazon.com/about-aws/whats-new/2023/11/myapplications-view-manage-applications-aws/) which is a new Console experience with consolidated costs, health, and performance of our applications. [Application Composer gets a new makeover with an IDE extension](https://aws.amazon.com/about-aws/whats-new/2023/11/ide-extension-aws-application-composer/) that not only gets drag-and-drop experience but also adds on the [Step Functions' workflow studio experience](https://aws.amazon.com/about-aws/whats-new/2023/11/aws-application-composer-step-functions-workflow-studio/) (hopefully, I'll get to try things around soon and more blogs around it coming up). And also, Amazon Q as part of the IDE.

As an AWS Hero, being able to get the front-row seats to witness the keynotes was one of the best experiences. A special shoutout to Heroes by Werner just before he started the keynote was a special moment!

## AWS Service updates and announcements

If you are actively following updates and announcements during pre:Invent season, there were major ones concerning improving the operational excellence of Serverless applications to better monitoring and observability in the world of distributed.

ICYMI, [The Serverless Terminal issue - AWS Lambda 9 years of Serverless AWSomeness #43](https://www.theserverlessterminal.com/p/aws-lambda-9-years-of-serverless) covers the pre:Invent announcements with Lambda updates making the troubleshooting experience better, CloudWatch Logs supporting regex filter pattern.

> Dude, I've literally searched logs for specific keywords/patterns and the search on console/CLI has got on my nerves.

![Troubleshooting and debugging with searching logs like needle in a haystack- Image generated using Amazon PartyRock and Amazon Bedrock with Stability AI](https://cdn.hashnode.com/res/hashnode/image/upload/v1701883393690/7408a4f2-aeb5-439a-b4f6-35f67a2be69b.png align="center")

*Troubleshooting and debugging with searching logs like a needle in a haystack- Image generated using Amazon PartyRock and Amazon Bedrock with Stability AI*

I remember years ago, my first-ever interaction with *Eric Johnson (Serverless DA, AWS)* was about ***how to migrate my rookie Serverless application that I built on console to IaC***, never have been a better way other than export as SAM template but now you can export that into Application Composer which is much smoother and simpler. Just that after that poor console management experience, I advocated for the IaC approach for building Serverless applications. Good to see that it's all falling into place.

While the week of re:Invent was hectic enough, I also challenged myself to publish [The Serverless Terminal issue - We've always done it this way #44](https://www.theserverlessterminal.com/p/weve-always-done-it-this-way-44) that calls out the different updates from Lambda new runtime supports to Step Functions making its way with HTTPs invocation make it much easier for integration options for third party APIs, how can I forget Bedrock integration on Step Functions!? With all that integration, if the state machine fails; there is a way to restart the workflow whenever it fails (no, it's not the entire workflow rather from the point of failure).

Strong opinion about what Serverless is, because many of the Server-full services have termed some of it's offerings as "Serverless" even when you have flat pricing for non-usage. Are we losing out on the real Serverless features and services in this journey?

## Connecting with Cloud/Serverless Minds

![Connecting with Serverless Minds, running out of space to fit all photos :P](https://cdn.hashnode.com/res/hashnode/image/upload/v1701886093367/10d74a47-11fe-45bd-9a48-96a3b8fae220.png align="center")

This is my first re:Invent and I'm meeting the majority of them for the first time. You ask, whom did I meet? AWS Heroes, AWS Community Builders, AWS UG Leaders, AWS folks who are listening to me whine and also patiently giving an ear to my queries and helping - AWS Developer Relations team, AWS Service teams - PMs, Engineers; the list does not end.

The conversations around what they are building with Serverless to unlocking the potential of Serverless - left me with the thought - "*wow that is possible!!?*" Well, I've seen so many of them building content around cloud and Serverless but in-person connection is hard to put down in words.

Imposter Syndrome is real - having such great conversations although many of them were for a few minutes and how we have known each other over the internet but everyone is so welcoming and for a first-timer, I bet most of them gave me tips on how to survive "the week".

I finally met a fellow Bangalorian - *Praneeta Prakash* (*AWS Modern App Experience PM*) her Twitter bio says Bengaluru hudugi and we conversed in my native language *Kannada*. Check out the [tweet](https://x.com/praneetaprakash/status/1730058181198610435).

Not just meeting Serverless minds and having a conversation, did something different with *Luciano Mammino (AWS Serverless Hero)* who co-runs AWS Bites podcast where we took our conversation from in-person to record it with all the noise at the Expo. Check out the [podcast](https://awsbites.com/107-expert-opinions-from-re-invent-2023/) featuring some brilliant folks!

## AWS Sessions

Something that I'm not worthy to talk about 'cause I didn't attend them! Sessions of different categories and levels happened all across the Vegas Strip in multiple Hotels and conference areas although I was in Venetian; I was not able to make it to the sessions. However, I did catch up with many speakers whose sessions I had booked but didn't make it to, and spoke about mostly things around their session topics.

Guess what? All recorded sessions during re:Invent are now available on demand and I created a [Twitter thread](https://x.com/zachjonesnoel/status/1731660115798045141?s=20) to keep a check on what sessions to watch before 2023 concludes.

## Community Panel discussion

![Speaking at Community Panel Discussion](https://cdn.hashnode.com/res/hashnode/image/upload/v1701934213412/4090a255-2138-4976-8804-3f6cc30d3ac5.jpeg align="center")

Although this being my first re:Invent, I was able to represent AWS UG Bengaluru, being AWS Hero at the community panel discussion that happened in the DevLounge area of the Expo along with *Kaushik, Lena, and Shafraz*.

We all shared our views of being part of the AWS Community and programs - AWS User Groups, AWS Community Builders, and AWS Heroes.

> I guess I can say - "I did speak at re:Invent"

## ServerlessLand Video

*Eric Johnson (Serverless DA, AWS)* and I had a chat on ServerlessLand Video talking about my Serverless journey and my role as a DA at Freshworks.  
Check out the [video](https://video.serverlessland.com/video/st-1EKVZfWqOUHYlX8x1qw0MTY).

![Interview on ServerlessLand Video](https://pbs.twimg.com/media/GANCp2ha8AEruaA?format=jpg&name=large align="center")

## Glorious moment for Freshworks

Freshworks was one of the sponsors for AWS re:Invent 2023 and we had our own wins be with sessions about how Freshworks is innovating with Generative AI to having *Adam Selipsky (CEO, AWS)* visit us at the booth and also gave a shoutout in his social media posts.

![Adam Selipsky at Freshworks booth](https://media.licdn.com/dms/image/D5622AQGKwyiKMHzBMQ/feedshare-shrink_2048_1536/0/1701261739547?e=1704931200&v=beta&t=NVkIp5FrY0D0k0sFimDbYL1e8xL4atY4ctgY_lDChbM align="center")

Check out [Adam's LinkedIn post](https://www.linkedin.com/posts/adamselipsky_awsreinvent-aws-activity-7135833454587383808-6uMH) for more details.

## What's next?

I'm still re:Covering from re:Invent but being able to connect with community folks not only as people over the internet, but now with human connection. I'd look forward to collaborating with many (I wish it's all of them) for many things with AWS UG Bengaluru, The Serverless Terminal, and The Zacs' Show Talking AWS.

To wrap up, I'd quote and agree with Werner -

> There has never been a better time to be a BUILDER

With that, my first-ever in-person re:Invent is in the books!! Big cheers to *Farrah Campbell (Head of Modern Compute Community, AWS)*, *Taylor Jacobson (AWS Heroes PM, AWS),* AWS Developer Relations team, AWS Community and Freshworks for making re:Invent memorable.

P.S. I planned to make this a tech blog and while I jotted down my initial thoughts, I felt that my experience of re:Invent is worthy to be shared rather than just a tech / cloud / Serverless takeaway from re:Invent.