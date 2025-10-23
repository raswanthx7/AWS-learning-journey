#  AWS IAM (Identity & Access Management)

##  Topics Covered
1. IAM Users, Groups, Roles, Policies  
2. Console Access vs Programmatic Access  
3. Common, Job-Oriented, and Security Policies with Use Cases  
4. Hands-On: Create IAM User, Group, and Apply Policy
---

##  IAM Users, Groups, Roles, Policies

- **IAM Users:** Individual accounts for people or applications. Each user can have login credentials and permissions.  
- **IAM Groups:** Collection of users with common permissions. Assigning permissions to a group automatically applies to all users in it.  
- **IAM Roles:** Temporary permission sets that can be assumed by users, applications, or services. Useful for EC2 instances, Lambda functions, or cross-account access.  
- **IAM Policies:** JSON documents that define permissions. Policies are attached to users, groups, or roles to control what they can do in AWS.

---

##  Console Access vs Programmatic Access

### Console Access
- Access AWS via **web browser**.  
- User signs in to AWS Management Console to manage resources manually.  
- **Use case:** Admins logging into console to check resources, create users, monitor dashboards.

### Programmatic Access
- Access AWS via **CLI (Command Line Interface), SDK (Software Development Kit), or API (Application Programming Interface)**.  
- **CLI:** Run commands in terminal to manage AWS resources.  
  *Example use case:* Start/stop EC2 instances using commands.  
- **SDK:** Library in programming languages (Python, Java, Node.js, etc.) to simplify AWS API calls.  
  *Example use case:* Automate S3 bucket creation in a Python script.  
- **API:** Direct interface that allows applications to communicate with AWS services.  
  *Example use case:* A custom application fetching data from DynamoDB automatically.

> Programmatic Access is used for **automation**, scripts, or integrating AWS with applications.

---

##  Common IAM Policies with Use Cases

### 1. Basic Policies
- `AdministratorAccess` → Full access to all services.  
  *Use case:* New admin managing all AWS resources for testing or small project.  
- `PowerUserAccess` → Access to all services except IAM management.  
  *Use case:* Developer deploying apps without managing IAM users.  
- `ReadOnlyAccess` → View resources, no changes allowed.  
  *Use case:* Auditor reviewing AWS resources without making changes.  
- `Billing` → Access to billing and cost management.  
  *Use case:* Finance team checking monthly invoices and usage.  
- `IAMSelfManageServiceSpecificCredentials` → Manage own credentials for specific services.  
  *Use case:* Developer updating their own AWS keys for services like CodeCommit.

- `AmazonS3FullAccess` → Full control over S3 buckets.  
  *Use case:* Data engineer creating buckets, uploading, and managing files.  
- `AmazonEC2FullAccess` → Full control over EC2 instances.  
  *Use case:* DevOps managing server instances and scaling infrastructure.  
- `AmazonDynamoDBFullAccess` → Full control over DynamoDB.  
  *Use case:* Backend developer managing NoSQL tables for an application.  
- `AWSLambdaFullAccess` → Full control over Lambda functions.  
  *Use case:* Developer deploying and testing serverless functions.  
- `CloudWatchFullAccess` → Full monitoring and logging access.  
  *Use case:* DevOps monitoring application performance and setting alarms.

### 3. Security-Oriented Policies
- `IAMUserChangePassword` → Allows users to change their own password.  
  *Use case:* End user updating credentials for security.  
- `AWSKeyManagementServicePowerUser` → Manage KMS keys.  
  *Use case:* Security team managing encryption keys for sensitive data.  
- `AmazonVPCFullAccess` → Full VPC configuration access.  
  *Use case:* Network admin creating subnets, VPC peering, and routing rules.  
- `SecurityAudit` → Read-only access to security configurations for auditing.  
  *Use case:* Auditor checking compliance and security settings.  
- `AWSConfigUserAccess` → Access to AWS Config to view resource compliance.  
  *Use case:* Compliance team verifying resource configuration against best practices.

---

##  Hands-On: Create IAM User, Group, and Apply Policy

**Step 1 – Create a Group**  
1. Go to AWS Console → IAM → Groups → Create Group.  
2. Enter Group Name, e.g., `Developers`.  
3. Attach a policy (e.g., `PowerUserAccess`) → Create Group.  

**Step 2 – Create a User**  
1. Go to IAM → Users → Add Users.  
2. Enter User Name, e.g., `starkdev`.  
3. Select **Console Access** (set password) and/or **Programmatic Access** (for CLI/API).  
4. Add user to the group created earlier (`Developers`).  
5. Review and create user.  

**Step 3 – Apply Policy to User/Group**  
1. Select the User or Group → Permissions → Attach Policy.  
2. Choose the desired policy (e.g., `AmazonS3FullAccess`) → Attach Policy.  

**Step 4 – Test Access**  
1. Login as the IAM user.  
2. Check permissions: access allowed resources (S3 bucket, EC2 console) and restricted actions.  

---

###  Notes
- IAM itself is **free**, creating users, groups, roles, or policies does not cost anything.  
- Charges occur only if users or roles **launch paid AWS resources** (EC2, RDS, etc.).  
- Always follow **least privilege principle**: give only necessary permissions.  

---

