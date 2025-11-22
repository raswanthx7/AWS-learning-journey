# VPC Peering 

## Topics Covered
- What is VPC Peering
- Why VPC Peering is used
- Understanding CIDR Overlap & Why Peering Fails
- Step-by-step VPC Peering setup (Console)
- Why to use the private IP for testing VPC Peering
- Common issues and troubleshooting

---

## 1. What is VPC Peering
VPC Peering is a networking connection that privately links two VPCs so they can communicate using **private IP addresses** through AWS’s internal backbone network.

There is no NAT, no Internet Gateway, and no public internet routing involved in the peering path.


---

## 2. Why VPC Peering Is Used
create VPC Peering when:
- Two VPCs need to communicate privately
- Resources in different VPCs must reach each other without using the internet
- Applications are separated for security/architecture but still need connectivity

Example:  
**VPC-A = App Layer**  
**VPC-B = Database Layer**  
Peering helps them talk privately.

---

## 3. Understanding CIDR Overlap & Why Peering Fails

### What is CIDR Overlap?
Two VPCs “overlap” when their CIDR ranges intersect.  
Example of overlap:
- VPC-A: `10.0.0.0/16`
- VPC-B: `10.0.10.0/24`

In this case, `10.0.10.0/24` lies **inside** `10.0.0.0/16`.  
AWS does **not allow peering** between VPCs with overlapping IP ranges.

### Why does AWS block overlapping VPCs?
Because routers cannot decide **which network** a packet belongs to.  
If both VPCs contain something like `10.0.10.5`, AWS will not know where to send it.

This leads to:
- Ambiguous routing  
- Incorrect packet delivery  
- Security problems  

### Correct Example (Non-overlapping CIDR)
- VPC-A: `10.0.0.0/16`
- VPC-B: `10.1.0.0/16`  
These do **not** overlap → peering is allowed.

### Simple explanation:
**If two VPCs have even 1 IP in common, peering will fail.**

---

## Requirements Before Peering
- CIDR blocks of both VPCs **must not overlap**
- Route tables must be updated on **both sides**
- Security Groups must allow traffic from the **other VPC’s CIDR**
- Use **private IPs** for testing

---

## Step-by-Step Setup (Console Based)

### Step 1 — Create Two VPCs
1. Open AWS Console → VPC → Your VPCs → Create VPC  
2. Create:
   - **VPC-A** → Example CIDR: `10.0.0.0/16`
   - **VPC-B** → Example CIDR: `10.1.0.0/16`
3. Important:  
   - CIDRs must not overlap  
   - Choose “VPC only” option for simplicity  

---

### Step 2 — Create Subnets
1. For VPC-A → Create Subnet → Example: `10.0.1.0/24`
2. For VPC-B → Create Subnet → Example: `10.1.1.0/24`
3. Keep them in the same region (required for peering)

---

### Step 3 — Internet Gateway (Only If You Need Public Access)
- needed public access **only to SSH from your laptop**.

So:

1. Create IGW  
2. Attach IGW to the VPC you want to SSH into (in your case **VPC-A**)  
3. Go to Route Table of VPC-A subnet → Add:  
   `0.0.0.0/0 → igw-xxxxx`
4. Ensure EC2 instance has:
   - Auto-assign public IP = **Enabled**
   - Security group allowing SSH from your laptop IP

**Note:**  
VPC-B does NOT need an IGW because you are NOT connecting to it from the internet.

---

### Step 4 — Launch EC2 Instances
One instance in each VPC:

- Instance-A in VPC-A subnet  
- Instance-B in VPC-B subnet  

Security Group rules to set:
- Allow **ICMP** from the other VPC CIDR  
- Allow **SSH** if needed for testing  

Example:  
In VPC-A SG inbound → allow ICMP from `10.1.0.0/16`  
In VPC-B SG inbound → allow ICMP from `10.0.0.0/16`

---

### Step 5 — Create VPC Peering Connection
1. VPC Console → Peering connections → Create Peering
2. Requester: **VPC-A**
3. Accepter: **VPC-B**
4. Create

Now go to Peering connections → select → **Accept Request**

Status must become:  
**Active**

---

## Step 6 — Update Route Tables (Most Important Step)
This is what enables communication.

For VPC-A Route Table:
Destination: `10.1.0.0/16`
Target: `pcx-xxxxx`

For VPC-B Route Table:
Destination: `10.0.0.0/16`
Target: `pcx-xxxxx`


Without these, **peering will NOT work**.  
This is the step beginners forget.

---

## Step 7: Test Connectivity
**Get private IP of Instance-B**

EC2 → Instances → Instance-B → Copy private IP (example: 10.1.1.55)

**SSH into Instance-A**

(or connect from Session Manager)

Run:
```bash
ping 10.1.1.55
```

If ICMP allowed, you will get replies.

If fails → check:

- Security group inbound rules
- Route table entries
- Peering status = Active
- CIDRs do not overlap

---

## Why to use the private IP for testing VPC Peering

Because VPC Peering only routes **private traffic** inside AWS’s internal network.

### What happens if you try using the public IP?

If ping/SSH to the public IP of VPC-B:

traffic will leave AWS, hit the Internet Gateway, go out to the public internet, then come back into AWS and reach the instance.

### Why AWS forces private IP use

Because:

- Peering is meant for internal, private, secure communication
- It exists to avoid going through the internet

**Example**

You SSH into EC2-A (10.0.1.10).
You ping EC2-B (10.1.2.20).

Result: Success
Because → the traffic uses the peering link.

If you ping EC2-B’s public IP (3.x.x.x):
Result: **This goes through IGW → internet**
Peering not used.

Even if peering is configured perfectly, pinging the public IP will never use peering.

---

## 7. Troubleshooting

### EC2 cannot ping through peering
- Check that both route tables have the correct destination and target
- Ensure VPC CIDRs are non-overlapping
- Check security groups (ICMP must be allowed)
- Check that peering connection is accepted and in active state

### SSH not working
- Internet Gateway may not be created
- Subnet may not be public
- Route table may not have 0.0.0.0/0 to IGW
- Security group may not allow SSH

---

## Conclusion
VPC peering allows secure private communication between two VPCs without using the internet. Proper CIDR design, security group rules, and updating route tables are essential for the setup to work.

---
