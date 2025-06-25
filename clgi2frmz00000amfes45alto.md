---
title: "Serverless development with Amplify Studio"
seoDescription: "Easing full-stack development with AWS Amplify Studio and understanding how each pillar of Amplify Studio could be used in a front-end source code."
datePublished: Fri Apr 14 2023 14:20:32 GMT+0000 (Coordinated Universal Time)
cuid: clgi2frmz00000amfes45alto
slug: serverless-development-with-amplify-studio
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1681568058171/327e92d7-f765-4827-ae75-697fb9736fb7.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1681568372003/8a9098fb-df81-4b76-8d53-2d8034466202.png
tags: aws, dynamodb, amazon-s3, appsync, awsamplify

---

As full-stack developers, we often love to develop applications out of the box with tools that can visually design your application's UI and define your data and API layers that can seamlessly integrate with the application's UI.

[AWS Amplify Studio](https://aws.amazon.com/amplify/studio/) amplifies your development with front-end and back-end by provisioning the right AWS resources under the hood for the right purposes and also the ability to easily integrate into the design.

## Working with data

Amplify Studio makes it really easy to define and manage your data irrespective of the environment it's used in.

### Defining your data

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681407800875/165527da-53ce-466b-9519-5a8db803871a.png align="center")

Amplify Studio's Data allows you to define [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) keeping in mind the properties with the right type and also powered with [AWS AppSync](https://aws.amazon.com/appsync/) where which generates a GraphQL schema under the hood.

```json
enum TopicLevelEnum {
  INTRODUCTORY
  INTERMEDIATE
  ADVANCE
}

type InfoCard @model @auth(rules: [{allow: public}]) {
  id: ID!
  title: String!
  short_description: String!
  long_description: String!
  thumbnail_url: AWSURL!
  categories: [String!]
  services: [String!]
  topic_level: TopicLevelEnum
  aws_docs_url: String!
  aws_workshow_url: String
  community_url: String
  aws_samples_url: String
  dislikes: Int
  url: String
  s3_thumbnail: String
  likes: Int
}

type Service @model @auth(rules: [{allow: public}]) {
  id: ID!
  service_name: String!
  category_name: String!
  logo_url: AWSURL!
  url: String
  s3_logo: String
}
```

Now that data is defined and the AWS resources - AppSync API and DynamoDB tables for the same are created with `@model` directives by AppSync. Since the API is using public read, `@auth(rules: [{allow: public}])` is part `InfoCard` and `Service` this will ensure that the auto-generated CRUD APIs for them will allow public access.

### Generating data

The content management feature of Amplify Studio is elegant for ensuring an admin/content creator role to create or update content that the front-end application would be consuming. This uses the data definition based on your GraphQL schema and DynamoDB schema with a form-based input.

