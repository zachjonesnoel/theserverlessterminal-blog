---
title: "Serverless with orchestration and choreography"
seoTitle: "Serverless with orchestration and choreography: What's better?"
seoDescription: "Serverless orchestration with AWS Step Functions and choreography with EventBridge, each offering unique benefits for structured execution"
datePublished: Tue Apr 23 2024 18:30:00 GMT+0000 (Coordinated Universal Time)
cuid: clvfjv94h00140am8enwp0jhe
slug: serverless-with-orchestration-and-choreography
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1714067942569/b94629d0-5b83-4c4b-9051-cbf9aa3df7b8.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1714067986805/eaf5c344-2c06-426c-8064-85c353de28b0.png
tags: aws, orchestration, aws-step-functions, choreography, amazon-eventbridge

---

Building Serverless on AWS would be using multiple AWS Services which are building either following an event-driven architecture or a microservice architecture which needs either orchestrated or a choreographed architecture so that execution happens smoothly! Sometimes, it also involves using a hybrid of orchestration and choreography.

# Orchestration

![Orchestration of music GIF from the internet](https://lh6.googleusercontent.com/izRpzDSJq6syGarWedXayMM6MiiQow7mbbf0b140zprmuSPxXMTnGStolqmjr3Zz433k9ekmbCUhphKaBhq-ZbrhspxiYFEgHqbe51aUijbaahquabe5Nw9MyfJrLlWzM8GdkZyDfoT9mdoUhHdw9w1uDQ=s2048 align="center")

Just as an orchestration looks, there is a concertmaster or lead who is part of the band would controls how each musician plays their instrument; in the orchestration pattern in Serverless architecture, the orchestrator controls the flow and execution of different tasks and processes based on the workflow that is defined.

## Why do you need orchestration?

![Perks of orchestrations](https://cdn.hashnode.com/res/hashnode/image/upload/v1714044592591/ba6dc9f8-ca43-46dc-a156-3f71364cc01c.png align="center")

Orchestration as a pattern promotes Serverless applications to execute in a structured and less chaotic.

* **Centralized control** - With centralized control and a defined workflow for task flow and execution, orchestration assists in promptly adhering to the workflow definition. Furthermore, error-handling techniques for both the workflow and each task can be integrated into the workflow definition.
    
* **Scalability** - In the orchestration pattern, it enables scaling individual tasks within the same workflow, simplifying the scaling of the entire workflow and only the tasks that require scaling up or down. These tasks could include Lambda Functions, ECS tasks, or the implementation of asynchronous tasks.
    
* **Monitoring** - The centralized control enhances visibility into individual tasks and the workflow as a whole. This improves monitoring capabilities for each iteration of the execution, including the state and data passed between tasks. This makes debugging easier and provides a better understanding of the workflow's execution.
    

## AWS Step Functions

In the AWS ecosystem, it is AWS Step Functions that enable orchestration of a workflow (`State Machine` in Step Functions terminology) with the execution of different tasks in the workflow.

![A State Machine for User sign-up and it's related process](https://lh7-us.googleusercontent.com/_p2tEkkFFBVlcRDh6Xh_39VZUPx40raPfFRg1ChCVZSI12XyY2oWtMGrQ9nvIP_gEZ5hWl6fAAZE1LuNgL28mOMmq7muNE39Cj33ZIWqA1QTvC86Wn8EcYHYyjHC62JG3MUPbATe1c4K01c=s2048 align="center")

### Serverless orchestration of workflows

AWS Step Functions helps with different kinds of flows - `parallel`, `map`, `pass`, `choice` and more which allows the flexibility to define a workflow with a combination of flows and `state`.

Step Functions is Serverless by nature as there isn't any infrastructure management or provision resources for the execution. Pricing for Step functions is based on the types of workflows offered -

* `Standard workflow` - for longer-running workflows, which are priced with each state transition.
    
* `Express workflow` - for shorter workflow duration where the pricing is based on number of requests for the workflow and it's duration.
    

The `State Machine` or the workflow is defined with Amazon States Language (ASL) that is a JSON-based definition. The `State Machine` could be used in the application's Infrastructure as Code (IaC) definitions.

Step Functions supports [error-handling techniques](https://blog.theserverlessterminal.com/handling-errors-with-stepfunctions-sns-sdk-integration) and the [JitterStrategy](https://blog.theserverlessterminal.com/the-jitter-strategy-for-step-functions-error-retries-on-the-new-workflow-studio). Additionally, Step Functions integrates with over 1000 API actions from 200+ AWS Services using [SDK Integrations](https://blog.theserverlessterminal.com/why-aws-step-functions-and-sdk-integrations) and [intrinsic functions](https://blog.theserverlessterminal.com/intrinsic-functions-to-level-up-your-step-functions). These features can enhance integrations and improve how data is accessed and processed with Step Functions.

%[https://blog.theserverlessterminal.com/series/aws-step-functions] 

# Choreography

![Choreography GIF from the internet](https://lh7-us.googleusercontent.com/dWznCrziGVioiGhZtYbI-IkBLFuyRz2CCmKM6mo0xyR_QivASNaip1Q5icr-ANgP3B7UVd_Cz1IAe-4_nOPo10GCFXuV03JXh7LQE8aFeBuItejTRDbRPS51NofmwRrWZZjCyn0yEjsauAU=s2048 align="center")

In the world of Event-Driven Architecture (EDA), the choreography pattern works well to ensure that microservices or architectural components are coordinated based on events. It's like a dance choreography where you wait for the right music beats and cues from your dance partner to make the next move.

## Why do you need choreography?

![Perks of choreography](https://cdn.hashnode.com/res/hashnode/image/upload/v1714064685487/01256ad3-55a7-4af2-9415-a5d52dd9a9d9.png align="center")

In Event-Driven Architecture (EDA), the application relies on real-time events and reactive responses, choreography allows individual microservices to reach independently to the event.

* **Loosely-coupled** \- The microservices and independent services are decoupled to ensure they operate independently, even though they are triggered by events and share events to communicate with other services. Keeping the microservices loosely coupled also enhances fault tolerance. If one microservice goes down, the system remains operational because the other microservices are still running as intended.
    
* **Scalability** - Microservices can scale independently according to the need to scale up or down, promoting efficient resource utilization.
    
* **Flexibility** - Since microservices can scale based on the need and are loosely coupled, the architecture allows for more flexibility in making updates and rolling out upgrades while the system continues to operate as usual.
    

## Amazon EventBridge

Amazon EventBridge enables Event-Driven Architecture (EDA) in the AWS Serverless with the EventBridge Bus offering making it easier to route events between a source and destination. EventBridge can scale up and down to support the routing of millions of events in a production-grade system.

Here is a blog about why EventBridge is a missing piece to your Serverless application -

%[https://blog.theserverlessterminal.com/amazon-eventbridge-the-missing-piece-to-your-app] 

### Choreographing with EventBridge

* **Asynchronous messaging** - EventBridge decouples services by allowing them to post events to a shared event bus. This bus then directs the events to specific destinations, enabling the source to post the message and proceed with the rest of the execution.
    
* **Event rules and filters** - EventBridge works with event buses that use event rules to determine which events should be sent to which destination. When creating these intelligent rules, filters can be applied to the event payloads for more detailed routing of specific events.
    
* **Archive and replay** - EventBridge Bus also supports archiving and replaying events. This feature allows events to be stored for later reproduction, aiding in debugging processes.
    
* **SaaS integrations** - EventBridge supports SaaS integrations with AWS partners where the events from the AWS partner's SaaS products can be targeted to a specific SaaS Bus to process the events.
    

# Orchestration v/s Choreography

|  | **Orchestration** | **Choreography** |
| --- | --- | --- |
| **Control** | Centralized control for workflow execution but has its dependencies making it coupled | No central control instead a bus for the flow of events and also loosely coupled |
| **Complexity** | Works for both simple and complex workflows | Simple event-driven process |
| **Error Handling and Retry** | Supports native error handling and retry | Distributed error handling at the microservice level |
| **Scalability** | Highly scalable but needs to adhere to different limitations during the workflow | Highly scalable as each service can scale independently |
| **Latency** | Possible latency because of dependencies | Low latency with direct event routing and Pipes P2P integrations. |
| **Cost Efficiency** | Depends on the workflow and type of workflow | Pay only for the usage of resources |

# Best of orchestration and choreography

The choice between `Orchestration` and `Choreography` is hard to choose as it depends on the use-case and architectural pros and trade-offs!

One such example is when EventBridge SaaS Event bus can integrate with AWS partners such as Freshworks events choreographed into the AWS account to route the events to trigger a `State Machine` execution to process the events from Freshworks product - Freshdesk.

![](https://lh7-us.googleusercontent.com/TeHgSTW-QljRHAjj8IVserPaqTahmHiOtcQtf2SjnzdOY1QB56UDn3SAf3Zn4KFrTBMrcZVmjQ0euWh3X2cEEQPW2QEu-npxVYLnBRaWOTeTR04iP_0ONc8gBUo_G18mKBY9PcIXOmKwerU=s2048 align="left")

%[https://medium.com/freshworks-developer-blog/a-guide-to-understanding-freshworks-integrations-with-amazon-eventbridge-9782e54f1076] 

# Wrap up!

Serverless architecture on AWS includes orchestration and choreography patterns for structured execution. Orchestration offers centralized control, scalability, and monitoring using AWS Step Functions, while choreography enables loosely coupled, event-driven coordination with Amazon EventBridge. Each pattern has its advantages and trade-offs, so the decision between them depends on the specific use case.

In the "Build on Weekly" episode 12 by *Darko Mesaroš* and *Rohini Gaonkar*, [Orchestration, Dancing, or big blocks of code](https://community.aws/content/2exdWp50hig2lCN8sJFi2ZFuJyt/orchestration-dancing-or-big-blocks-of-code-s03-e12-build-on-weekly) discusses these patterns of orchestration, choreography, and monoliths, highlighting the benefits of each pattern.