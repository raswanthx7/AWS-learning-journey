# Internet Gateway and Public Subnet routing

## Topics Covered
- What is an Internet Gateway (IGW)
- Why a Public Subnet needs internet connectivity
- Default routing inside a VPC
- Creating a Custom Route Table for Public Subnet
- Creating and Attaching an Internet Gateway
- Associating Route Table to Public Subnet
- Launching a Test EC2 Instance in Public Subnet **(including selecting the correct VPC & Subnet)**
- How 0.0.0.0/0 works
- How traffic flows: User → IGW → Route Table → EC2
- Clarifying misconceptions (router location, flow inside VPC)

---

# What is an Internet Gateway
An Internet Gateway is a VPC component that enables:
1. Instances inside the VPC to **access the internet** (outbound traffic).
2. Users on the internet to **access public resources** inside the VPC (inbound traffic).

It must be **attached** to a VPC to function.

---

# Why a Public Subnet Needs an IGW
A subnet becomes **public** only when its route table contains:
```
0.0.0.0/0 → Internet Gateway
```
This means:
> “If traffic is not meant for the VPC, send it to the internet.”

Without this → the subnet is private.

---

# Default Route Inside a VPC
Every VPC automatically has the route:
```
10.0.0.0/16 → local
```
This allows **internal communication** between all resources in the VPC.
It exists in every route table.

---

# Hands‑On: Creating an Internet Gateway
1. Go to **VPC → Internet Gateways**.
2. Click **Create Internet Gateway**.
3. Name it: `TechNova-IGW`.
4. Click **Attach to VPC** → Select your VPC `TechNova-VPC`.

Now your VPC supports internet connectivity.

---

# Hands‑On: Creating a Custom Route Table for Public Subnet
We never use the default route table for public routing.

Steps:
1. Go to **VPC → Route Tables**.
2. Click **Create Route Table**.
3. Name: `Public-RT`.
4. Select your VPC `TechNova-VPC`.
5. Create.

Add internet route:
1. Open `Public-RT`.
2. Go to **Routes → Edit Routes**.
3. Add:
```
Destination: 0.0.0.0/0
Target: Internet Gateway (TechNova-IGW)
```
4. Save.

---

# Hands‑On: Associate Public Subnet to Public Route Table
1. Open `Public-RT`.
2. Go to **Subnet Associations**.
3. Click **Edit**.
4. Select your **Public Subnet**.
5. Save.

Now the public subnet has internet access.

---

# Hands‑On: Launching a Test EC2 Instance **(Selecting VPC + Subnet)**
When launching EC2, AWS does **not automatically select your custom VPC**.
You must manually choose it.

Steps:
1. Go to **EC2 → Launch Instance**.
2. Choose **Amazon Linux** or **Ubuntu**.
3. Scroll to **Network Settings**.
4. Under **VPC**, choose: `TechNova-VPC`.
5. Under **Subnet**, choose: `TechNova-Public-Subnet`.
6. Enable **Auto‑assign Public IP → Enabled**.
7. Create a security group allowing:
   - SSH (22)
   - HTTP (80)
8. Launch the instance.

Test internet:
```
ping google.com
```
If the ping works → Your IGW + Route Table + Public Subnet setup is correct.

---

# Why We Create a Custom Route Table
We must separate public and private routing.

**Public Route Table:**
```
10.0.0.0/16 → local
0.0.0.0/0   → igw-xxxx
```

**Private Route Table :**
```
10.0.0.0/16 → local
```
(no IGW route)

---

# Route Table Concepts
**Destination** – IP range the packet wants to reach.  
**Target** – Where AWS sends the traffic (IGW, NAT, Local).  
**Route Origin** – Who added the route (AWS or user).  
**Propagated** – Ignore unless using VPN/Direct Connect.

---

# Why 0.0.0.0/0
It means:
> “Send all internet‑bound traffic to this target.”

Anything outside the VPC CIDR → goes to the IGW.

---

# Traffic Flow (User → IGW → EC2)
1. User types domain.
2. DNS resolves to EC2/LB public IP.
3. Traffic enters the VPC through the IGW.
4. Router checks the route table.
5. Identifies EC2’s private IP.
6. Sends traffic internally.
7. EC2 responds to the user.

---

# Misconceptions Cleared
- The route table is **logically** attached to subnets; it is not a physical device.
- There is **one VPC router** managed by AWS.
- Load Balancers use **private IPs**, not EC2 public IPs.

---

# Summary 
 completed public subnet internet setup by:
1. Creating & attaching an Internet Gateway.
2. Creating a dedicated Public Route Table.
3. Adding `0.0.0.0/0 → IGW`.
4. Associating the table with the public subnet.
5. Launching an EC2 instance **inside your custom VPC + subnet**.
6. Testing internet connectivity.

---

