# Understanding AWS VPC Architecture (The Foundation)

### Topics Covered
- What is a VPC
- Why AWS introduced VPC
- EC2-Classic vs VPC
- Core Components of VPC
- Tenancy (Default vs Dedicated)
- IPv4 CIDR Block Options (Manual Input vs IPAM Allocate)
- Real-world Analogy
- TechNova Scenario (Example)
- Hands-on: Creating Your First Custom VPC

---

## What is a VPC

A **Virtual Private Cloud (VPC)** is a logically isolated network within AWS where you can launch and manage your resources such as EC2 instances, databases, and load balancers.

You control the complete IP address range, subnets, routing, and network access. It’s like your private data center inside AWS.

---

## Why AWS Introduced VPC

Before VPC, AWS used a shared public environment called **EC2-Classic**.  
All instances lived in a single flat network with limited isolation and control.

This became a problem for security and customization, so AWS introduced **VPC**, giving each user the ability to create a completely private, secure, and customizable network.

---

## Core Components of a VPC

1. **VPC** – The main container that defines your private network boundary.
2. **Subnets** – Logical divisions inside a VPC (for example, public and private zones).
3. **Route Tables** – Define where network traffic should go.
4. **Internet Gateway (IGW)** – Allows communication between your VPC and the public internet.
5. **NAT Gateway** – Lets instances in private subnets access the internet (outbound only).
6. **Network ACLs (NACLs)** – Subnet-level firewall that filters inbound and outbound traffic.
7. **Security Groups** – Instance-level firewall that controls allowed traffic to/from specific instances.
8. **DHCP Options Set** – Defines DNS servers, domain names, and related network configuration options.

---

## Tenancy in AWS VPC

When creating a VPC, AWS asks for **tenancy type**.  
It decides **where** your EC2 instances physically run — not who can access them.

- **Default Tenancy** – Your instances share the same physical servers with other AWS customers, but remain fully isolated through virtualization and security.  
  This is secure, cost-effective, and used by most companies.

- **Dedicated Tenancy** – Your EC2 instances run on hardware reserved exclusively for your account.  
  This ensures physical isolation but costs more.

### Real-world Analogy
Think of AWS as an apartment building:
- **Default Tenancy**: You rent a room in a secure building. Other tenants live in the same building, but each door is locked and private.
- **Dedicated Tenancy**: You rent the entire building only for your company. No one else can use that hardware.

Most companies use **default tenancy**, except banks, healthcare, or government organizations that need strict physical isolation for compliance reasons.

---

## IPv4 CIDR Block Options

When creating a VPC, AWS gives two options to define your **IPv4 CIDR block**:

1. **Manual Input** – You manually enter a private IP range (e.g., `10.0.0.0/16`).  
   This is ideal for learning, small projects, and the AWS Free Tier.  
   You control the exact address range your VPC will use.

2. **IPAM Allocate IPv4 CIDR Block** – Uses **AWS IP Address Manager (IPAM)** to automatically assign a CIDR block from a managed pool.  
   This is used by large organizations managing multiple VPCs across regions or accounts to avoid overlapping IP ranges.  
   IPAM is a **paid service** and not needed for basic learning.

**Precaution (Free Tier):** Always select **Manual Input** to avoid extra charges.

---

## Real-world Analogy of VPC

A VPC is like a private land owned by your company inside AWS.
- You decide the boundaries of your land (CIDR block).
- You build separate areas on that land (subnets).
- You create roads for communication (route tables).
- You place a gate to the public world (internet gateway).
- You set up guards at each level (NACLs and Security Groups).

---

## TechNova Scenario

Company: **TechNova**

Goal: Create a secure network setup for two types of servers:
- **Web Server** → Should be public-facing.
- **Database Server** → Should remain private and isolated.

Step 1: Create the VPC itself — this is your foundation.

### Hands-On

1. Go to the AWS Management Console → VPC → Your VPCs → Create VPC.
2. Choose **VPC only**.
3. Enter details:
   - **Name**: `TechNova-VPC`
   - **IPv4 CIDR block**: `10.0.0.0/16` (Manual input)
   - **IPv6 CIDR block**: None (optional for now)
   - **Tenancy**: Default (Free Tier safe)
4. Click **Create VPC**.
5. Observe the auto-created Route Table and NACL.
6. Verify that no Internet Gateway is attached yet.

### Outcome
You have now created your own isolated private network — the foundation of all networking in AWS.

In the next step, you’ll create public and private subnets inside this VPC and connect them through routing and gateways.

---

