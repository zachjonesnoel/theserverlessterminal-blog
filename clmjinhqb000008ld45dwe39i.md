---
title: "The Jitter Strategy for Step Functions Error Retries on the New Workflow Studio"
seoTitle: "The Jitter Strategy for Step Functions Error Retries on the New Workfl"
seoDescription: "In this blog, learn about the Step function's error handling and how JitterStrategy would make a difference when Step function retries."
datePublished: Wed Sep 13 2023 18:30:00 GMT+0000 (Coordinated Universal Time)
cuid: clmjinhqb000008ld45dwe39i
slug: the-jitter-strategy-for-step-functions-error-retries-on-the-new-workflow-studio
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1694716507673/3fc23da8-4ecb-40e8-af65-ae6ea0de604b.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1694716734325/dc283e2c-42ef-41e8-a056-79e79205aef6.png
tags: aws, error-handling, serverless, aws-step-functions

---

AWS Step Function just announced a [new enhanced error handling and retry mechanism](https://aws.amazon.com/about-aws/whats-new/2023/09/aws-step-functions-enhanced-error-handling/) in State Machine executions that enables more fine-grain control of error retry rules. And also uses the [enchanced Workflow Studio authoring experience](https://aws.amazon.com/blogs/compute/enhancing-workflow-studio-with-new-features-for-streamlined-authoring) to build the workflow.

Read more about [how error handling works on Step Functions](https://blog.theserverlessterminal.com/handling-errors-with-stepfunctions-sns-sdk-integration) but in this blog, we will focus more on the new parameters in error handling with catch and retries on Step Functions such as `MaxDelaySeconds` and `JitterStrategy`.

## Errors in Step Functions

During the execution of a State Machine, there are possibilities of execution would be interrupted by various errors such as `States.Timeout` when the task execution has taken more than `TimeoutSeconds` defined as when it failed to get a heartbeat longer than `HeartbeatSeconds` defined.

Some of the possible errors are listed below.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1694626388997/c6e99ff7-01c7-46cc-bd77-fb7d3590070f.png align="center")

`States.All` wildcard is available on Step Functions to work with all the errors encountered during the execution.

## Deploying a State Machine

Navigate to AWS Step Functions *Create state machine* and then select the *Orchestrate Lambda Functions* template with the new console experience.

![Choosing a template to create State Machine on Workflow Studio](https://cdn.hashnode.com/res/hashnode/image/upload/v1694710086638/3666c21a-59c6-4516-9e8c-5714bc17a414.png align="center")

Once the template is selected, Workflow Studio will give you more details of the template.

![Choosing Orchestrate Lambda Function template and preview of the template before deploying to your AWS Account](https://cdn.hashnode.com/res/hashnode/image/upload/v1694710186979/5df8cc84-c725-4cfc-bf2e-55509aed6b13.png align="center")

The *Orchestrate Lambda Functions* template showcases the stock buy/sell recommendation based on the stock price. Choose *Run a demo* option to deploy the state machine and other resources such as Lambda Functions with SNS and SQS to your AWS Account.

To test out the state machine, you could run a sample execution.

![A sample execution of the workflow which was successful.](https://cdn.hashnode.com/res/hashnode/image/upload/v1694710569774/3aa57dfb-400f-4360-81b7-5f6754587e16.png align="center")

## Updating the State Machine with error retries

### Enabling retry

Let's update the *Check Stock Price* state which invokes a Lambda function with an error retry with a few retry options

```javascript
{
    "Retry": [
        {
          "ErrorEquals": [
            "States.ALL"
          ],
          "BackoffRate": 2,
          "IntervalSeconds": 1,
          "MaxAttempts": 3,
          "Comment": "Check Stock Price Lambda error",
          "MaxDelaySeconds": 2
        }
}
```

In this error retry snippet, the wildcard `States.ALL` error listens to all the errors in this state to perform a retry. This retry has a few other options -

* `IntervalSeconds` is an integer that specifies the number of seconds before the first retry.
    
* `BackoffRate` which is a property that would multiply the `IntervalSeconds` property to determine the next retry would occur.
    
* `MaxAttempts` defines the maximum number of retries possible.
    
* `MaxDelaySeconds` defines the maximum time in seconds that the retry interval can increase.
    

When the state machine is executed with an error retry in the first state,

![Error retry for Check Stock Price state](https://cdn.hashnode.com/res/hashnode/image/upload/v1694713246550/49571c9e-b97b-4b71-9dcf-f67a26197f76.png align="center")

| Retry | Retry attempt second |
| --- | --- |
| 1st retry | 1s (`IntervalSeconds`) |
| 2nd retry | 3s |
| 3rd retry | 6s |

### Enabling retry with JitterStrategy

In the second *Generate Buy/Sell recommendation* state, enabling retry for all `States.ALL` wildcard with the below retry options -

```javascript
{
    "Retry": [
        {
          "ErrorEquals": [
            "States.ALL"
          ],
          "JitterStrategy": "FULL",
          "Comment": "Buy/Sell recommendation error",
          "MaxAttempts": 5
        }
      ]
}
```

Along with the previously enabled options, the additional property set is - `JitterStrategy` as `FULL`. When `JitterStrategy` is enabled with the value `FULL`, it randomizes delay intervals so that the retry mechanism doesn't retry excessively, this is very powerful especially when Lambda is invoked concurrently.

When the error occurs in the *Generate* *Buy/Sell recommendation* state,

![Error retry for Generate Buy/Sell recommendation state with JitterStrategy](https://cdn.hashnode.com/res/hashnode/image/upload/v1694713962178/6407e7cb-ad75-4d58-9e5f-69165c6c4b9e.png align="center")

| Retry | Retry attempt started after |
| --- | --- |
| 1st retry | 00:00:01.298 |
| 2nd retry | 00:00:01.479 |
| 3rd retry | 00:00:01.752 |
| 4th retry | 00:00:02.986 |
| 5th retry | 00:00:04.213 |

Notice the retry attempts are between 1s and 5s (`MaxAttempts`) which are random when compared to the previous case without `JitterStrategy`.

### Enabling Fail flow

Whenever failing the state machine, it throws an `error` and the `cause` that can customize the error states. But for this flow, enabling a custom error message defined in the state machine for the choice state.

```javascript
{
    "Fail": {
      "Type": "Fail",
      "Error": "Stock prediction failed",
      "Cause": "Unable to proceed as stock prediction to buy or sell stock failed"
    }
}
```

![Fail flow with error message and cause defined for the choice state](https://cdn.hashnode.com/res/hashnode/image/upload/v1694714651241/218d57d2-c823-427f-86ff-c44e040f069f.png align="center")

When the choice state goes through a default state, the state machine fails execution.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1694714766637/6a9d7e21-43a9-4906-97f2-00674b5adb04.png align="center")

When the error occurs, the `fail` flow captures the error as *Stock prediction failed* with the cause *Unable to proceed as stock prediction to buy or sell stock failed*.

## Keep a watch on retries

When working with state machines that have multiple states with retries defined in all or many of them, the retry mechanism would retry until `MaxAttempts`.

#### Q: It's good to retry multiple times

Well, not if the computing brains or the state would reproduce the same error. However, definitely good when retrying would result in success.

#### Q: Multiple retries shouldn't cause interruptions in my state machine execution

If the retries result in a success, it won't interrupt the state machine execution. But if the same error occurs, better to `fail` the execution with passing events to EventBridge or DLQ.

#### Q: Would this be expensive?

In a *Standard workflow*, state machine execution is priced based on every state transition and when retry is configured there would be a state transition for each retry attempt this would be expensive.

Also, the retry attempt is invoking another AWS resource, that execution would also be billed. In this workflow, the Lambda function was invoked multiple times which also accounts for the total workflow cost.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1694715301741/aa4bc989-5b15-41b1-983e-141a42fbe5b0.png align="center")

### Wrap up!

The retry mechanism would help when working with resource-based errors such as `Lambda.ServiceException` or any other AWS Service error based on the task. With `JitterStrategy` enabled, addresses the concurrent retries.

Note, this example was to showcase the features with error retry and fail flow.