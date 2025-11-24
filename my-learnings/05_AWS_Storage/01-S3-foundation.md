# S3 Foundations

## Topics Covered
- What is S3
- Why It's Called Object Storage
- What Makes S3 Unique
- Durability — 11 Nines (99.999999999%)
- Availability (99.99%)
- Buckets, Objects, Prefixes, and Keys
- Why S3 Has No Folders or File System
- Why object keys are strings
- Regions + Global Namespace
- S3 vs EBS vs EFS
- Hands-on (Essential Labs)
- Summary

---


## 1. What is S3

Amazon S3 (Simple Storage Service) is AWS’s globally available, highly durable, infinitely scalable object storage platform.

S3 is not a disk.

S3 does not behave like a file system, does not mount to an OS, and does not provide block-level access. Every interaction with S3 happens through HTTPS APIs, the AWS Console, CLI, or SDKs.  

S3 stores data as objects inside buckets and distributes them automatically across multiple Availability Zones for reliability and durability.

---

## 2. Why It's Called Object Storage

Object storage means S3 does not store data as files or blocks.  
Every stored item is an object composed of:

- Data: the actual file content (image, text, binary, video)
- Metadata: information describing the object (size, content type, last modified, storage class, custom metadata)
- Object Key: a full string that uniquely identifies the object inside the bucket

This model allows S3 to scale without limitation and provide massive parallel read/write capability.

Use cases include:
- Backups and archives
- Static websites
- Media storage
- Data lakes
- Logs
- ML training datasets
- Big data workloads

---

## 3. What Makes S3 Unique

- 11 nines durability  
- Infinite scalability  
- No filesystem limitations  
- Distributed object storage design  
- API-driven access  
- Automated replication across multiple AZs  
- Multiple storage classes for cost optimization  
- Integrates with nearly every AWS service  
- Suitable for any workload where files are stored as complete objects  

---

## 4. Durability — 11 Nines (99.999999999%)

S3 achieves extremely high durability by storing multiple redundant copies of each object across multiple Availability Zones.

Even if hardware fails, racks fail, or one entire AZ becomes unavailable, the data remains intact.

Durability represents the probability of data loss.  
It is not the same as service uptime.

---

## 5. Availability (99.99%)

Availability refers to how often S3 can actually respond to requests.

S3 may experience maintenance windows or small outages, but the stored data is still safe.

Durability protects your data.  
Availability determines if the service can currently serve you.

These concepts are not equal and must be understood separately.

---

## 6. Buckets, Objects, Prefixes, and Keys 

### Bucket
The top-level container where objects are stored.  
Buckets exist in a specific AWS region and bucket names must be globally unique across all AWS accounts worldwide.

### Object
The actual file you upload.  
Every object consists of:
- Data: file contents  
- Metadata: system metadata (Content-Type, ETag, Last-Modified) and optional user-defined metadata  
- Object Key: the unique identifier of the object inside the bucket

### Object Key (full path string)
This is not a file system path.  
It is simply a string that identifies the object.

Examples:
- photos/2025/april/birthday.jpg  
- logs/app/2025/11/log1.txt  

S3 uses the entire string as the key.  
The slashes (`/`) have no technical meaning; they exist only for visual grouping.

### Prefix
Everything before the final segment of the key.

Example:  
Object key: photos/2025/april/birthday.jpg  
Prefix: photos/2025/april/

Prefixes simulate folders but are not real directories.

---

## 7. Why S3 Has No Folders or File System

S3 is not a hierarchical storage system.  
It does not contain:
- Directories
- Inodes
- File system structures
- Mount points
**NOTHING LIKE NORMAL OS FILE SYSTEM**

The S3 console shows a folder-like view only for user convenience.  
Internally, there is just a flat namespace where every object is identified by its key string.

### Why object keys are strings
A flat namespace scales far beyond traditional filesystems.  
Since no OS-level folder structure exists, the only logical way to locate objects is through key strings.

This simplicity gives S3:
- Infinite scaling
- High performance
- Simple lookups
- Massive parallelism

---

## 8. Regions + Global Namespace

Buckets are regional resources.  
Your bucket and its objects live inside the chosen region (for example: ap-south-1).

Bucket names are globally unique.  
This is why you cannot create a bucket with a name already taken by someone else in a different region or account.

Buckets are not global, but bucket names are.

---

## 9. S3 vs EBS vs EFS

### S3 (Object Storage)
- Not mountable  
- No OS-level filesystem  
- Unlimited capacity  
- Cheapest storage  
- Designed for static content, backups, logs, large datasets  

### EBS (Block Storage)
- Acts like a virtual hard disk  
- Mountable to one EC2 at a time  
- Low latency, random read/write  
- Used for OS disks and databases  

### EFS (File Storage)
- Fully managed NFS filesystem  
- POSIX compliant  
- Mountable by multiple EC2 instances at once  
- Used for shared apps, microservices, distributed workloads  

---



## 10. Hands-on (Essential Labs)

### 1. Create a bucket
- Go to S3 → Create bucket  
- Bucket name must be globally unique  
- Choose region: ap-south-1  
- Keep “Block Public Access” ON  
- Versioning OFF (for now)  
- Create bucket  

### 2. Upload an object
- Inside bucket → Upload  
- Select any file  
- Upload  
- Observe metadata and object key  

### 3. Download an object
- Select object → Download  

### 4. Delete an object
- Select → Actions → Delete  

---

## Summary

- S3 is a global service, but each bucket lives in one region.  
- Bucket names must be globally unique.  
- A bucket is the container where you store objects.  
- Every uploaded file is an object.  
- S3 is private by default.  
- Maximum object size = 5 TB.  
- Upload limit per single PUT request = 5 GB.  
Larger uploads require multipart upload.  
- Durability = 11 nines achieved through multi-AZ replication.  
- Cross-Region Replication is optional, not default.  
- S3 is not a filesystem; it is a key-value object store.  
- Object key is the unique string identifier of each object.
- object key = full path string (not file system path)

---

