---
title: "Using GitHub actions - Deploy to AWS & SNS Publish"
datePublished: Sat Dec 04 2021 18:40:26 GMT+0000 (Coordinated Universal Time)
cuid: cldu831ae00i1vpnv40kv18z1
slug: using-github-actions-deploy-to-aws-and-sns-publish
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675773177351/2d996f77-b4e4-46fe-9708-c413450161a5.jpeg

---

[GitHub actions](https://github.com/features/actions) enables developers/DevOps engineers to smartly incorporate CI/CD pipelines, perform tasks on actions on different branches and many more things. In the blog, we will look into GitHub actions which uses two workflows - 
1. [SAM Pipeline to deploy to AWS environments](#sam-pipeline)
2. [SNS Publish on commits / delete branch](#notify)

This helps in deploying SAM applications to AWS with Pipeline to ensure multiple staging environments to be deployed and handle feature branches. Along with it, you can do more things on AWS end of there is a event trigger via SNS to process each commit action.

---

### My Workflow
#### SAM Pipeline to deploy to AWS environments <a name="sam-pipeline"></a>
[SAM Pipeline](https://aws.amazon.com/blogs/compute/introducing-aws-sam-pipelines-automatically-generate-deployment-pipelines-for-serverless-applications/) enables leveraging SAM and GitHub actions to deploy your Serverless application to AWS environment. This leverages `sam build` and `sam deploy` commands performed via SAM CLI in the GitHub actions (Ubuntu) environment. 
The workflow defines jobs for -
+ test : Checking if the GitHub event is *push*.
+ delete-feature : If the GitHub event is *delete* on a feature branch with the name starting with *feature-**, it deletes that particular feature branch with `sam delete`.
+ build-and-deploy-feature : If the GitHub event is *push* on a feature branch with the name starting with *feature-**, it deploys the app to AWS with the code from feature branch with `sam deploy`.
+ build-and-package : If the GitHub event is *push* on a main branch, it packages the the app for two stages - **dev** and **prod** and uploads the artifacts zip files to S3 bucket with `sam package` command.
+ deploy-testing : The code will use the Stage 1 - **dev** and deploys the Serverless application to AWS with `sam deploy`.
+ integration-test : If the branch is *main*, you can run specific integration tests via bash scripts. And if the tests are passed, it proceeds to the next step.
+ deploy-prod : If the integration tests are passed, the complete code from *main branch* is deployed to AWS with `sam deploy`.

![SAM Pipeline](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773169924/47429abf-7d0d-4b61-8c47-655ff5f12a1a.png)
![Action details](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773171494/543cb71e-072d-4b22-a208-7d94b01b8fb9.png)
 

#### SNS Publish on commits / delete branch <a name="notify"></a>
The workflow is used to notify with [AWS Simple Notification Service (SNS)](https://aws.amazon.com/sns/) by pushing to a SNS topic. This workflow uses `Danushka96/sns-action@v2` which performs `SNS:PUBLISH` to send message to the SNS topic.

![SNS Publish](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773173160/9b8a2f6d-de09-4d70-94e2-ef9b4280e5d6.png)
![Action description](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773175335/2d665b4f-0977-4a82-983a-938488cfc72a.png)

---

### Submission Category: 
**DIY Deployments** which eases deployment of SAM app via SAM Pipelines and also using SNS publish, we can get creative with ways of additional workflows on AWS to be triggered.

---

### Yaml File or Link to Code
The blog post implementation of 2 workflows is implemented with `pipeline.yaml` and `sns.yml`

{% github zachjonesnoel/sam-github-actions/ %}

---

### Additional Resources / Info
This project uses several GitHub actions - 
+ [actions/checkout@v2](https://github.com/actions/checkout)
+ [actions/setup-python@v2](https://github.com/actions/setup-python)
+ [aws-actions/setup-sam](https://github.com/actions/checkout-sam)
+ [aws-actions/configure-aws-credentials@v1](https://github.com/aws-actions/configure-aws-credentials)
+ [actions/upload-artifact@v2](https://github.com/actions/upload-artifact)
+ [actions/download-artifact@v2](https://github.com/actions/checkoutom/actions/download-artifact)
+ [Danushka96/sns-action@v2](https://github.com/actions/checkout)

