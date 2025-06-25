---
title: "AWS SAM app with MongoDB Atlas Data APIs"
datePublished: Sun Jan 09 2022 15:39:56 GMT+0000 (Coordinated Universal Time)
cuid: cldu81og900jqx2nvfmg1cf8y
slug: aws-sam-app-with-mongodb-atlas-data-apis
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675773098992/449ebfb4-ef01-4529-9169-6fc85cbdf371.png

---

This is the implementation of a typical web app backend where the MongoDB Atlas is leveraged to store data.

### Overview of My Submission
The SAM app uses MongoDB Atlas for database choice which contains two collections - `shield_agents` and `shield_locations`. This stores the S.H.I.E.L.D info such as the database of all agents and non classified locations. The operation of `INSERT` and `GET` is integrated with [MongoDB Atlas Data APIs](https://docs.atlas.mongodb.com/api/data-api/) where the API credentials are created and passed into AWS Lambda fns as *environment variables* with CloudFormation Parameters. The APIs from SAM app are exposed with *AWS API Gateway*. Also have the Search enabled with indexes.
![MongoDB dashboard](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773098992/449ebfb4-ef01-4529-9169-6fc85cbdf371.png)
 
### Submission Category: 
**Choose Your Own Adventure**, the first time I'm using MongoDB and Data APIs so went a step to adapt it into my usual tech-stack (AWS Serverless) with a [AWS SAM application](https://aws.amazon.com/serverless/sam/). 

### Link to Code
{% github https://github.com/zachjonesnoel/shield-database %}

### Screenshots
Creating an agent with `insertOne` API
![Create agent](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773100616/b2c08bb9-7c54-481a-9d22-7ba07f8d75b1.png)
Getting one of the created agent with `findOne` API
![Get created agent](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773102797/9ea12e3d-0d11-4626-9f4e-b0871768cea2.png)
Creating a location with `insertOne` API
![Create location](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773104363/b91756ac-5d06-4e32-b426-223e02eb1537.png)
Getting one of the created location with `findOne` API
![Get created location](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773105980/7522c47f-b7b8-4d9a-8ac6-413723236d3d.png)
[MongoDB Atlas Search](https://docs.atlas.mongodb.com/atlas-search/) helps in creating an `index` for a collection and using the index to query and check if the filter pattern matches any record. The matching records also return a match score.
![Atlas Search](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773108433/88d5acd2-4a0a-4cde-bbc4-f7b7ad065859.png)
[MongoDB Atlas Charts](https://docs.mongodb.com/charts/) can help you visualize data. 
No of locations - 
![MongoDB Atlas Charts - locations](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773110878/ef7f2d75-78d5-4034-8c0b-b9254108ef06.png)
Agents with security clearance level- 
![MongoDB Atlas Charts - security level](https://cdn.hashnode.com/res/hashnode/image/upload/v1675773112200/342e0d31-bea8-4959-9c5b-a4586f742ca4.png)
 

### Additional Resources / Info
+ [MongoDB Atlas Data API](https://docs.atlas.mongodb.com/api/data-api/)
+ [MongoDB Atlas Search](https://docs.atlas.mongodb.com/atlas-search/)
+ [MongoDB Atlas Charts](https://docs.mongodb.com/charts/)
+ [AWS SAM application](https://aws.amazon.com/serverless/sam/)
