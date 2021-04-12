---
title: Create and Delete a Bucket
lang: en
permalink: /optimist/storage/s3_documentation/createanddeletebucket
nav_order: 3110
parent: S3 Compatible Object Storage Introduction
grand_parent: Storage
---

Create and Delete a Bucket
=================================================

Contents:
-----------
- [S3cmd](#s3cmd)
	- [Create a bucket](#createbuckets3cmd)
	- [Delete a bucket](#deletebuckets3cmd)
- [S3Browser](#s3browser)
	- [Create a bucket](#createbuckets3browser)
	- [Delete a bucket](#deletebuckets3browser)
- [Cyberduck](#cyberduck)
	- [Create a bucket](#createbucketcyberduck)
	- [Delete a bucket](#deletebucketcyberduck)
- [Boto3](#boto3)
	- [Create a bucket](#createbucketboto3)
	- [Delete a bucket](#deletebucketboto3)

To upload your data (documents, photos, videos, etc.) it is necessary to create a bucket, which is similar to a folder. First create an S3 bucket and then you can upload as many objects as required into the bucket.

[//]: # "Since the namespace for the iNNOVO S3 is used by all customers and the name of a bucket is unique. The name of a bucket after its creation cannot be used by another iNNOVO account until the bucket is deleted. Therefore, please do not rely on a specific name for buckets."

[S3cmd](#s3cmd)
=============

[Create a bucket](#createbuckets3cmd)
-----------------------

To create a bucket, use the following command:

```bash
$ s3cmd mb s3://NameOfTheBucket
```

The output in the command line will look similar to this:

```bash
$ s3cmd mb s3://iNNOVO-Test
Bucket 's3://iNNOVO-Test/' created
```

[Delete a bucket](#DeleteBucketS3cmd)
-----------------------

To delete a bucket, use the following command:

```bash
$ s3cmd rb s3://NameOfTheBucket
```
The output in the command line will look similar to this:

```bash
$ s3cmd rb s3://iNNOVO-Test
Bucket 's3://iNNOVO-Test/' removed
```

[S3Browser](#s3browser)
=============

[Create a bucket](#createbucketS3Browser)
-----------------------

After opening S3Browser, we click on "New bucket"(1) in the upper left corner, in the newly opened window, we assign the name of the bucket via "Bucket name"(2) and then click on "Create new bucket"(3).

![](attachments/CreateAndDeleteBucket1.png)

[Delete a bucket](#deletebuckets3browser)
-----------------------

First select the bucket you want to delete(1) and then click on "Delete bucket"(2) in the upper left corner.

![](attachments/CreateAndDeleteBucket2.png)

In the window that opens, confirm that you want to delete the file by checking the checkbox(1) and then click on "Delete Bucket"(2).

![](attachments/CreateAndDeleteBucket3.png)

[Cyberduck](#cyberduck)
=============

[Create a bucket](#createbucketcyberduck)
-----------------------
After opening Cyberduck, we click on "Action"(1) and on "New folder"(2) in the middle of the top.

![](attachments/CreateAndDeleteBucket4.png)

A new window opens, here we can define the name(1) and confirm this with "Create"(2):

![](attachments/CreateAndDeleteBucket5.png)

[Delete a bucket](#deletebucketcyberduck)
-----------------------

To delete a bucket, select it with a left mouse click. The bucket is then deleted via "Action"(1) and "Delete"(2).

![](attachments/CreateAndDeleteBucket6.png)

Confirm the action by clicking on "Delete"(1) once again.

![](attachments/CreateAndDeleteBucket7.png)

[Boto3](#boto3)
=============

In Boto3 we first need the S3 identifier so that a script can be used. For details see: [Create and use S3 credentials#Boto3](./CreateAndUseS3CredentialsEN/#boto3)

[Create a bucket](#createbucketboto3)
-----------------------
To create a bucket, we first need a client for it and we will then create the bucket afterwards.
One option looks like this:
```bash
## Create the S3 client
s3 = boto3.client('s3')

## Create a bucket
s3.create_bucket(Bucket='iNNOVO-Test')
```

A complete script for boto 3 including authentication may look like this:

```python
#!/usr/bin/env/python

## Define that boto3 should be used
import boto3
from botocore.client import Config

## Authentication
s3 = boto3.resource('s3',
                        endpoint_url='https://s3.es1.fra.optimist.innovo.cloud',
                        aws_access_key_id='aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa',
                        aws_secret_access_key='bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb',
                    )

## Create the S3 client
s3 = boto3.client('s3')

## Create a bucket
s3.create_bucket(Bucket='iNNOVO-Test')
```

[Delete a bucket](#deletebucketboto3)
-----------------------

As before, we first need a client to delete the bucket.
One option looks like this:

```bash
## Create the S3 client
s3 = boto3.client('s3')

## Delete a bucket
s3.delete_bucket(Bucket='iNNOVO-Test')
```

A complete script for boto 3 including authentication may look like this:

```python
#!/usr/bin/env/python

## Define that boto3 should be used
import boto3
from botocore.client import Config

## Authentication
s3 = boto3.resource('s3',
                        endpoint_url='https://s3.es1.fra.optimist.innovo.cloud',
                        aws_access_key_id='aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa',
                        aws_secret_access_key='bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb',
                    )

## Create the S3 client
s3 = boto3.client('s3')

## Delete a bucket
s3.delete_bucket(Bucket='iNNOVO-Test')
```