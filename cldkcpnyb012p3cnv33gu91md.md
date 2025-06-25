---
title: "IAM Credentials Rotation Automation with Step Functions"
datePublished: Thu Mar 03 2022 17:44:50 GMT+0000 (Coordinated Universal Time)
cuid: cldkcpnyb012p3cnv33gu91md
slug: iam-credentials-rotation-automation-with-step-functions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675176289545/47307a76-46a6-4b28-a56d-4a2b3a2ac875.png

---

The best practices of using [IAM](https://aws.amazon.com/iam/) credentials is to have them rotated frequently (eg. every 90 days, every 6 months). This is because if credentials are compromised, you can ensure that the existing credentials are deleted, and a new credential is issued. Another best practice includes having the credentials stored securely on [Secrets Manager](https://aws.amazon.com/secrets-manager/), so that the credentials are never hard-coded on the application end.

In this blog, we will see how [Step Functions](https://aws.amazon.com/step-functions/) helps in orchestrating a workflow which implements the rotation of credentials (creating a new programmatic access credentials and deleting existing programmatic access credentials), updates the secret stored on Secrets Manager, and notifies the admin that a new credential is generated and has updated the secret as well. 

Alternatively, Secrets Manager natively supports the feature of [secrets rotation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html). However, in that case, based on the CRON expression, a specific [Lambda Function](https://aws.amazon.com/lambda/) is executed. This blog-post is to demonstrate Step Functions' SDK integration capabilities to orchestrate the same flow.

### Understanding the workflow
The Step Functions workflows integrates with the services for the respective SDK APIs 
+ IAM - `iam:listAccessKeys`, `iam:deleteAccessKey` and `iam:createAccessKey`
+ Secrets Manager - `secretsmanager:updateSecret`
+ SES - `ses:sendEmail`

*Note : The IAM role created by Step Functions would also require the above mentioned fine grained access to the specific actions of the respective resources.*

<center>
![Step Functions workflow with different states of SDK integration](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176276708/128beaae-34a5-4eb6-b5f0-b7d2787fcc6d.png)
</center>

#### ListAccessKeys state

<center>
![State definition for ListAccessKeys](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176278811/aa97349b-f482-4f1e-bdce-6a9737a98520.png)
</center>

In this state, the IAM SDK API `iam:listAccessKeys` is used to list the available *active credentials* of the user specified. 

#### DeleteAccessKey state

<center>
![State definition for DeleteAccessKey](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176280146/563182b2-639c-4a2e-a733-ba7577d376b9.png)
</center>

In this state, the IAM SDK API `iam:deleteAccessKey` is used to delete the available *active credential* of the user specified with the access key ID. 

#### CreateAccessKey state

<center>
![State definition for CreateAccessKey](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176281289/57b108f2-460a-44ca-a06e-fe16afd0e035.png)
</center>

In this state, the IAM SDK API `iam:createAccessKey` is used to create a new access key and secret key for the specified user. 

#### UpdateSecret state

<center>
![State definition for UpdateSecret](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176282365/e808b0b5-122e-4b80-bffd-ff2f02ad5e25.png)
</center>

In this state, the Secret Manager SDK API `secretsmanager:updateSecret` is used to update the credential stored on Secret Manager.

#### SendEmail state

<center>
![State definition for SendEmail](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176284353/10b3c81c-d0c0-4e02-9a3d-26a9ab7c6056.png)
</center>

In this state, the Simple Email Service (SES) SDK API `ses:sendEmail` is used to notify the users via email that a new IAM credential has been updated on the Secret Manager. Alternatively, `ses:sendTemplatedEmail` is also used to send a formatted email, but that would require a creation of template prior to this workflow execution. The email body is designed to be a general HTML text to ensure that no other specific credentials is revealed over emails.

The complete JSON of State Machine is - 
```JSON
{
  "Comment": "A description of my state machine",
  "StartAt": "ListAccessKeys",
  "States": {
    "ListAccessKeys": {
      "Type": "Task",
      "Parameters": {
        "UserName.$": "$.userName"
      },
      "Resource": "arn:aws:states:::aws-sdk:iam:listAccessKeys",
      "Next": "DeleteAccessKey",
      "ResultPath": "$.listAccessKey"
    },
    "DeleteAccessKey": {
      "Type": "Task",
      "Next": "CreateAccessKey",
      "Parameters": {
        "AccessKeyId.$": "$.listAccessKey.AccessKeyMetadata[0].AccessKeyId",
        "UserName.$": "$.userName"
      },
      "Resource": "arn:aws:states:::aws-sdk:iam:deleteAccessKey",
      "ResultPath": "$.deleteAccessKey"
    },
    "CreateAccessKey": {
      "Type": "Task",
      "Parameters": {
        "UserName.$": "$.userName"
      },
      "Resource": "arn:aws:states:::aws-sdk:iam:createAccessKey",
      "Next": "UpdateSecret",
      "ResultPath": "$.createAccessKey"
    },
    "UpdateSecret": {
      "Type": "Task",
      "Parameters": {
        "SecretId.$": "$.secretName",
        "SecretString.$": "States.JsonToString($.createAccessKey.AccessKey)"
      },
      "Resource": "arn:aws:states:::aws-sdk:secretsmanager:updateSecret",
      "Next": "SendEmail",
      "ResultPath": "$.updateSecret"
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
              "Data": "The IAM Credential is rotated and updated on Secrets Manager"
            }
          },
          "Subject": {
            "Data": "New IAM Credential updated!!!"
          }
        },
        "Source": "test@zachjonesnoel.com"
      },
      "Resource": "arn:aws:states:::aws-sdk:ses:sendEmail",
      "ResultPath": "$.sendEmail"
    }
  }
}
```

### Workflow execution
When all the states have executed successfully, the end result of the State Machine would mean that your IAM user's programmatic credentials are updated on *Secrets Manager*.

![A successful workflow execution](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176286201/a6b626e2-374f-4bfa-92f3-1220415a0f5b.png)

Also, an email notification is sent to designated users/admins as this is an automation process.

![The received email from the successful execution of SendEmail](https://cdn.hashnode.com/res/hashnode/image/upload/v1675176288137/6b02b538-6855-441f-abe0-d55e0570aa12.png)

Now, your applications can always use the secrets from Secrets Manager without the hurdle of updating it every time the IAM credentials are rotated. 

<small>*For all the security reasons, the complete workflow walkthrough is not provided as that exposes the IAM credentials.*</small>

### Conclusion
This is an orchestration of IAM credentials rotation which ensures your application is ways using secured credentials that can be updated in time of need. Even though this blog-post features only the Step Function module, this Step Function execution could be started from an event from *EventBridge* making it an event-triggered automation. 