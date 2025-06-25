---
title: "Cloud9 environments status monitoring with Step Functions"
seoTitle: "Cloud9 environments status monitoring with Step Functions"
seoDescription: "Cloud9 environment status monitoring using Step Functions & SES email notification for AWS administrators"
datePublished: Sun Jun 19 2022 18:16:36 GMT+0000 (Coordinated Universal Time)
cuid: cldkcop84013l4onvgtbjdzhz
slug: cloud9-environments-status-monitoring-with-step-functions
canonical: https://dev.to/aws-builders/cloud9-environments-status-monitoring-with-step-functions-26lm
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675176244654/d43d121b-4c68-4c5e-a3df-c4c8cbc520a4.jpeg
tags: aws, serverless, stepfunction, aws-step-functions

---

As developers using [AWS Cloud9](https://aws.amazon.com/cloud9/) as their choice of development environment, they often run the cost of the EC2 instance which is used under-the-hood. In this blog, you will understand how to monitor environments which are not in `stopped` state and trigger an SES email to a designated email address. This helps AWS administrators stay on track with Cloud9 environments.

### Understanding the workflow
The [Step Functions](https://aws.amazon.com/step-functions/)'s state machine uses [AWS Cloud9](https://aws.amazon.com/cloud9/) and [Amazon SES](https://aws.amazon.com/ses/) APIs which are integrated with [Step function's SDK integration](https://docs.aws.amazon.com/step-functions/latest/dg/supported-services-awssdk.html).
The different steps uses - [AWS Cloud9](https://aws.amazon.com/cloud9/) SDK API `listEnvironments` and `describeEnvironmentStatus` along with [Amazon SES](https://aws.amazon.com/ses/) SDK API `sendEmail`. So the state machine's IAM execution role would need access to perform the same actions.
![Complete workflow of the state machine](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176226389/0fa87482-8a51-4fce-ac83-f6520fa6c0bf.png)
 
#### ListEnvironments state
![Snapshot of ListEnvironments state machine workflow and JSON definition](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176228709/44b61bf7-140a-43e5-8b1a-1e797ba8df05.png)
Using [AWS Cloud9](https://aws.amazon.com/cloud9/) SDK API [`listEnvironments`](https://docs.aws.amazon.com/cloud9/latest/APIReference/API_ListEnvironments.html), we can list all the environment's IDs and the API restricts upto 25 max items then they would have to be paginated. This state machine is using the first page (first 25 records) only from the API response.

#### Map state
In state machines, we could use [map state](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-map-state.html) for looping items. In this map state, each of the environment ID is used to fetch the status and do the needed processing. 
```json
"Map": {
      "Type": "Map",
      "Iterator": {
        "StartAt": "DescribeEnvironmentStatus",
        "States": {
          "DescribeEnvironmentStatus": {
            "Type": "Task",
            "Parameters": {
              "EnvironmentId.$": "$.value"
            },
            "Resource": "arn:aws:states:::aws-sdk:cloud9:describeEnvironmentStatus",
            "ResultPath": "$.envStatus",
            "Next": "Choice"
          },
          "Choice": {
            "Type": "Choice",
            "Choices": [
              {
                "Not": {
                  "Variable": "$.envStatus.Status",
                  "StringEquals": "stopped"
                },
                "Next": "SendEmail"
              }
            ],
            "Default": "Pass"
          },
          "SendEmail": {
            "Type": "Task",
            "End": true,
            "Parameters": {
              "Destination": {
                "ToAddresses": [
                  "zachjonesnoel@mailinator.com"
                ]
              },
              "Message": {
                "Body": {
                  "Html": {
                    "Charset": "UTF-8",
                    "Data.$": "States.Format('[{}] Environment ID {} is {}', $.envStatus.Status, $.value, $.envStatus.Message)"
                  }
                },
                "Subject": {
                  "Data": "Your Cloud9 Environment status!!!"
                }
              },
              "Source": "test@zachjonesnoel.com"
            },
            "Resource": "arn:aws:states:::aws-sdk:ses:sendEmail"
          },
          "Pass": {
            "Type": "Pass",
            "End": true
          }
        }
      },
      "End": true,
      "ItemsPath": "$.envs.EnvironmentIds",
      "Parameters": {
        "index.$": "$$.Map.Item.Index",
        "value.$": "$$.Map.Item.Value"
      }
    }
```

#### DescribeEnvironmentStatus state
![Snapshot of DescribeEnvironmentStatus state machine workflow and JSON definition](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176230978/5538accd-f63c-4537-adbc-6d7b82824636.png)
With each environment ID, we can get the status of that environment with [AWS Cloud9](https://aws.amazon.com/cloud9/) [`describeEnvironmentStatus` SDK API](https://docs.aws.amazon.com/cloud9/latest/APIReference/API_DescribeEnvironmentStatus.html). This responds with one of the status - 
+ `connecting` - Environment with connection in progress.
+ `creating` - For an environment which is creation in progress. 
+ `deleting` - Whenever the CloudFormation stack is deleting the environment and the other AWS resources used under-the-hood.
+ `error` - When the environment is in an error state.
+ `ready` - For a successfully connected environment which is ready for usage.
+ `stopped` - Based on the idle time stop, the environment would be stopped.
+ `stopping` - Whenever the stop is initiated.

#### Choice state
![Choice state based on the status response](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176233336/b4240048-c01e-46c3-a54a-67ced9487191.png)
 With response status from `describeEnvironmentStatus`, a choice condition is used to check if the status is anything other than `stopped` then trigger the next step else pass.

#### SendEmail state
![SendEmail state workflow and JSON definition](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176234908/8a028c1c-a0ca-4205-ba1d-4d47d203e434.png)
The [`sendEmail` SDK API](https://docs.aws.amazon.com/ses/latest/APIReference-V2/API_SendEmail.html) of [Amazon SES](https://aws.amazon.com/ses/) is used to send email from a verified identity on SES. This uses HTML based body. For ensuring the email body is well formatted, [`States.Format` intrinsic function](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-intrinsic-functions.html) is used. 

### Workflow executions
![Complete execution walk-through](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176237325/f337ca36-0a51-4d34-8f31-271b88eedebc.gif)
 
This state machine execution walks though the different states and also the iterations of each environment ID. There are cases, where the email has been triggered with *SendEmail state*.

When a new environment is created, an email with the status `creating` is sent along with the message.
![New environment in creation](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176240352/d82dcee6-28c3-4db0-a0fc-b52403819aba.png)

When the environment is successfully created, or the state is successfully connected it's status is changed to `ready`.
![Environment is successfully created and ready to use](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176241876/f1b59057-cfc7-4492-bd13-e7794034e3da.png)

When the environment is deleted, this invokes a CloudFormation stack delete and email with the following body is sent.
![Environment with deleting state triggered email](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176243248/d8621d80-0ef8-43f7-ad69-39e9be59f706.png)
 
### Conclusion
AWS Step Functions helps orchestration of various workflows. This involves some administrative monitoring workflows also automated helping to monitor different status changes in Cloud9 environment and notifying the needed users via email leveraging Amazon SES.
And as part of cost-optimizaton, the *pass state* could be avoided. 