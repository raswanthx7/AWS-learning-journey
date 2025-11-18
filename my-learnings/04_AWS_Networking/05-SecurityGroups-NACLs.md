# Network ACL (NACL) and Security Groups (SG) 

## Topics Covered
- What is a Security Group (SG)
- What is a Network ACL (NACL)
- Difference between SG and NACL
- Location and Scope
- How to Create and Attach Security Groups
- How to Create and Associate NACLs
- NACL Rule Fields Explained
- Step-by-Step Practical Configuration
- Testing Traffic Flow
- Key Takeaways

---

## 1. Security Group (SG)

### What is a Security Group?
- A **virtual firewall** for your EC2 instance.
- Controls **inbound and outbound traffic** at the **ENI level**.
- **Stateful**: Response traffic is automatically allowed, no need to explicitly allow return traffic.
- You can attach **one or more SGs** to an ENI.
- If the instance has multiple ENIs, each ENI can have **different SGs**.

### Location and Scope
- Attached to **Elastic Network Interface (ENI)**.
- EC2 instance inherits the SG rules via the attached ENI.

### How to Create a Security Group
1. Go to **AWS Console → EC2 → Security Groups → Create Security Group**
2. Provide:
   - **Name** – e.g., `WebServerSG`
   - **Description** – e.g., `SG for public web server`
   - **VPC** – Select the correct VPC
3. Add **Inbound Rules**:
   - Example:
     - Type: SSH | Protocol: TCP | Port: 22 | Source: 0.0.0.0/0 | Allow
     - Type: HTTP | Protocol: TCP | Port: 80 | Source: 0.0.0.0/0 | Allow
   - Add more as required.
4. Add **Outbound Rules** (default: allow all)
5. Click **Create**

### How to Attach / Detach SG
- **Attach**: Go to **EC2 → Networking → Security Groups → Attach Security Group**
- **Detach**: Go to **EC2 → Networking → Security Groups → Remove Security Group**
- Minimum **1 SG must be attached** at all times.
- Attaching/detaching updates the **ENI**, not directly the EC2 instance.

---

## 2. Network ACL (NACL)

### What is a Network ACL?
- A **subnet-level firewall** for controlling traffic.
- **Stateless**: Responses must be explicitly allowed in outbound rules.
- Can **allow or deny** traffic.
- Applied to **subnets**, not individual EC2 instances.
- Evaluated **in rule number order** (lowest first).

### Location and Scope
- Each **subnet must have a NACL**.
- Default NACL is automatically created for each VPC and allows all traffic.

### NACL Rule Fields Explained
| Field | Description | Example / Notes |
|-------|------------|----------------|
| Rule Number | Priority of evaluation, lowest first | 100, 110, 120… |
| Type | Predefined traffic type | SSH, HTTP, HTTPS, Custom TCP, All Traffic |
| Protocol | Protocol number (TCP=6, UDP=17, ALL=-1) | Default auto-filled for Type |
| Port Range | Port or port range | 22 (SSH), 80 (HTTP), 443 (HTTPS) |
| Source / Destination | IP range allowed or denied | 0.0.0.0/0 for public internet |
| Allow / Deny | Permit or block traffic | Allow for public access, Deny to block |

### How to Create a NACL
1. Go to **AWS Console → VPC → Network ACLs → Create Network ACL**
2. Provide:
   - **Name** – e.g., `PublicSubnetNACL`
   - **VPC** – Select the correct VPC
3. Add **Inbound Rules**:
   - Example:
     | Rule # | Type  | Protocol | Port Range | Source       | Allow/Deny |
     |--------|-------|----------|------------|-------------|------------|
     | 100    | SSH   | TCP      | 22         | 0.0.0.0/0   | Allow      |
     | 110    | HTTP  | TCP      | 80         | 0.0.0.0/0   | Allow      |
     | 120    | HTTPS | TCP      | 443        | 0.0.0.0/0   | Allow      |
4. Add **Outbound Rules**:
   - Example:
     | Rule # | Type       | Protocol | Port Range | Destination  | Allow/Deny |
     |--------|-----------|----------|------------|-------------|------------|
     | 100    | All Traffic | ALL     | ALL        | 0.0.0.0/0   | Allow      |
5. **Associate NACL with Subnet**:
   - Go to **Subnet Associations → Edit → Select subnet → Save**
6. Now this NACL protects all EC2 instances inside the subnet.

---

## 3. Step-by-Step Testing

### Scenario
- Public subnet → EC2 instance
- Goal: Allow SSH (22) and HTTP (80), block HTTP from private subnet

**Steps**
1. Attach SG to EC2: allow **22 and 80 inbound**, outbound allow all
2. Create custom NACL:
   - Public subnet inbound: allow 22, 80  
   - Public subnet outbound: allow all  
   - Private subnet inbound: deny 80, allow others
3. Test traffic:
   - SSH → should work
   - HTTP public subnet → should work
   - HTTP private subnet → should fail immediately
4. Modify NACL: deny inbound 80 public subnet → HTTP stops immediately

**Conclusion**: NACL acts first at **subnet level**, then SG at **ENI/instance level**.

---

## 4. Key Takeaways
- **SG = instance/ENI-level, stateful, attach/detach anytime**  
- **NACL = subnet-level, stateless, rule order matters, replaceable for subnets**  
- Testing shows **real-world traffic flow**:  
Internet → NACL (subnet) → ENI (SG) → EC2
- Rule numbers in NACL = priority  
- SG automatically allows response traffic; NACL does not

---

## 5. Best Practices
- Use **SGs for instance-level security**, NACLs for **subnet-level filtering or extra security layer**  
- Keep **SG minimal**, NACL simple, avoid conflicts  
- Test changes **before production**  
- Document rules for future reference or interview discussion

---

