# Elastic Network Interface (ENI) 

## Topics Covered
- What is an ENI
- Why ENI exists
- Where ENI lives inside VPC
- Components of an ENI
- Primary vs Secondary ENI
- Attaching and detaching ENIs
- When companies use ENIs
- When AWS exams ask about ENIs
- Practical steps and verification

---

# 1. What is an ENI
An Elastic Network Interface (ENI) is a virtual network card for an EC2 instance.
It provides the network identity for the instance.

An EC2 instance gets:
- private IP
- public IP (optional)
- MAC address
- security groups

from the ENI, not from the instance itself.

---

# 2. Why ENI Exists
Before ENIs, EC2 instances were tightly bound to their network configuration.
If an instance failed, the network identity failed with it.

ENI separates:
- compute (EC2)
- networking (ENI)

This allows modular networking and flexible designs.

---

# 3. Where ENI Lives
- ENI lives inside a **subnet**.
- ENI belongs to an **Availability Zone**.
- EC2 instance attaches one or more ENIs.
- Without ENI, EC2 has no networking.

---

# 4. Components of an ENI
An ENI contains:
- Primary private IPv4 address
- Optional secondary private IPv4 addresses
- One public IP or Elastic IP (attached to primary IP only)
- MAC address
- One or more security groups
- Subnet association
- ENI ID
- Description (optional)

---

# 5. Primary vs Secondary ENI

## Primary ENI
- Created automatically when launching EC2.
- Holds the primary private IP.
- Cannot be detached while instance is running.
- Can be detached only when the instance is stopped.

## Secondary ENI
- Created manually.
- Can be attached to a running instance.
- Can be detached anytime.
- Used for special networking cases (rare in normal companies).

---

# 6. Attaching and Detaching ENIs

## Primary ENI
- Automatically attached.
- Only removable in stopped state.
- Required for instance boot and metadata access.

## Secondary ENI
- Hot attach/detach supported.
- Used for extra network cards, firewalls, IPS/IDS, multi-homed servers.

---

# 7. Real Company Usage
Most companies use:
- only the primary ENI
- no secondary ENI

Reason:
- Auto Scaling handles failover, not ENI movement.
- Secondary ENI is used only in niche cases.

---

# 8. ENI in AWS Exams
ENI appears in questions like:
- “Instance failed, but you need to preserve the private IP.”
- “You want to move a network interface to another instance.”
- “You need multiple private IPs for legacy applications.”
- “You need multiple ENIs for firewall or traffic inspection.”

Exam expects:
- primary ENI: detachable only when stopped
- secondary ENI: hot attach/detach possible

---

# 9. Practical Steps

## View ENI
- Go to EC2 console
- Networking tab
- Click the Network Interface ID

## Create ENI
- EC2 Dashboard → Network Interfaces
- Create Network Interface
- Select subnet
- Select security groups
- (Optional) assign private IPs

## Attach ENI
- Actions → Attach
- Choose instance

## Detach ENI
- Actions → Detach
- Only works if:
  - Secondary ENI (anytime)
  - Primary ENI (only when instance is stopped)

---


