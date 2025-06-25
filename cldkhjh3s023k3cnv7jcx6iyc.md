---
title: "Share events for your Lambda functions"
datePublished: Tue Mar 22 2022 14:07:21 GMT+0000 (Coordinated Universal Time)
cuid: cldkhjh3s023k3cnv7jcx6iyc
slug: share-events-for-your-lambda-functions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675184398985/71d0a25e-221f-4f09-a260-217d83f467c2.jpeg

---

Recently, [AWS Lambda functions](https://aws.amazon.com/lambda/) enabled the feature of sharing events for Lambda functions amongst different IAM users. You can read about the announcement [here](https://aws.amazon.com/about-aws/whats-new/2022/03/aws-lambda-console-test-events/).

### Creating sharable test events
For creating a new sharable test events, from the AWS Lambda function navigate to the *Test* section.
![Test in AWS Lambda console](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184385288/0fc09bce-9721-4664-81db-31bb54910d78.png)
In the *Test* section, when creating a new event, select the option of *Sharable* and define the event JSON and event name. 
![Create shared events](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184387597/93281641-89fe-4c05-9687-bbfdbbbd14c2.png)
Once the events are created, you can view both private events and the sharable events on AWS Lambda functions console.
![Viewing shared tests](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184389002/3496a448-4dd4-4fe7-b3f0-2f66cd0451f1.png)

### Shareable event on EventBridge
Any of the Lambda function which has *sharable events*, you can head out to [Amazon EventBridge](https://aws.amazon.com/eventbridge/) console under *lambda-testevent-schemas* you can view, edit the shared events in the context of AWS Lambda function.
![Viewing shared events in EventBridge](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184391230/b22b2940-d8d1-46bc-8b33-061686669bc7.jpeg)
And each of the *sharable event*, you can view the details and also different available versions of the event itself.
![Detailed event in EventBridge](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184392495/d17fe173-5201-41b0-b3e1-e55c4c8af729.png)
The JSON structure on EventBridge Schema registry follows the *OpenAPI format*.
```JSON
{
  "openapi": "3.0.0",
  "info": {
    "version": "1.0.0",
    "title": "Event"
  },
  "paths": {},
  "components": {
    "schemas": {
      "Event": {
        "type": "object",
        "required": [
          "key1",
          "key2",
          "key3"
        ],
        "properties": {
          "key1": {
            "type": "string"
          },
          "key2": {
            "type": "string"
          },
          "key3": {
            "type": "string"
          }
        }
      }
    },
    "examples": {
      "hello-world": {
        "value": {
          "key1": "value1",
          "key2": "value2",
          "key3": "value3"
        }
      },
      "test": {
        "value": {
          "key1": "value1",
          "key2": "value2",
          "key3": "value3"
        }
      }
    }
  }
}
```

### Editing events
From the [Amazon EventBridge](https://aws.amazon.com/eventbridge/) console, you can choose the different version of the event and modify JSON event. Since this is following *OpenAPI*, you would have to define the schema and the properties which are required along with the event JSON itself. 

```JSON
{
  "openapi": "3.0.0",
  "info": {
    "version": "1.0.0",
    "title": "Event"
  },
  "paths": {},
  "components": {
    "schemas": {
      "Event": {
        "type": "object",
        "required": [
          "key1",
          "key2",
          "key3"
        ],
        "properties": {
          "key1": {
            "type": "string"
          },
          "key2": {
            "type": "string"
          },
          "key3": {
            "type": "string"
          },
          "key4": {
            "type": "string"
          }
        }
      }
    },
    "examples": {
      "hello-world": {
        "value": {
          "key1": "value1",
          "key2": "value2",
          "key3": "value3",
          "key4": "value3"
        }
      },
      "test": {
        "value": {
          "key1": "value1",
          "key2": "value2",
          "key3": "value3"
        }
      }
    }
  }
}
```
In this demo, the *version 2* of the event is selected and is modified to support an additional property `key4`. 
![Editing events](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184394681/3c0231bc-6c2c-4ab1-9477-b32dec860409.png)
Once *saved as new version*, the console creates a latest version with the modified event. 
![New edited event available on EventBridge console](https://cdn.hashnode.com/res/hashnode/image/upload/v1675184396717/0e7bdfe3-4307-4ef9-a458-28b747f0c402.png)

### Deleting sharable event
If you wish to delete a specific version, you have the full control to do so from EventBridge console. And also you can delete all the versions also and then the Lambda function will not be listing the shared event also.

### How this helps developers
Often at times, in different staging or development AWS Accounts, developers would have to share the events for AWS Lambda functions to be tested independently for local testing and ensuring the *right event JSON* is tested for the expected working of AWS Lambda functions.
Previous, developers had to locally save that event JSON and share it out of AWS environment but now with the *sharable events*, developers can collaborate better! And the process of sharing the events for local AWS Lambda function testing is eased up. This is a huge announcement for developers and improving the developer experience.