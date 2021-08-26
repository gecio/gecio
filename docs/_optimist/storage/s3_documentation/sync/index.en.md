---
title: S3 Replication
lang: en
permalink: /optimist/storage/s3_documentation/replication/
nav_order: 3170
parent: S3 Compatible Object Storage
grand_parent: Storage
---

S3 Sync
============

# Introduction
The following tutorial outlines the process of synchronizing an S3 source container with a target container in a separate project, with the contents of the source container being replicated to the target container. To do this we first need to setup an Identity and access management (IAM) policy and then run the `s3cmd sync` command on the containers.

# Prerequisites
- Users should have access to both OpenStack projects
- Users should ensure that they have the s3cmd CLI installed
- Users should ensure that they have already generated ec2 credentials for use with the source bucket
- These ec2 credentials should be placed into the .s3cfg file in order to work with S3 on the CLI.

# IAM Policy
Create a file called "iampolicy" (the source bucket is called "firstbucket" and the destination bucket is called "secondbucket" in this example):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::firstbucket",
                "arn:aws:s3:::firstbucket/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": [
                "arn:aws:s3:::secondbucket",
                "arn:aws:s3:::secondbucket/*"
            ]
        }
    ]
}
```

Apply the IAM policy to the first bucket with the s3cmd CLI:
```bash
$ s3cmd setpolicy iampolicy s3://firstbucket
```

It can then be verified in the output of the below command that the policy has been applied:
```bash
$ s3cmd info s3://firstbucket
```

# Sync the source and destination buckets
The source bucket (firstbucket) can then be synched with the destination bucket (secondbucket) using the following command, specifying first the source and destination bucket:
```bash
$ s3cmd sync s3://firstbucket s3://secondbucket
```

The result should be similar to the below output:
```bash
$ s3cmd sync s3://firstbucket s3://secondbucket
Summary: 4 source files to copy, 0 files at destination to delete
remote copy: 's3://firstbucket/Screenshot 2021-08-18 at 21.59.10.png' -> 's3://secondbucket/Screenshot 2021-08-18 at 21.59.10.png'
remote copy: 's3://firstbucket/test-folder/' -> 's3://secondbucket/test-folder/'
remote copy: 's3://firstbucket/test-folder/test-repl/' -> 's3://secondbucket/test-folder/test-repl/'
remote copy: 's3://firstbucket/test123.txt' -> 's3://secondbucket/test123.txt'
Done. Copied 4 files in 1.5 seconds, 2.70 files/s.
```
