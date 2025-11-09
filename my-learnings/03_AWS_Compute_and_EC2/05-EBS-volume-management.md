# EBS Volume Management

##  Topics Covered
- Introduction to EBS  
- EBS Volume Types  
- Creating and Attaching an EBS Volume  
- Formatting and Mounting the Volume  
- Unmounting, Detaching, and Deleting EBS  
- Precautions Before Deletion or Termination  
- Snapshots (Backup and Restore)  
- Hands-On: EC2 Backup Using Snapshot  
- Real-World Usage and Notes  

---

##  Introduction to EBS

**Amazon EBS (Elastic Block Store)** provides persistent block-level storage for EC2 instances.  
It behaves like a hard drive that you can attach to your instance — store OS files, databases, logs, or any data that requires consistent I/O performance.

Each **EBS volume** exists **independently** from the life of an EC2 instance, meaning your data remains even if the instance stops or restarts.

EBS is:
- **Durable:** Data persists after instance stop/restart.  
- **Scalable:** You can resize volumes anytime.  
- **High-Performance:** You can choose between SSD (fast I/O) and HDD (low-cost) types.  
- **Backup Ready:** Supports **snapshots** for backup and recovery.  

---

##  EBS Volume Types

| Type | Description | Use Case |
|------|--------------|----------|
| gp3 | General Purpose SSD, high performance | Default choice for most workloads |
| gp2 | Older generation general purpose SSD | Legacy workloads |
| io2 / io1 | Provisioned IOPS SSD | Databases, high I/O workloads |
| st1 | Throughput Optimized HDD | Big data, streaming workloads |
| sc1 | Cold HDD | Low-cost, infrequently accessed data |

---

##  Creating and Attaching an EBS Volume

### Step 1: Create a New Volume
1. Go to **EC2 → Elastic Block Store → Volumes**  
2. Click **Create Volume**  
3. Select:
   - **Volume Type** (e.g., gp3)
   - **Size** (in GiB)
   - **Availability Zone** (must match your EC2 instance’s AZ)

### Step 2: Attach to an EC2 Instance
1. Select the created volume → **Actions → Attach Volume**  
2. Choose your **instance** → specify **device name** (e.g., `/dev/xvdf`)

---

##  Formatting and Mounting the EBS Volume

1. Connect to EC2 using SSH  
2. Check if the new volume is visible:
   ```bash
   lsblk
   ```
Example output:
```bash
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk /
xvdf    202:80   0  10G  0 disk
```

3. Format the new volume:
```bash
sudo mkfs -t ext4 /dev/xvdf
```

4. Create a mount point:
```bash
sudo mkdir /data
```

5. Mount the volume:
```bash
sudo mount /dev/xvdf /data
```

6. Verify:
```bash
df -h
```

---

## Unmounting, Detaching, and Deleting

Before detaching or deleting, you must unmount the volume to avoid data corruption.

Unmount:
```bash
sudo umount /data
```

Detach from EC2:
- Go to AWS Console → Volumes → Actions → Detach Volume

Delete:
- Once detached, select → Actions → Delete Volume

---

## Precautions Before Deletion or Termination

1. **Always unmount** before detaching.
2. **Never delete** a volume with important data — create a snapshot first.
3. **Detach** before terminating an instance (if you want to reuse it).
4. For **root volumes**, data will be lost on termination unless “Delete on Termination” is unchecked.

---

## Snapshots (Backup and Restore)

**EBS Snapshots** are incremental backups stored in **Amazon S3.**
They help you back up data or create new volumes from existing ones.

Create a Snapshot:

Go to **EC2 → Volumes → Actions → Create Snapshot**

Enter a name and description

Restore from Snapshot:

Go to **Snapshots → Select Snapshot → Create Volume**

Attach to EC2 → Mount → Use it like a normal volume

 Snapshots are incremental — only changed data blocks are backed up after the first snapshot.

---

## Hands-On: EC2 Backup Using Snapshot

**Scenario:** You want to back up your EC2 instance storage before making system changes.

Steps:

1. Stop the instance (optional but safer)
2. Go to Volumes → Select root volume → Create Snapshot
3. After creation, verify snapshot under Snapshots tab
4. To restore:

   - Create a new volume from the snapshot
   - Attach it to a new EC2 instance
   - Mount and verify your data

This is a full backup-recovery cycle.

---

## Real-World Usage and Notes

- In production, automate snapshot creation using AWS Backup or Lambda scripts.
- Use gp3 for general workloads (best cost-performance).
- Use io2 only for high IOPS workloads like databases.
- Regularly monitor volume usage with:
```bash
lsblk
df -h
sudo file -s /dev/xvdf
```
- Unmount before detaching to prevent file system damage.

---

## Summary

EBS = Persistent block storage for EC2
→ Attach → Format → Mount → Use → Unmount → Detach → Delete/Snapshot → Resize → Restore

---
