# IAM Policies — deepdive

## Topics Covered
- What is an IAM Policy
- Policy Language (JSON)
- Core elements — minimal template
- The 5 Core Elements of a Policy (Version, Statement,Sid, Effect, Action, Resource, Condition)
- Resource vs Non-Resource Services
- Why We Use ARN vs *
- Why Version Exists and Why It Should Not Be Changed
- Example IAM Policy with Real Scenario
- IMPORTANT — Policy Case Sensitivity 


---

## 1. What is an IAM Policy
An IAM Policy is a JSON document that defines what a user, role, or group can or cannot do in AWS.

In simple words:
A policy is a permission document written in JSON that controls access to AWS services.

AWS reads this JSON and decides whether a request should be allowed or denied.

---

## 2. IAM Policy Language (JSON)
This is not a human language. Policy language refers to the structure AWS accepts to understand permissions.

It uses:
- Keys (Version, Action, Resource)
- Values ("Allow", "s3:GetObject", etc.)
- Arrays ([ ])
- Strings ("")

AWS requires this specific JSON grammar for policy evaluation.

---

## 3. Core elements — minimal template

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "OptionalId",
      "Effect": "Allow" | "Deny",
      "Principal": { ... },        // only in resource-based policies
      "Action": "service:Operation" | [ ... ],
      "Resource": "arn:partition:service:region:account-id:resource" | "*",
      "Condition": { ... }         // optional
    }
  ]
}
```

## 4. Core Elements of an IAM Policy

## 4.1 Version
Example:
```json
"Version": "2012-10-17"
```

This is the policy language version, not your AWS version.

All modern policies use "2012-10-17".

It should not be changed because AWS expects this structure.

Older versions existed before 2012 but are deprecated.

The current version supports:
- Conditions
- Multiple statements
- Better policy evaluation

Always use "2012-10-17".

---

## 4.2 Statement
A policy can contain one or more statements.

```json
"Statement": [ ... ]
```

Each statement describes a single permission rule.

---

## 4.3 sid

`Sid` is just a label/name for a statement, used to identify it — AWS does not evaluate it for permissions.

Why we use it (real reason)

- Helps you recognise each permission block
- Makes debugging easier
- Used for auditing and tracking
- Helps in CloudTrail logs to identify which statement allowed/denied an action
- Useful when you have many statements in one policy

Example
```json
"Sid": "AllowListAndReadThisBucket"
```

---

## 4.3 Effect
Possible values:
- "Allow"
- "Deny"

Explicit "Deny" always overrides "Allow".

---

## 4.4 Principal

Principal defines **who** the policy applies to.

IAM policies (user-level policies) DO NOT use Principal.  
Principal is only used in **resource-based policies**, such as:

- Bucket Policies
- IAM Role Trust Policies
- KMS Key Policies
- SQS/SNS Access Policies

Example:
```json
"Principal": {
"AWS": "arn:aws:iam::111122223333:user/dev1"
}
```
Or allow everyone:
```json
"Principal": "*"
```
---

## 4.5 Action
Defines what operation is allowed or denied.

Examples:
- s3:GetObject
- ec2:StartInstances
- iam:CreateUser

---

## 4.6 Resource
Defines where the action applies.

There are two service types:

### A. Resource-Level Services (Use ARN)
These allow targeting specific resources.

Examples:
- S3 bucket
- EC2 instance
- DynamoDB table
- SQS queue

Example ARNs:
```json
arn:aws:s3:::mybucket/*
arn:aws:ec2:ap-south-1:111122223333:instance/i-02ab3cd4ef5678901
```

### ARN Format:

arn:partition:service:region:account-id:resource

#### Explanation of Each Part

| Part        | Meaning |
|-------------|---------|
| arn         | Literal prefix meaning "Amazon Resource Name" |
| partition   | AWS environment: `aws` (commercial), `aws-us-gov`, `aws-cn` |
| service     | AWS service namespace (s3, ec2, dynamodb, iam, kms) |
| region      | Region of the resource (ap-south-1, us-east-1). For S3 bucket names it is empty |
| account-id  | AWS Account ID owning the resource. For some global resources, this is empty |
| resource    | Actual resource reference: bucket name, instance ID, table name, role name, etc. |

**Example ARN Breakdown (S3 Object)**
```json
arn:aws:s3:::skynet-billing-prod-1234/logs/data.csv
```
Breakdown:
- arn → Amazon Resource Name
- aws → Partition
- s3 → Service
- region → Empty because S3 bucket namespace is global
- account-id → Empty for global namespace services
- resource → skynet-billing-prod-1234/logs/data.csv (bucket + object key)

**Example ARN Breakdown (EC2 Instance)**
```json
arn:aws:ec2:ap-south-1:111122223333:instance/i-02ab3cd4ef5678901
```

Breakdown:
- arn → Amazon Resource Name
- aws → Partition
- ec2 → Service
- ap-south-1 → Region
- 111122223333 → Account ID
- instance/i-02ab3cd4ef5678901 → Resource path (instance type + ID)

---

### B. Non-Resource Services (Use "*")
Some services do not support ARNs for individual resources.

Examples:
- IAM
- STS
- Organizations

For these actions, AWS requires:
```json
"Resource": "*"
```

### Why "*" is used
Some actions do not target a specific resource.

Example:
- iam:CreateUser cannot point to a user that does not exist yet.

---

## 4.7 Condition

The Condition block adds **extra filters** to permission decisions.

It answers:  
“Allow/Deny this action only if this condition is true.”

### Why Condition is used
To restrict access based on:
- IP address
- MFA
- Encryption
- Time of day
- Object prefixes
- Tags
- Access via VPC endpoint
- Secure transport (HTTPS)

### Condition Example — Allow access only from specific IP
```json
"Condition": {
"IpAddress": {
"aws:SourceIp": "103.45.67.89/32"
}
}
```
### Condition Example — Allow access only if request is encrypted with HTTPS
```json
"Condition": {
"Bool": {
"aws:SecureTransport": "true"
}
}
```

---

# 5. Why Version Should Not Be Changed
Older versions (before 2012) lacked:
- Multiple statements
- Advanced conditions
- Service-specific permissions
- Modern AWS action formats

"2012-10-17" is the standard, so always use it.

---

# 6. Example: S3 Read-Only Access to a Specific Bucket

## Scenario
A company wants:
- A user to read objects from a single bucket
- No listing, deleting, or modifying
- Only GetObject allowed

## IAM Policy

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
      "Sid": "AllowListAndReadThisBucket",
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation",
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::my-company-logs",
        "arn:aws:s3:::my-company-logs/*"
      ]
    }
  ]
}
```

- s3:ListAllMyBuckets → Allows the user to see all bucket names on the S3 console home page.
- s3:ListBucket → Allows the user to list objects inside the specific bucket (see files/folders).
- s3:GetBucketLocation → Allows the user to view the bucket’s region so the console/CLI can load bucket details.
- s3:GetObject → Allows the user to read/download objects from inside the bucket.

---

## 7. IMPORTANT — Policy Case Sensitivity 

1. IAM policy keys must use exact capitalization: "Version", "Statement", "Effect", "Action", "Resource", "Principal", "Condition".
2. AWS action names are case-sensitive — you must write them exactly (Example: s3:GetObject, not s3:getobject).
3. Service prefix must always be lowercase (s3, ec2, iam).
4. Every JSON string must be inside double quotes — missing quotes breaks the policy.
5. No trailing commas allowed — even one extra comma causes parsing failure.
6. Brackets must be perfectly matched ( { }, [ ] ) or AWS rejects the policy.
7. Wrong spelling or wrong capital letters in any action (like Putobject instead of PutObject) results in AccessDenied.
8. Resource ARNs must match the correct pattern — bucket-level actions use bucket ARN (no /*), object-level actions use bucket/*.
9. Deny always overrides Allow — one deny anywhere in IAM, bucket policy, or ACL will block the request.
10. IAM user policies cannot use “Principal” — only resource-based policies (like bucket policy) can.
11. Conditions must match the correct data type — StringEquals vs Bool vs IpAddress must be used correctly.

---

# Summary Table

| IAM Concept | Purpose |
|-------------|---------|
| Version | Defines policy language (always 2012-10-17) |
| Statement | Container for permission rules |
| Effect | Allows or denies the action |
| Action | AWS operation to allow/deny |
| Resource | ARN or * depending on service |
| Condition | Extra filters (IP, MFA, encryption, etc.) |
| ARN | Unique identifier for AWS resources |
| Wildcard * | Used when AWS does not support resource-level permissions |

---
