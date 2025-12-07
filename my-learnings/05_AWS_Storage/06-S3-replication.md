# S3 Replication (CRR & SRR)

## Topics Covered
- What replication is
- Versioning requirement
- CRR (Cross-Region Replication)
- SRR (Same-Region Replication)
- What gets replicated
- What does not replicate
- Delete marker replication
- Existing object replication
- Batch Operations (for existing objects)
- Hands-on behavior 

---

# 1. What is S3 Replication

Replication automatically copies objects from a **source bucket** to a **destination bucket**.  
It is used to keep a second copy, either in the **same region** (SRR) or in a **different region** (CRR).

Important:
- Replication is asynchronous
- Not instant, but fast

---

# 2. Requirements

Replication requires:
- Versioning enabled on source bucket
- Versioning enabled on destination bucket
- Replication rule configured
- IAM role for replication

---

# 3. CRR (Cross-Region Replication)

## What it is
Replicates objects to another region, for example:
- Source: ap-south-1 (Mumbai)
- Destination: ap-southeast-1 (Singapore)

## Why used
- Disaster Recovery (DR)
- Compliance (store outside the original region)
- Global presence (expand service to another region)
- Lower latency for regional users (depending on application routing)

---

# 4. SRR (Same-Region Replication)

## What it is
Replicates objects within the same region, for example:
- ap-south-1 → ap-south-1

## Why used
- Secondary copy
- Analytics bucket separate from production bucket
- Backup-like separation
- Keep read-only copies

---

# 5. What Gets Replicated

Replication copies:
- Objects
- Object versions (must have versioning)
- Metadata
- (Optionally) delete markers

---

# 6. What Does NOT Replicate Automatically

- Existing objects before replication rule (unless you choose “replicate existing objects” and run a batch job)
- Objects that the IAM replication role cannot access
- Some encryption scenarios without correct KMS policies

Note:
Replication does **not** automatically route traffic for latency—that’s application logic or CloudFront.

---

# 7. Delete Marker Replication

If you **enable** delete marker replication:
- When you delete an object in source
- A delete marker appears in destination as well

If you **disable** delete marker replication:
- Delete happens only in source
- Destination keeps object visible

This is important for backup-like logic.

---

# 8. Existing Object Replication (Important)

By default:
- Replication only applies to new objects uploaded after the rule is created.

If you want old objects to also replicate:
- Check “Replicate existing objects”
- AWS asks to create an S3 Batch Job
- Batch job copies existing data one time

---

# 9. Batch Operations

When you selected “Replicate existing objects”, AWS opened a screen for creating a Batch Copy Job.  
That screen was **not** a replication configuration—  
it was asking where to store the **batch job report** (CSV), and what permissions to use.

Only the batch job copies old data.

Replication rule handles new uploads.

---

# 10. Hands-On Behavior

### A) Create bucket in region A (source)
- Enabled versioning

### B) Create bucket in region B (destination)
- Enabled versioning

### C) Create replication rule
- Applied to all objects (no prefix)
- Selected delete-marker replication (optional)
- Chose “Create new role”

### D) Uploaded a new file in source
- After a few seconds
- File appeared automatically in destination

**This confirmed replication is working.**

---

# 11. How to Test Replication (Simple and Reliable)

1. Upload a new file to source bucket
2. Refresh destination bucket
3. If the file appears → replication is correct

Do not use old objects as a test; they require batch operations.

---

# 12. Behavior After Deleting an Object

If delete marker replication is enabled:
- Source delete marker is copied to destination
- Object becomes hidden in both buckets

If disabled:
- Delete hides object in source only
- Destination object remains visible

---

# 13. Notes

- Replication ≠ backup (because deletes replicate too)
- Replication is asynchronous
- Old objects require batch job
- You always test with new objects
- Delete marker behavior is optional

---

# 14. Practical Use Cases

CRR:
- Disaster recovery
- Multi-region architecture
- Compliance (data residency)
- International expansion of services

SRR:
- Production → Analytics separation
- Create read-only copies
- Backup-like separation without leaving the region

---

# Summary

- Enable versioning on both buckets
- Create replication rule
- Only new objects replicate automatically
- Delete markers can replicate if enabled
- To copy old objects, run “replicate existing” batch job
- Replication is used for DR, compliance, and multi-region architectures

---