![Create Service UI from Amplify Studio](https://cdn.hashnode.com/res/hashnode/image/upload/v1681561327503/45a632c7-01bb-499d-a3ce-6b1a83473dcf.png align="center")

Amplify Studio also has an *underrated feature* that developers would love when using this during the course of development, Amplify Studio has auto-generate data which is handy for the development phase where developers need not worry about "there isn't enough data to display on the front-end" instead generate up to 100 records with the feasibility of setting some constraints so that you get valid data.

![Amplify Studio with the feature of auto-generate data](https://cdn.hashnode.com/res/hashnode/image/upload/v1681561809526/24daed44-e17e-4041-a359-353f113fbae1.png align="center")

### Test your data

With the data populated, you would need to ensure the GraphQL APIs with AppSync are working as expected. Amplify Studio also features a GraphQL editor to simulate and test the APIs.

![GraphQL simulator in Amplify Studio for testing APIs](https://cdn.hashnode.com/res/hashnode/image/upload/v1681562332807/9507fda4-173f-4f3d-abcc-e6b2c3d42c51.png align="center")

The simulator provides the API docs - where you can navigate the different `queries`, `mutations` or `subscription`.

## Amplify Storage

Amplify Storage let's you manage the media files and assets which are stored on Amazon S3. Amplify Storage will first create the Amazon S3 bucket where you can set the authorization rules for user to `upload`, `view` or `delete` the objects (files in this case) on S3 bucket.

### Managing files on Storage

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1681567644277/87632fb3-017f-404e-8756-dd8dee43c671.png align="center")

From Amplify Storage, you can navigate across different levels of folders and files and upload the files or create folders. Amplify Studio also provides your with a snippet of code that uses Storage APIs to get the file.

```javascript
const file = await Storage.get("aws-amplify/auth/aws-amplify-auth.md", {
    level: "public"
});
```

## **Figma design to code**

Using the [**getting started Figma design**](https://www.figma.com/community/file/1047600760128127424) from the community and the components available in this design uses the design guidelines which is a subset of [**AWS Amplify UI Kit**](https://ui.docs.amplify.aws/) that has a collection of UI components for different UI frameworks both web and mobile.

With my limited knowledge of Figma designing and using the pre-defined components and modifying the components as per my requirement, I've successfully created three components - `ServiceCard`, `FeatureCard` and `InfoCardDetails` .

![Figma design with the components created using pre-defined Amplify components](https://cdn.hashnode.com/res/hashnode/image/upload/v1681405674242/eea9dfb6-fe52-4f16-8636-598ccb2a1212.png?auto=compress,format&format=webp align="left")

On successfully authorizing AWS Amplify Studio to access Figma designs from my Figma account, the UI Library on Amplify Studio lists all the components ready to be configured and imported into your Amplify project.

### **Component configuration**

![Detected ServiceCard component on Amplify Studio](https://cdn.hashnode.com/res/hashnode/image/upload/v1681406439696/0160b36e-c998-4d82-8f3a-2ba6b079f6e1.png?auto=compress,format&format=webp align="left")

`FeatureCard` is now ready to be configured with the data that is defined. Amplify Studio provides a tree for all the elements created in this component and configuring different component properties.

![Component to Data model mapping](https://cdn.hashnode.com/res/hashnode/image/upload/v1681563346972/eab105f1-6407-47de-af77-6475c6f0deb3.png align="center")

Firstly, map the component to the data model that is used in the component.

![Setting child properties of the component from Amplify Studio](https://cdn.hashnode.com/res/hashnode/image/upload/v1681564052897/91b8966d-7995-479b-b31a-61b30fc92d1e.png align="center")

Set each of the child properties with the data from the defined data model. In the above screenshot, you can see the title of the card is set with `infoCard.Title` to the `label` property of the child component.

![Configuring properties for event handlers](https://cdn.hashnode.com/res/hashnode/image/upload/v1681564433437/08d32684-5437-4d57-9495-4123726c6f1d.png align="center")

In case of `InfoCardDetails` component, the data model defines URLs for documentation, samples and workshop. For those to be functional as a button `onClick` event, Amplify Studio allows those configurations where you can specify the target by selecting `Open URL in new tab` and also mapping the equivalent data from `InfoCard` data model.

### Collection components

In the component configuration page, click on *Create collection* button.

![Collections configuration on Amplify Studio](https://cdn.hashnode.com/res/hashnode/image/upload/v1681564780015/3064cb6c-358a-4948-8d48-71e373cc7643.png align="center")

The collections can be configured with properties that can make the collections UI really customizable in terms of how they would appear as a - `List` or `Grid`, ordering, spacing, alignment, enabling `search` functionality in the collection and also `pagination` with the page size.

![Configuring collection properties in Amplify Studio](https://cdn.hashnode.com/res/hashnode/image/upload/v1681565407298/4b9dbe47-9f5f-4d53-b920-8688c5c59cbf.png align="center")

The collections could also be configured with `filters` which are applied to the collections and also define the `sort` conditions.

## Sync Amplify Studio with local IDE

With the data and UI configurations done on Amplify Studio, these changes can be synced to your local IDE with Amplify CLI.

```powershell
amplify pull --appId your-amplify-project-id --envName your-env
```

This will create all the `amplify` related files in your local project folder with `amplify` backed and resources configurations under `/amplify` folder. In your `src` folder, the UI components and data model related files are generated in `/ui-components` folder and `/models` folder respectively.

### Importing and using components

Installing the needed npm dependencies.

```powershell
npm install -g @aws-amplify/cli
npm install aws-amplify @aws-amplify/ui-react
```

In `index.js` file, configure Amplify.

```javascript
import { ThemeProvider } from "@aws-amplify/ui-react";
import { Amplify } from 'aws-amplify';

import awsconfig from './aws-exports';

import "@aws-amplify/ui-react/styles.css";
import { studioTheme } from "./ui-components";

Amplify.configure(awsconfig);
```

Using a theme from Amplify with `ThemeProvider`.

```xml
<ThemeProvider theme={studioTheme}>
  <App />
</ThemeProvider>
```

Import the UI component created and configured from Amplify Studio.

```javascript
import {
 FeatureCardCollection 
} from './ui-components';
```

### Modifying the components locally

From the above screenshots, you may have noticed the images being broken while previewing on Amplify Studio. Let's modify the `InfoCardDetails` component to retrieve data from Amplify Storage.

```javascript
import React, { useEffect, useState } from "react";
import {
  getOverrideProps,
  useNavigateAction,
} from "@aws-amplify/ui-react/internal";
import { Button, Divider, Flex, Image, Text } from "@aws-amplify/ui-react";
import { Storage } from "aws-amplify"
import { useHistory } from "react-router-dom";

export default function InfoCardDetails(props) {
  const { infoCard, overrides, ...rest } = props;
//Set the states
  const [imageThumbnail, setImageThumbnail] = useState('')
  const [mdDoc, setMDDoc] = useState('')
  useEffect(() => {
    fetchImage()
    fetchMD()
  }, [])

//Using Amplify Storage to download images 
  async function fetchImage() {
    try {
      const image = await Storage.get(infoCard.s3_thumbnail, {
        level: "public"
      })
      setImageThumbnail(image)
    } catch (err) { console.log('error fetching images') }
  }
....
//Setting image to Image component
            <Image
              width="unset"
              height="408px"
              display="block"
              gap="unset"
              alignItems="unset"
              justifyContent="unset"
              shrink="0"
              alignSelf="stretch"
              position="relative"
              padding="0px 0px 0px 0px"
              objectFit="cover"
              src={imageThumbnail}
              {...getOverrideProps(overrides, "image")}
            ></Image>
....
}
```

After modifying the same in `FeatureCard` and `ServiceCard`, locally run the app with `npm start`.

![FeatureCard collection rendering on the front-end](https://cdn.hashnode.com/res/hashnode/image/upload/v1681567088777/a23b3f22-5960-45b0-88de-fa797d15f6cd.png align="center")

![InfoCardDetails rendering with images and data from InfoCard model](https://cdn.hashnode.com/res/hashnode/image/upload/v1681567227116/0f01193b-1c17-4464-8ea2-460f94dade82.gif align="center")

The app renders all the images after they are downloaded from Amplify Storage.

## Wrap-up

Building applications with Amplify Studio has certainly amplified developer experience with defining, provisioning, and also managing different AWS services.

![Managing AWS resources with Amplify Studio](https://cdn.hashnode.com/res/hashnode/image/upload/v1681568244210/278e905b-24eb-4126-b97e-5b330a8b3c12.png align="center")

  
Having to do it manually for a full-stack developer during development would mean that they would need in-depth knowledge of all those underlying AWS services.