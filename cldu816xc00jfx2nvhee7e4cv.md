---
title: "CloudWatch RUM for all insights"
datePublished: Sun Jan 16 2022 12:03:15 GMT+0000 (Coordinated Universal Time)
cuid: cldu816xc00jfx2nvhee7e4cv
slug: cloudwatch-rum-for-all-insights
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675773090971/f00c1cc7-42a6-45f7-8420-f65b8e7acb7a.jpeg

---

[CloudWatch RUM](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-RUM.html) was recently launched during re:Invent 2021 which provides insights to your web-application about certain metrics based on user-actions and errors for debugging.
You can read about the [announcement](https://aws.amazon.com/about-aws/whats-new/2021/11/amazon-cloudwatch-rum-applications-client-side-performance/).
{%twitter 1465384891869138946 %}

### Key takeways from the blog 
+ [Understanding CloudWatch RUM ](#understanding-rum)
+ [Setting-up RUM](#setting-up-rum)
+ [Different telemetry data](#data)

### Understanding CloudWatch RUM <a name="understanding-rum"></a>
**CloudWatch Real-User Monitoring (RUM)** is a monitoring functionality facilitated by CloudWatch which has always been the monitoring tool on AWS. RUM enables developers and DevOps engineers to understand the issues/errors encountered on the web-app and also insights such as which *device* or *browser* or *location* had the error. Additionally, there is performance insights and the time taken for a file to load on the client side along with the geographically information. 
As [Jeff Barr](https://twitter.com/jeffbarr) quotes it, it's that simple to implement on the client side.
> You simply register your application, add a snippet of JavaScript to the header of each page, and deploy.

The **CloudWatch RUM** consolidated and provides dashboard which gives you a detailed insights such as - *page load speed*, *geographic info*, *devices*, *browsers*, *average load during the time*, *user journey*. All this with just a snippet of JS to page.

### Setting-up RUM <a name="setting-up-rum"></a>
**CloudWatch Real-User Monitoring (RUM)** setup can be summarized with the 3 steps - 
+ [Add app monitor](#add-app)
+ [Adding the JS snippet to your web app](#snippet)
+ [Monitor the web-app from CloudWatch console](#monitoring)
![Setting-up RUM](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773057977/c15ba9bd-434e-4b29-9864-71a9230ea725.png)

#### Add app monitor <a name="add-app"></a>
While adding a new app monitor, you would have to specify details such as - *app monitor name*, *app domain* and an option to include the sub-domains of the *app domain*. 
You can choose what all data is been collected and stored as telemetry data for the dashboards.
![Configs](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773059640/fad839be-483b-4dd8-b0c8-5933a9712632.png)
The telemetry data is stored only for *30 days*, so if you would wish to store the logs, you can create a CloudWatch log event which captures and stores all these datas.
![Data storage](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773061132/5e8c080b-9958-44e6-a8a9-e923f8255ce1.png)
CloudWatch RUM needs authorization to access AWS resources, for which Amazon Cognito Identity Pools are used.
![Authorization](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773062464/16449642-2ef2-4c54-8cac-896e540080bd.png)
The telemetry data of the web-pages can also be fine grained to the choice of *all pages*, *specific pages only* or *exclude certain pages*.
![Configure pages](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773064646/a73c9007-c3c2-44e2-a5b9-7ca8cd1feafd.png)

#### Adding the JS snippet to your web app <a name="snippet"></a>
Once you save the configurations and add the app monitor, you would be presented with a JavaScript snippet. 
![JS snippet](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773066003/da310690-b803-4a61-9f3a-260407b21fa5.png)
As simple as it is, this just needs to be added to the `<head>` of your web-page which is sending telemetry data using the `<script>` tag. 
 
#### Monitor the web-app from CloudWatch console <a name="monitoring"></a>
Once set-up and moved your web-page to the server, you can navigate to your CloudWatch console to view the dashboard. 
![Overview dashboard](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773067562/f2392761-6200-4555-aeb4-5b62149375a9.png)

### Different telemetry data <a name="data"></a>
From your CloudWatch console, you can view different types of insights. The previous section shows the overview of your app monitor.

Page load speed data for 1 month (Dec 17th 2021 - Jan 16th 2022)
![Page load speed](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773070055/74038c95-44b0-4f8d-b0ae-14c3f868f90f.png)
Different web vitals for 1 month (Dec 17th 2021 - Jan 16th 2022)
![Different web vitals](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773071508/2fdfa675-b094-46ec-a4d8-8b05e88825c0.png)
Different web browsers used for 1 month (Dec 17th 2021 - Jan 16th 2022)
![Different web browsers](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773073685/2a5e048e-d030-44da-8ae9-2a6726cef6b8.png)
Different devices used for 1 month (Dec 17th 2021 - Jan 16th 2022)
![Different devices](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773075928/cc24ddb1-fb24-4245-968c-ed2feb8a8fae.png)
Different locations with page load time for 1 month (Dec 17th 2021 - Jan 16th 2022)
![Different locations with page load time](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773077266/14a4e70f-bb3f-4f3f-9f61-dc0d00e70e57.png)
<!--![all positive](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773078651/8aecbfab-0019-493e-8953-06a52299fc3a.png)-->
Different locations with sessions for 1 month (Dec 17th 2021 - Jan 16th 2022)
![Different locations with sessions](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773081683/c6411a2c-e300-4a04-94f0-a692075bea0e.png)
You can view the details at a country filter of sessions for 1 month (Dec 17th 2021 - Jan 16th 2022)
![Country filter](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773084361/0d690d98-4eea-414e-b322-c57b9815f6bf.png)
For multiple pages, you can even get the user-journey.
![User-journey](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773085870/7d0ed364-03e3-4abd-8c66-741e2681d319.png)
For the sessions with errors, you can view what the error was and also the data time of occurrence along with device details. 
![Error session](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773088015/dcfe0b8b-b898-4374-a7c2-685ffaaf6070.png) 
![Error sessions details](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773089589/ef123795-0734-4279-9e76-31167b240e96.png)

### Pricing
The free trial has 1 million RUM events which is across the account. And this is only for the first time when RUM is used. Post which, $1 per 100k RUM events.
You can view the detailed [pricing details](https://aws.amazon.com/cloudwatch/pricing/).

### Wrap-up
**CloudWatch RUM** has provided a simplistic approach to web-app insights. The above sample logs and telemetry data are of my personal landing page https://zachjonesnoel.com which has been up and running from Dec 01 2021 and this dashboard has facilitated me to understand what and how is the performance.
Jeff Barr writes about the [New – Real-User Monitoring for Amazon CloudWatch](https://aws.amazon.com/blogs/aws/cloudwatch-rum/).
