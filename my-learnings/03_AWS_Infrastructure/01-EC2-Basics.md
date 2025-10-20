#  EC2 Instance Launch & Core Components

### **Topics Covered**
- What is EC2  
- AMI (Amazon Machine Image)  
- Instance Types  
- Key Pairs  
- Storage (EBS)  
- Launching Your First EC2 Instance  

---

##  What is EC2?

**EC2 (Elastic Compute Cloud)** is an AWS service that provides **virtual servers** in the cloud.  
Instead of running physical servers, EC2 allows you to launch **instances (virtual machines)** within seconds and manage them like real computers.

 **Purpose:** Run applications, host websites, perform computations, or test environments — all in the cloud.  
 Think of EC2 as your **personal Linux or Windows machine**, running inside AWS.

---

##  Amazon Machine Image (AMI)

**AMI** is a **blueprint/template** used to create your EC2 instance.  
It defines the operating system (OS) and pre-installed software on your instance.

 **Analogy:** AMI = the mold used to build your virtual machine.

### **Examples**
- Amazon Linux 2 (Free Tier eligible)
- Ubuntu Server 22.04
- Windows Server
- Custom AMIs (preloaded with software or configuration)

### **Why Important?**
Because it determines what environment your instance starts with — like whether it’s ready for Python, Java, or web hosting.

---

##  Instance Types

Each EC2 instance type defines **hardware configuration** (CPU, RAM, network performance).

AWS organizes instance types into **families**:

| Family | Purpose | Example | Use Case |
|---------|----------|----------|----------|
| **T Family (e.g., t3.micro)** | General purpose | Balanced CPU & RAM | Free Tier, web server, testing |
| **M Family** | Balanced compute + memory | m5.large | Application servers |
| **C Family** | Compute optimized | c7g.large | Batch processing, gaming |
| **R Family** | Memory optimized | r6g.large | Databases, caching |

### **Why Important?**
Selecting the correct instance type ensures performance without overspending.

---

##  Key Pairs

Key pairs are used to **securely connect** to your EC2 instance through SSH.

A key pair contains:
- **Public key** → Stored on AWS.
- **Private key (.pem)** → Downloaded by you.

### **Purpose**
When you connect via SSH, AWS verifies that your **private key** matches the **public key** stored in the instance.  
If it matches → you’re authenticated.

 Without the `.pem` file, you can’t access your EC2 — it’s your **only identity proof**.

---

##  Storage (EBS - Elastic Block Store)

EBS acts as your instance’s **hard disk**.

- Default volume = 8 GB (Free Tier eligible)
- You can increase size later
- Data persists even after stopping the instance

### **Types Overview**
| Type | Performance | Typical Use |
|------|--------------|--------------|
| **gp2 / gp3** | General purpose | Default storage |
| **io1 / io2** | High IOPS | Databases |
| **st1** | Throughput optimized | Big data, logs |
| **sc1** | Cold HDD | Rarely accessed data |

 You’ll deep dive into these on the **EBS module** later.

---

##  Launching Your First EC2 Instance

**Step Overview (Free Tier Safe):**

1. **Search “EC2”** → Click “Launch instance”.
2. **Name your instance** → e.g., `first-linux-server`.
3. **Select AMI:**  
   - Choose **Amazon Linux 2 (Free Tier eligible)**.
4. **Select Instance Type:**  
   - Choose **t3.micro** (Free Tier).
5. **Create Key Pair:**  
   - Name → `stark-key`  
   - Type → RSA  
   - Format → `.pem` (Download it once)
6. **Storage:**  
   - Keep default (8 GB gp3).
7. **Security Group:**  
   - Allow **SSH (22)** temporarily (we’ll secure later).
8. **Launch Instance.**

 Instance created successfully → You now have your first cloud-based Linux server.

---

##  Free Tier Safety Tips

- Only 1 instance (t3.micro or t4g.micro) = 750 hrs/month.  
- Always **Stop instance** after using.  
- Never create multiple instances.  
- Avoid Elastic IPs or Snapshots now (we’ll cover later).  
- Check usage in **Billing → Free Tier Usage**.

---

 **Conclusion**

You’ve learned:
- What EC2 is and why it matters.
- The roles of AMI, instance types, key pairs, and storage.
- How to safely launch and manage your first virtual machine.

Next up: **Connecting and managing your EC2 instance (SSH, Apache, etc.)**

---


