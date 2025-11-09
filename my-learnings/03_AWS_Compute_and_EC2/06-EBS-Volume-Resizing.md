#  AWS EBS Volume Resizing

###  Topics Covered
1. What is EBS Resizing  
2. Why Resizing is Needed  
3. Difference Between Volume Size Increase and Filesystem Resize  
4. Step-by-Step: Modify EBS Volume in AWS Console  
5. Verify in EC2 Instance  
6. Extend Partition (growpart or fdisk)  
7. Resize Filesystem (XFS and EXT4) 
8. Verify the Resizing  
9. Common Errors and Precautions  
 
---

##  What is EBS Resizing?

EBS resizing means increasing the **storage size** of your existing EBS volume **without losing data**.  
AWS allows you to modify the volume size online — no need to stop or detach the instance.

When you increase an EBS volume, only the raw volume size changes initially.  
Your **EC2 instance still “sees” the old filesystem size** until you extend the partition and resize the filesystem.

---

##  Why Resizing is Needed

- The application or database needs more storage space.  
- You are running out of space on `/var`, `/home`, or `/` partitions.  
- You want to upgrade from smaller to larger volume for better performance.  

Example:  
You have a 10 GB EBS volume mounted at `/`, but disk usage is already 90%.  
Instead of creating a new volume, you **resize the existing one** to 20 GB or 30 GB.

---

##  Volume Size vs. Filesystem Resize

| Action | Where It Happens | Purpose |
|--------|------------------|----------|
| Modify Volume | AWS Console / CLI | Increases physical EBS size |
| Extend Partition | Inside Linux (growpart/fdisk) | Makes partition use new space |
| Resize Filesystem | Inside Linux (xfs_growfs or resize2fs) | Expands filesystem to use new space |

> In short: **3 steps** → Increase volume → Extend partition → Resize filesystem.

---

## Step-by-Step: Modify EBS Volume in AWS Console 

###  Step 1: Modify the EBS Volume in AWS Console

1. Go to **EC2 → Elastic Block Store → Volumes**.  
2. Select your target volume.  
3. Click **Actions → Modify Volume**.  
4. Increase the **Size (GiB)** to your desired value (e.g., 8 GB → 15 GB).  
5. Click **Modify → Yes** to confirm.  
6. Wait for the **State** to show “in-use – optimizing”.

You can also do this using the AWS CLI:
```bash
aws ec2 modify-volume --volume-id vol-xxxx --size 15
```
### Step 2: Verify in EC2 Instance

After resizing the volume in AWS Console, log in to your instance and check if the volume shows increased size:
```bash
lsblk
```

If you don’t see the new size yet, reload partition info:
```bash
sudo partprobe
```

Check again:
```bash
lsblk
```

---

### Step 3: Extend the Partition

**Why:**
- When you increase the size of an EBS volume in AWS, the operating system doesn’t automatically recognize or use that extra space — it just sees the original partition size.
- `growpart` tells Linux: “Hey, extend this partition boundary to include the newly available space on the disk.”
- If the partition doesn’t automatically expand to fill the extra space, extend it manually.

For **modern systems (using cloud-init or NVMe disks)**:
```bash
sudo growpart /dev/nvme0n1 1
```
Here:

- `/dev/nvme0n1` → Disk name
- `1` → Partition number

**For old disks (SDA types):**
```bash
sudo growpart /dev/xvda 1
```

You can verify again using:
```bash
lsblk
```

### Step 4: Resize the Filesystem

**Why:**
- `growpart` only expands the partition, not the filesystem. Think of the partition as a “box” and the filesystem as the “contents” inside it. You’ve made the box bigger, now you’re telling the filesystem to fill the full box.
- Once the partition is extended, you must resize the filesystem to use the new space.

**For XFS filesystems:**
```bash
sudo xfs_growfs /
```
(`mount point` — for example, `/` or `/data`)


### For EXT4 filesystems:
```bash
sudo resize2fs /dev/nvme0n1p1
```

(`partition name` — use `lsblk` to check)

---

### Step 5: Verify the Resizing

**Check final status:**
```bash
df -h
```

Compare output before and after resizing — you should see the new size.

You can also verify with:
```bash
lsblk
```
**Example Summary**

Before resize:
```bash
/dev/nvme0n1p1   8G   7.5G   500M   94%   /
```

After resize:
```bash
/dev/nvme0n1p1  15G   7.5G   7.5G   50%   /
```
---

## Common Errors & Precautions

- Never resize a mounted root volume if the instance is in heavy use — do it during maintenance time.
- Always unmount data volumes before resizing them (if not root).
- Take a snapshot before resizing — this acts as your backup.
- After resizing, reboot the instance if the filesystem doesn’t reflect changes.
- If resizing fails, restore from the snapshot.

---

## Quick Recap

- Step 1: Modify the volume in AWS Console
- Step 2: Check disk size inside instance (`lsblk`)
- Step 3: Extend partition using `growpart`
- Step 4: Resize filesystem (`xfs_growfs` or `resize2fs`)
- Step 5: Verify with `df -h`

---

## Real-World Use Case

In a production AWS environment, you’ll often see EBS resizing done when:

- Application logs filled `/var/log`
- Database files exceeded allocated space
- CI/CD agent needs more space on build nodes

A cloud engineer must be able to safely resize volumes without downtime.

---

**EBS resizing is one of the most practical and frequently used maintenance tasks in cloud engineering.**

---
