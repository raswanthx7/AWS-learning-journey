# Private Subnet & NAT Gateway 

## Topics Covered
- What is a Private Subnet (real meaning)
- Why Private Subnets cannot access the internet directly
- What is a NAT Gateway
- Why NAT Gateway must sit in the **Public Subnet**
- Why NAT Gateway needs an Elastic IP
- How NAT performs source IP translation (SNAT)
- Routing flow: Private EC2 → NAT Gateway → Internet Gateway → Internet
- Step-by-step: creating Elastic IP
- Step-by-step: creating NAT Gateway
- Step-by-step: updating Route Table for Private Subnet
- SSH access method: Bastion Host (ONLY Bastion, no SSM)
- Why Bastion is needed for Private Subnet
- Verification steps (ping + curl)
---

# Private Subnet & NAT Gateway

## 1. What is a Private Subnet?
A **private subnet** is a subnet **without a direct route to the Internet Gateway**.
- No public IPs allowed.
- Resources inside cannot be reached from the internet.
- Ideal for: Databases, backend servers, internal apps.

### Why private subnets cannot access internet directly?
Because:
- They have **no public IP**.
- They have **no 0.0.0.0/0 → IGW route** in their Route Table.

So even if they try to reach the internet, the traffic has nowhere to go.

### Why Create a Private Subnet

Because not every server should be exposed to the public.
Security best practices:

- Public Layer → Only Load Balancers or Bastion
- Private Layer → Application, Database, Internal Services

Companies follow this to avoid:

- Attacks
- Unauthorized login
- Accidental exposure
- Compliance risks

---

## 2. What is a NAT Gateway?
A **NAT Gateway** is an AWS-managed service that allows **instances in private subnets to access the internet** while **preventing inbound traffic** from the internet.

In simple terms:
- It **masks** the private EC2's IP using **its own public IP**.
- Sends the request to the internet on behalf of the private instance.
- When the response comes back, it forwards it to the correct private instance.

### IMPORTANT: NAT Gateway is only for **outbound** internet traffic.
Private instance → Internet.  
NOT Internet → Private instance.

---

## 3. Why NAT Gateway must sit in the Public Subnet
NAT Gateway needs:
- A **public IP** (Elastic IP)
- A route to the **Internet Gateway**

Public subnets have:
- Auto-assign Public IP enabled (or manually attached)
- "0.0.0.0/0 → Internet Gateway" route

Private subnets DO NOT have the IGW route, so NAT cannot function inside them.

Therefore:
> NAT Gateway must always be created **in a Public Subnet**.

---

## 4. Why NAT Gateway needs an Elastic IP
When a private EC2 accesses the internet, its request must show a **public IP**.

Private IPs cannot leave AWS.

So NAT does:
- Source NAT (SNAT)
- Replaces the private EC2’s source IP (ex: 10.0.2.45)
- With the NAT Gateway’s Elastic IP

This is why NAT Gateway **must** have an Elastic IP.

---

## 5. Flow: How NAT Gateway Works (Step-by-Step)
1. Private EC2 sends request to internet (e.g. `yum update`, `curl`).
2. Private subnet's route table sends it to NAT Gateway.
3. NAT Gateway takes the packet.
4. NAT Gateway replaces private IP with its **public Elastic IP**.
5. NAT Gateway sends traffic to **Internet Gateway**.
6. IGW sends it to the public internet.
7. Response comes back to IGW.
8. IGW delivers to NAT Gateway.
9. NAT Gateway maps the connection back to the **correct private EC2**.
10. Private EC2 receives the response.

This is secure and clean.

---

# Creating NAT Gateway (Hands-On)

## Step 1: Allocate an Elastic IP
AWS Console → VPC → Elastic IPs → Allocate new Elastic IP.

Name it: `technova-nat-eip`.

---

## Step 2: Create NAT Gateway
AWS Console → VPC → NAT Gateways → Create NAT Gateway

- **Name:** `technova-nat`
- **Subnet:** select your **public subnet** (technova-public)
- **Connectivity type:** Public (default)
- **Elastic IP:** choose the EIP you created

Create.

(Warning: NAT Gateway costs money per hour + per GB)

---

## Step 3: Update Private Subnet Route Table
Go to Route Tables → select your **Private Route Table**.

Add route:
```
Destination: 0.0.0.0/0
Target: NAT Gateway (nat-xxxxxxxx)
```

Save.

Now Private Subnet has safe outbound internet.

---

## Step 4: Creating Private EC2 Instance — Step-by-Step
1. Launch EC2 Instance
- Select Amazon Linux 2 / Ubuntu
- Instance type: t2.micro (free tier)
2. Place it in the Private Subnet
- Choose VPC
- Choose Private Subnet
- Auto-assign Public IP: **DISABLED** (Disable for private and enable for public)
3. Attach Security Group
Private EC2 SG rules:
- Inbound: allow only internal traffic (e.g., from bastion SG)
- Outbound: allow all
4. DO NOT attach Internet Gateway or Public IP
5. Launch

---

# Bastion Host (Why & How)
You cannot SSH directly into private EC2 because:
- No public IP
- No IGW route
- No direct inbound traffic allowed

So we create:
### Bastion Host (Jump Server)
- A small EC2 in the **public subnet**
- Has a public IP
- Acts as a secure gateway

### SSH Flow using Bastion:
Your Laptop → SSH → Bastion → SSH → Private EC2

No inbound access from internet to private EC2.

---

# Verification Steps

## Test from Private EC2
```
ping google.com
curl https://aws.amazon.com
```
If it works → NAT Gateway is configured correctly.

## Test SSH via Bastion
From your laptop:
```
ssh -i key.pem ec2-user@<bastion-public-ip>
```
From Bastion:
```
ssh ec2-user@<private-ec2-private-ip>
```

If it works → Bastion + Private network is correct.

---

# Summary (Interview Level Notes)
- Private EC2 cannot reach the internet directly.
- NAT Gateway enables internet **outbound** traffic.
- NAT Gateway must be in **public subnet**.
- NAT Gateway must have an **Elastic IP**.
- Private subnet route table must point **0.0.0.0/0 → NAT Gateway**.
- Bastion Host is used to SSH into private EC2s.

---

