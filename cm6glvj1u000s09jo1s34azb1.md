---
title: "Amazon S3 is more than storage and brings in a lot for the analytics ecosystem"
seoTitle: "Amazon S3 is more than storage and brings in a lot for analytics"
seoDescription: "Amazon S3 introduces new capabilities for analytics workloads, enhancing data query performance and management with S3 Tables and Metadata"
datePublished: Tue Jan 28 2025 15:01:45 GMT+0000 (Coordinated Universal Time)
cuid: cm6glvj1u000s09jo1s34azb1
slug: amazon-s3-is-more-than-storage-and-brings-in-a-lot-for-the-analytics-ecosystem
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1737998472957/9eba840a-fed3-4e00-bd38-40ef4dc68315.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1737999723934/61b998c5-7118-4b0b-8db0-13584687447d.png
tags: analytics, aws, storage, table, s3

---

At AWS re:Invent 2024, Amazon S3 announced [S3 Tables](https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-s3-tables-apache-iceberg-tables-analytics-workloads/) and [S3 Metadata (preview)](https://aws.amazon.com/about-aws/whats-new/2024/12/amazon-s3-metadata-preview/) specifically for analytics workloads. Although not extensively into building Analytics workloads, coming from the background of building ETL pipelines that use S3 and parquet data and I wanted to explore this new capability!

# Parquet data in S3

Parquet is the columnar storage format that is efficient for data storage and retrieval and is widely used by different ETL and big data processing frameworks such as Apache Spark, Hive, and Amazon Athena. This makes the parquet data more accessible with the query approach by services like Athena that use SQL queries on the parquet data. Parquet-formatted data is stored as any other object in an S3 Bucket, with an additional tag specifically mentioning that it is in `parquet` format.

![How Parquet data is stored and queried from S3 Buckets](https://cdn.hashnode.com/res/hashnode/image/upload/v1737213615751/10a7fa74-b05e-40d1-969e-cdeec752bb34.png align="center")

Storing this parquet data in Amazon S3 opens up the opportunity to leverage cloud storage by integrating AWS Services such as AWS Glue for ETL, Amazon Redshift for data warehouse, and Amazon SageMaker for ML workloads. Parquet stored on S3 is not only performant but also cost-efficient as the files are smaller than CSV and can be much cheaper to store on S3 with different S3 storage classes based on how frequently they are queried.

# S3 Tables

Amazon S3 launched a new bucket type - `tables`, which is designed for structured data formats using the Apache Iceberg table storing Apache parquet format data. This offers up to **3x faster query performance** with parquet and a **10x higher transaction per second** when compared with parquet data stored as an object in an S3 bucket, making it ideal for data analytics workloads.

![Architecture of S3 tables](https://cdn.hashnode.com/res/hashnode/image/upload/v1737991474611/6f3e9916-6275-4f5b-aee7-87f40d3fce68.png align="center")

S3 tables enable structure data formatting with the structure of Table Buckets with `namespace`, which contains the `tables` that could be queried from services like Amazon Athena the catch is to set the tables in a namespace, and also loading the tables is supported with only Amazon EMR and open source Apache Spark which makes the developers go through the environment setup of EMR clusters or Spark; making the getting started experience enforced to go to EMR or self-hosted Spark.

![S3 table setup in Table Buckets](https://cdn.hashnode.com/res/hashnode/image/upload/v1737991850576/cf9a29c9-cb85-494f-9041-5e257138a5de.png align="center")

## Setting up one-time integration with AWS Analytics Services

![S3 tables from the AWS Console.](https://cdn.hashnode.com/res/hashnode/image/upload/v1737989203601/cddd5e0b-7a5a-45a2-870a-72ae379e1b53.png align="center")

Another region-wide, one-time setup of AWS Analytics services is an additional environment setup process one should follow.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1737991917925/c78c7970-9db8-4688-a164-85f88b062600.png align="center")

Honestly, this could have been done natively by AWS, and given the developers to manage permissions to different AWS Analytics services.

## S3 Tables with built-in management

What makes S3 Tables performance is that way S3 Tables handles the typical table management with -

* **Data compaction** - which combines the small table objects into larger objects which is configurable between 64MB and 512MB as a snapshot.
    
* **Snapshot management** - ensures the snapshot lifecycle has a minimum number of snapshots to retain the maximum age of the snapshot to retain, and eventually deleting the expired snapshots.
    

These factors weigh in to make S3 Tables performant, you can read about [how S3 table uses compaction to improve query performance by up to 3 times](https://aws.amazon.com/blogs/storage/how-amazon-s3-tables-use-compaction-to-improve-query-performance-by-up-to-3-times/), along with some benchmarks with uncompacted tables in general-purpose buckets v/s compacted tables in table buckets.

# S3 Metadata

Along with S3 Tables, Amazon S3 also announced S3 Metadata, which is a bucket property that can be enabled to capture the metadata about each S3 object in a general-purpose S3 bucket. S3 Metadata uses S3 Tables with the power of parquet data, the metadata of the objects are now queriable making searching for S3 objects with metadata much more efficient.

![S3 Metadata using S3 Tables to store object metadata](https://cdn.hashnode.com/res/hashnode/image/upload/v1737993643054/e0216843-b745-4e12-b6f7-202b9612a20b.png align="center")

## Types of S3 Metadata

S3 Metadata supports each object in a bucket with metadata that are of two categories -

* **System defined** - the metadata that is controlled by Amazon S3 natively, such as - `Date`, `Content-Length`, `Last-Modified`, and `ETag` that are immutable values by the user along with the metadata such as - `Cache-Control`, `Content-Disposition`, and `Content-Type`, which allow change in value by the user.
    
* **User defined -** the metadata that could be assigned by the user when the object is being uploaded, these keys are prefixed with `x-amz-meta-`.
    

## S3 Metadata in action

* Enable the S3 Metadata for the S3 Bucket from the console which requires you to set the name of the S3 Table and following the creation, it also creates the namespace `aws_s3_metadata`.
    
    ![S3 Console with S3 Metadata enabled](https://cdn.hashnode.com/res/hashnode/image/upload/v1737997436299/703394f3-9224-4e30-8a1e-2b0dfed6e730.png align="center")
    
* And when you navigate to `Table Buckets`, you can see the S3 Table bucket with the tables created and listed.
    
    ![S3 Tables listing the table for S3 Metadata](https://cdn.hashnode.com/res/hashnode/image/upload/v1737998132449/fb360e18-47fe-40f0-8e21-62e770599f8e.png align="center")
    
* When you try to upload a new object to the S3 bucket, you can optionally define the Metadata - `system-defined` or `user-defined`.
    
    ![Uploading S3 object with S3 Metadata](https://cdn.hashnode.com/res/hashnode/image/upload/v1737998273683/5dcddeae-598b-479f-8197-c69bf91dfd73.png align="center")
    
* Once uploaded, you can view the metadata from the S3 Console and also query the same with Athena.
    
    ![Viewing the defined S3 Metadata](https://cdn.hashnode.com/res/hashnode/image/upload/v1737998353012/32e1e41a-1589-41b3-86fa-db6a7dd3e80a.png align="center")
    

# Why would I choose S3 Metadata?

Since the data is stored as the Apache Iceberg with S3 Tables, one question that hit me was - *why would I enable S3 Metadata instead of directly implementing it with S3 Tables*?

S3 Metadata is crucial for organizing and managing the data of S3 Buckets, imagine having a system that extensively uses S3 Buckets to store data (of any format), leveraging the S3 Metadata for that would mean you can have the metadata of the object (user-defined metadata) that can help with data categorization and management. Additionally, with the metadata of the objects, data discovery with additional attributes would play a pivotal role for applications.