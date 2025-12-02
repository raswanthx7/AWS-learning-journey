# S3 Security — Hands-On Lab Guide

## Topics Covered
- Lab Overview
- Prerequisites
- Bucket Creation
- IAM User Creation
- IAM Policy Creation (dev1, data1, intern1)
- Policy Attachment
- Verification and Testing
- Bucket Policy Enforcement
- ACL and Object Ownership Concepts
- Final Understanding

---

## 1. Lab Overview

This lab provides a structured, end-to-end walkthrough of internal S3 security design.  
We will create a secure S3 bucket, configure multiple IAM users with different permission levels, enforce bucket-level security using a bucket policy, and understand the practical behavior of ACL-based object ownership.

The lab demonstrates:
- Identity-based access control (IAM)
- Resource-based access control (Bucket Policy)
- Object ownership considerations (ACL)
- Deny precedence and policy evaluation flow

---

# 2. Prerequisites

- Use an IAM user or role with **AdministratorAccess**.  
- Do not use the root account for normal operations.  
- Select a single region (e.g., **ap-south-1**) and remain consistent.  
- Ensure permissions exist to create:
  - S3 buckets  
  - IAM users  
  - IAM policies  
  - Bucket policies  

If the user can create S3 buckets and IAM users, the prerequisites are satisfied.

---

# 3. Create the S3 Bucket

Create a bucket that represents internal company storage.

### Bucket Configuration
- **Name:** `skynet-billing-prod-<yourname>-1234`
- **Region:** ap-south-1
- **Block Public Access:** All options **enabled**
- **Versioning:** Disabled
- **Default Encryption:** Optional (SSE-S3 recommended)

Create the bucket.

### Folder Structure
Inside the bucket, create the following prefixes:

- `invoices/`
- `reports/`
- `logs/`

Upload one small file into each folder to simulate application data.

---

# 4. Create IAM Users

Create three IAM users representing internal teams:

- **dev1**
- **data1**
- **intern1**

For each user:
- Enable **AWS Management Console Access**
- Assign a login password
- Do not attach permissions at this stage

---

# 5. Create IAM Policies

Below are the fully correct and complete policies for each role.  
Replace the bucket name with your own bucket ARN.

---

## 5.1 dev1 — Read and Write Access

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListAllBuckets",
      "Effect": "Allow",
      "Action": "s3:ListAllMyBuckets",
      "Resource": "*"
    },
    {
      "Sid": "BucketListing",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Resource": "arn:aws:s3:::skynet-billing-prod-raswanth-1234"
    },
    {
      "Sid": "ObjectReadWrite",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::skynet-billing-prod-raswanth-1234/*"
    }
  ]
}
```

### 5.2 data1 — Read-Only Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListAllBuckets",
      "Effect": "Allow",
      "Action": "s3:ListAllMyBuckets",
      "Resource": "*"
    },
    {
      "Sid": "BucketListing",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Resource": "arn:aws:s3:::skynet-billing-prod-raswanth-1234"
    },
    {
      "Sid": "ObjectReadOnly",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::skynet-billing-prod-raswanth-1234/*"
    }
  ]
}
```

### 5.3 intern1 — List-Only Access
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListAllBuckets",
      "Effect": "Allow",
      "Action": "s3:ListAllMyBuckets",
      "Resource": "*"
    },
    {
      "Sid": "BucketListingOnly",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Resource": "arn:aws:s3:::skynet-billing-prod-raswanth-1234"
    }
  ]
}
```

---

## 6. Attach Policies to Users

Assign the policies as follows:

- dev1 → Skynet-DevTeam-S3Access
- data1 → Skynet-DataTeam-ReadOnly
- intern1 → Skynet-Intern-ListOnly

---

## 7. Verification and Testing

### dev1 (Expected Results)

- List bucket: ✔
- Upload: ✔
- Download: ✔
- Delete: ✔

### data1 (Expected Results)

- List bucket: ✔
- Download: ✔
- Upload: ✘
- Delete: ✘

### intern1 (Expected Results)

- List bucket: ✔
- Download: ✘
- Upload: ✘
- Delete: ✘

IAM-level behavior is now confirmed.

---

## 8. Bucket Policy Enforcement

Add a bucket policy to enforce a global DeleteObject restriction.
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyDeleteForEveryone",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:DeleteObject",
      "Resource": "arn:aws:s3:::skynet-billing-prod-raswanth-1234/*"
    }
  ]
}
```
Result

- dev1 can no longer delete objects
- Bucket-level Deny overrides IAM Allow
- This confirms Deny precedence in AWS access evaluation

---

## 9. ACL and Object Ownership

Navigate to:
S3 → Bucket → Permissions → Object Ownership

**Bucket Owner Enforced (Recommended)**

- ACL disabled
- Bucket owner becomes the owner of all uploaded objects
- Eliminates cross-account ownership issues
- This is the default mode

**ACL Enabled**

View object-level permissions such as:

- READ
- WRITE
- FULL_CONTROL

Important concept:
If an external AWS account uploads an object, they are the owner unless the upload uses:
```bash
x-amz-acl: bucket-owner-full-control
```

ACLs primarily matter in cross-account object ownership scenarios.

---

## 10.  Final Understanding

This lab demonstrated:

- IAM → identity-level permissions
- Bucket Policy → resource-level enforcement
- ACL → object-level ownership behavior
- Deny → highest priority in AWS evaluation
- GetBucketLocation + ListBucket + ListAllMyBuckets → mandatory for real console use

---
