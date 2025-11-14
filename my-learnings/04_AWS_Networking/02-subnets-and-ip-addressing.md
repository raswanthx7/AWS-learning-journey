# CIDR and Subnet Design

## Topics Covered
- What is CIDR
- Why CIDR was introduced
- The problem before CIDR
- CIDR notation explained
- Subnetting logic
- AWS VPC IP addressing
- Reserved IPs in AWS
- Designing public and private subnets
- Hands-on subnet creation

---

## What is CIDR

CIDR (Classless Inter-Domain Routing) is a modern way of defining IP address ranges.  
Instead of using fixed classes (A, B, C), CIDR lets you define custom-sized IP blocks.  
Example: `10.0.0.0/16` gives 65,536 IPs, while `10.0.1.0/24` gives 256 IPs.

---

## Why CIDR Was Introduced

Before CIDR, networks used rigid classes:  
- Class A → 16 million IPs  
- Class B → 65,000 IPs  
- Class C → 256 IPs  

This wasted a lot of addresses. CIDR solved this by allowing flexible subnet sizes.  
You can now allocate just the number of IPs you actually need.

---

## Problem Before CIDR

Earlier, a small company might get an entire Class B (65,000 IPs) even if they needed only a few hundred.  
This led to **IPv4 exhaustion**. CIDR made address allocation efficient and scalable.

---

## CIDR Notation Explained

CIDR notation looks like this:  
`10.0.0.0/16`

Here:
- `10.0.0.0` → Network Address  
- `/16` → Prefix length (number of bits reserved for network)  

The remaining bits are used for hosts.  
Smaller prefix → More hosts (e.g., `/16` = 65,536 IPs)  
Larger prefix → Fewer hosts (e.g., `/24` = 256 IPs)

---

## Subnetting Logic

Subnetting divides a big CIDR block into smaller, manageable chunks.  
For example:  
If your VPC CIDR is `10.0.0.0/16`, you can create subnets such as:
- `10.0.1.0/24` → Public Subnet  
- `10.0.2.0/24` → Private Subnet  

This logical separation helps isolate layers (web, app, DB) and improve security.

---

## AWS VPC IP Addressing

When you create a VPC, you assign it a CIDR block — typically `/16`.  
You can later divide that range into multiple subnets across Availability Zones.  

**AWS automatically reserves 5 IP addresses in each subnet:**
1. Network address
2. VPC router
3. DNS
4. Future use
5. Broadcast address (not used in AWS but reserved)

So in a `/24` subnet (256 IPs), only **251** are usable.

---

## Designing Public and Private Subnets

Let’s assume you have `10.0.0.0/16` for your VPC.  
You can design:
- **Public Subnet:** `10.0.1.0/24` → for web servers (Internet-facing)
- **Private Subnet:** `10.0.2.0/24` → for database or app servers (internal)

Each subnet should be in a different AZ for high availability.

---

## Hands-On Practice

1. Create a new VPC → `10.0.0.0/16`
2. Create two subnets:
   - **Public Subnet:** `10.0.1.0/24` in `ap-south-1a`
   - **Private Subnet:** `10.0.2.0/24` in `ap-south-1b`
3. Observe:
   - Each subnet automatically gets a route table and NACL.
   - Check subnet details → available IPs = total - 5 reserved.
4. Validate that both subnets don’t overlap.

---

## Key Takeaways
- CIDR enables flexible and efficient IP allocation.
- AWS reserves 5 IPs in every subnet.
- Logical subnet design avoids conflicts and simplifies scaling.
- Always plan subnet CIDRs carefully before deployment.

---

