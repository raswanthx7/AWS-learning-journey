# S3 Access Controls 

## Topics Covered
- What is IAM Policy
- What is Bucket Policy
- What is ACL
- Presigned URLs
- Common S3 Actions
- Scenario Overview
- Why Multiple Access Controls Exist in S3
- IAM Policies (dev1, data1, intern1)
- Bucket Policy (Deny Delete)
- ACL (Object Ownership)
- ListAllMyBuckets vs ListBucket
- Final Permissions Table
- Important Policy Rules (Case Sensitivity, JSON Rules)

---

## 1. What is IAM Policy
IAM Policy defines what an AWS **user, group, or role** inside the account is allowed or not allowed to do.  
IAM is **identity-based permission**.

Examples:
- Can this user read this bucket?
- Can this user upload?
- Can this user delete?

IAM is always attached to the **identity**, not the bucket.

---

## 2. What is Bucket Policy
Bucket Policy is a **resource-based policy** attached directly to the bucket.  
It is used for:
- Cross-account access
- Public access (static website)
- Security enforcement (Deny rules)
- Overriding identity permissions

Bucket Policy can allow or deny **WHO** can access **THIS BUCKET**.

---

## 3. What is ACL (Access Control List)
ACL is legacy object-level access control.  
**Used when:**
- Cross-account objects are uploaded
- Object owner != bucket owner
- Public-read permissions (older systems)

### Why ACL Exists
ACL solves a very specific problem:
- When someone uploads an object, **they become the object owner**, not the bucket owner.
- The bucket owner might not have permission to read or delete that object unless ACL allows it.

**Example:**
A different AWS account uploads a file to your bucket →  
You **don’t** automatically own that file →  
ACL decides ownership and rights.

> IAM and Bucket Policy **cannot** override object ownership.

Modern AWS use:  
- ACL = only when necessary, bucket policy and IAM are preferred.
- **When ACLs are disabled (Bucket Owner Enforced), the bucket owner automatically becomes the owner of every object, even if another AWS account uploads it. This removes all cross-account ownership problems and is the modern default in AWS.**

---

## Presigned URLs

A presigned URL is a temporary, signed URL granting time-limited access to a private S3 object.

### Uses
- Allow a client to upload without granting IAM permissions  
- Provide secure download access to private files  
- Enable temporary access without modifying bucket policies  

Presigned URLs are generated via:
- AWS CLI  
- AWS SDKs (Python, JavaScript, etc.)

### Key Fact
Presigned URLs **do not require** bucket policy changes.  
They bypass normal access control because the signature itself authorizes the request.

---

## 4. Common S3 Actions 

- s3:ListAllMyBuckets → Shows all bucket names on the S3 home page.
- s3:ListBucket → Lists objects inside a specific bucket.
- s3:GetBucketLocation → Allows console/CLI to know the bucket's region.
- s3:GetObject → Downloads/reads an object.
- s3:PutObject → Uploads an object.
- s3:DeleteObject → Deletes an object from the bucket.

---

##  5. Scenario — Skynet Billing Solutions Pvt. Ltd

A company called Skynet Billing Solutions Pvt. Ltd has a bucket:

- skynet-billing-prod-1234

Three internal users:
- dev1 → needs read + write + list
- data1 → needs read-only + list
- intern1 → needs list-only (no read)

Business rule:
- Protect bucket from accidental deletion
- Allow only required operations
- Everything else must be denied

This scenario explains when to use IAM, Bucket Policy, and ACL.

---

## 6. Why S3 Has Three Access Systems

| Layer | Name          | Controls                        | Purpose                          |
|-------|----------------|----------------------------------|----------------------------------|
| 1     | IAM Policy     | User permissions                 | Controls what the identity can do |
| 2     | Bucket Policy  | Bucket-level rules               | Security enforcement, cross-account |
| 3     | ACL            | Object ownership                 | Legacy and special ownership cases |

---

## 7. IAM Policies (User-Level Permissions)

IAM controls what each user can do.

### dev1 (Read + Write)
```json
{
"Version": "2012-10-17",
"Statement": [
{
"Sid": "AllowSeeBucketList",
"Effect": "Allow",
"Action": "s3:ListAllMyBuckets",
"Resource": "*"
},
{
"Sid": "AllowListAndReadWriteBucket",
"Effect": "Allow",
"Action": [
"s3:ListBucket",
"s3:GetBucketLocation",
"s3:GetObject",
"s3:PutObject",
"s3:DeleteObject"
],
"Resource": [
"arn:aws:s3:::skynet-billing-prod-1234",
"arn:aws:s3:::skynet-billing-prod-1234/"
]
}
]
}
```

### data1 (Read Only)

```json
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Action": "s3:ListAllMyBuckets",
"Resource": "*"
},
{
"Effect": "Allow",
"Action": [
"s3:ListBucket",
"s3:GetBucketLocation",
"s3:GetObject"
],
"Resource": [
"arn:aws:s3:::skynet-billing-prod-1234",
"arn:aws:s3:::skynet-billing-prod-1234/"
]
}
]
}
```

### intern1 (List Only)

```json
{
"Version": "2012-10-17",
"Statement": [
{
"Effect": "Allow",
"Action": "s3:ListAllMyBuckets",
"Resource": "*"
},
{
"Effect": "Allow",
"Action": [
"s3:ListBucket",
"s3:GetBucketLocation"
],
"Resource": "arn:aws:s3:::skynet-billing-prod-1234"
}
]
}
```

---

## 8. Bucket Policy (Enforcement — Deny Delete)

```json
{
"Version": "2012-10-17",
"Statement": [
{
"Sid": "ProtectObjects",
"Effect": "Deny",
"Principal": "*",
"Action": "s3:DeleteObject",
"Resource": "arn:aws:s3:::skynet-billing-prod-1234/"
}
]
}
```

Reason:
Deny > Allow.  
Even if IAM allows delete, bucket policy denies delete for everyone.

---

# 9. ACL (Object-Level Ownership)
Used only when:
- Object owner ≠ bucket owner
- Public-read needed
- Cross-account upload situations

For this scenario, ACL is optional.

---

# 10. ListAllMyBuckets vs ListBucket

- **ListAllMyBuckets** → show all bucket names on S3 home  
- **ListBucket** → show objects inside one bucket  
- **GetBucketLocation** → console/CLI needs region info  
- Without these, user sees “Access Denied” or empty bucket

---

# 11. Final Permissions Table

| User    | ListAllMyBuckets | ListBucket | GetObject | PutObject | DeleteObject | Notes |
|---------|------------------|------------|-----------|-----------|--------------|--------|
| dev1    | ✔                | ✔          | ✔         | ✔         | ❌           | Read + Write, delete blocked |
| data1   | ✔                | ✔          | ✔         | ❌        | ❌           | Read only |
| intern1 | ✔                | ✔          | ❌        | ❌        | ❌           | List only |

---
# 12. Important Rules for Writing Policies (Summary)

1. Capitalization must be exact: Principal, Action, Resource, Statement, Version.  
2. Action names are case-sensitive (GetObject, ListBucket).  
3. Service prefix must be lowercase (s3, ec2).  
4. No trailing commas allowed in JSON.  
5. Arrays and brackets must be perfectly matched.  
6. Bucket-level actions use bucket ARN without /*.  
7. Object-level actions use bucket/* ARN.  
8. Deny always overrides Allow.  
9. IAM policies cannot use Principal, bucket policies must use Principal.


---
