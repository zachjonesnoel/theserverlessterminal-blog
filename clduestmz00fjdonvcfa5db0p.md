---
title: "DynamoDB and TTL"
datePublished: Sun May 30 2021 15:14:42 GMT+0000 (Coordinated Universal Time)
cuid: clduestmz00fjdonvcfa5db0p
slug: dynamodb-and-ttl
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1675784458137/b4f775bf-8c0c-4afc-aefd-174438e5b956.jpeg

---

In the modern databases, there is a TTL (Time to Live) option available where the item/record is automatically deleted when the TTL attribute is exceeding the current time. Like any other modern database, Amazon DynamoDB also provides this really impressive feature. 

#### What is TTL?
TTL (Time to Live) is the technique of defining the life-span of a data, more as an expiry date for a data which is residing in the database. This is usually used to clean up old records which wouldn't be referred to in the future. 
Eg. Session credentials.

[Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/TTL.html) defines TTL as 
> Amazon DynamoDB Time to Live (TTL) allows you to define a per-item timestamp to determine when an item is no longer needed. Shortly after the date and time of the specified timestamp, DynamoDB deletes the item from your table without consuming any write throughput. TTL is provided at no extra cost as a means to reduce stored data volumes by retaining only the items that remain current for your workload’s needs. 

#### Configuring TTL in DynamoDB
In Amazon DynamoDB, TTL can be configured from AWS Console or CLI or even CloudFormation. An attribute in DynamoDB item has to be set as a TTL attribute which is of type *Number* and the value stored in that attribute has to be an *epoch timestamp*, any other number value stored in DynamoDB would be excluded.

On your AWS Console, navigate to your DynamoDB table and in the overview tab you will find the details about your DynamoDB table, one of the options which is *Time to live attribute* by default it is disabled so you would have to click on *Manage TTL* hyperlinked text.
![Configuring TTL](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784448533/c9bb599f-b764-4669-abb5-807e1b1d440c.png)
A pop-up appears which has the TTL configuration for the DynamoDB table where you would have to enter in the TTL attribute name. DynamoDB provides another sub-feature with DynamoDB streams for a 24 hour window for backing up the deleted items. Once you set the TTL attribute, you can preview the expiring items by manually selecting a date and time. 
![TTL settings](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784450095/ea0e5251-b862-4df4-9694-49bf540cc8a8.png)
Once this is configured, it's about how the items are put into DynamoDB. 
For demo, I've used a *pk* as the partition key and *session_expires_on* as the TTL attribute. I've also added 4 records with different expiry time, so that DynamoDB would delete the items at different time periods. 
![Records before TTL](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784451650/724452b0-41ef-491f-a192-d3ec1fc43388.png)
Once the TTL attribute's value is less than current epoch timestamp, DynamoDB handles the deletion of the items.
![Records afterTTL](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784452913/9de50363-451b-4618-be5c-2543cc3258da.png)
The metrics for deleted items is also available. Cloudwatch metrics is updated whenever an items is deleted. You can find it under the *Metrics* tab from DynamoDB console.
![TTL Cloudwatch metrics](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784454468/6055156a-1390-4eb0-95de-79795156b8fe.png)
And after a couple of hours, the other records were also deleted.
![TTL Cloudwatch metrics](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784455757/de476804-70c7-4d8c-b6df-f7a6003cd4e3.png)


#### Things to watch out for in DynamoDB TTL
+ TTL attribute should be of type Number.
+ TTL attribute value should be in epoch timestamp and the timestamp should be a future date.
+ DynamoDB doesn't guarantee instantaneous deletion of items when the time is exceeded but guarantees deletion within 48 hour time period.  
+ Since instantaneous delete is not guaranteed, developers should take measures to filter out expired items.
+ Any change in TTL settings will take 1 hour to reflect the changes.
![TTL setting change](https://cdn.hashnode.com/res/hashnode/image/upload/v1675784457008/68d6dce6-65d8-4837-a69e-f8b163e4bb18.png)
+ DynamoDB streams could be used for data recovery but the data would be available for the 24 hour window only.


#### Applications of DynamoDB TTL
+ User session management where you can set a session-timeout for the records to expire and delete.
+ OTP or temporary credential management.
+ Deletion historical records. Eg. if your application is bound to hold data from latest 7 days, anything older than 7 days have to be deleted.
+ Managing API Gateway web-socket connections which are orphaned or not active for example, in the last 10 minutes.


#### Conclusion
Amazon DynamoDB provides TTL feature where the item should contain an attribute with futuristic epoch timestamp. Once the attribute value exceeds current epoch timestamp, it would be deleted which is not instantaneous but can be deleted anytime in a 48 hour window. DynamoDB native TTL facilitates developers to avoid EventBridge cron jobs to manually delete the records from a Lambda function.

