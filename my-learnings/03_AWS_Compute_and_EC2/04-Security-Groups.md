# Topics Covered:
- Security Groups (Stateful)
- Inbound vs Outbound Rules
- Real-world rules: SSH (22), HTTP (80), HTTPS (443), ICMP
- Why Ephemeral Ports Matter
- Compare Security Groups vs NACL
- Hands-on: Lock/Unlock EC2 remotely
- Default Security Group

---

## 1. Security Groups Overview
- Security groups (SG) act as **virtual firewalls** for EC2 instances.  
- SGs are **stateful**:
  - If you allow **outbound traffic**, the return traffic is automatically allowed inbound.  
  - No need to configure inbound ephemeral ports manually (unlike NACLs).

---

## 2. Inbound vs Outbound Rules
- **Inbound rules**: Define which incoming traffic is allowed to the EC2 instance.  
  Example: SSH (22) for admin access, HTTP (80) for web, HTTPS (443) for secure web.  

- **Outbound rules**: Define which outgoing traffic EC2 can initiate.  
  Example: Application on EC2 wants to download updates → needs HTTP/HTTPS outbound.  

---

## 3. Real-World Port Examples
| Protocol | Port | Purpose |
|-----------|------|----------|
| SSH | 22 | Connect to EC2 remotely |
| HTTP | 80 | Host web pages |
| HTTPS | 443 | Secure web traffic |
| ICMP | – | Ping or network diagnostics |

---

## 4. Why Ephemeral Ports Matter
When your EC2 sends an outbound request (e.g. `yum update`), the response returns to a temporary (ephemeral) port on the instance.  
NACL must allow those ephemeral ports for responses to get through.


### Real-World Example: Application Update Scenario
- **Scenario**: EC2 instance has an application. It wants to fetch updates from `update.in`.  

1. **Outbound request**:
   - Application sends HTTP/HTTPS request → port **80/443** on `update.in`.  
   - EC2 chooses a **temporary ephemeral port** (1024–65535) as the source port.  

2. **Inbound response**:
   - `update.in` responds → traffic comes back to the EC2 instance’s ephemeral port.  
   - **NACLs (stateless)** must allow inbound ephemeral ports 1024–65535.  
   - **Security groups (stateful)** automatically allow the response; no extra rule needed.  

---

## 5. Compare Security Groups vs NACL
| Feature              | Security Group (SG)       | NACL                       |
|----------------------|-------------------------|----------------------------|
| Stateful / Stateless  | Stateful                 | Stateless                  |
| Inbound/Outbound      | Configure inbound/outbound separately | Must configure both directions separately |
| Applies To           | EC2 instance level       | Subnet level               |
| Response traffic     | Automatically allowed   | Must allow manually (ephemeral ports) |

---

## 6. Hands-on: Lock and Unlock EC2
- To **lock EC2**: Remove SSH (22) from inbound rules in SG → cannot login.  
- To **unlock EC2**: Re-add SSH (22) inbound rule → regain login access.  
- Practice helps understand the stateful behavior: outbound reply traffic is automatic, inbound must be explicitly allowed.

---

## 7. Default Security Group
Each VPC has one default SG created by AWS.  
- Allows **inbound** traffic only from instances using the same SG.  
- Allows **all outbound** traffic to any destination.  
- Used automatically if no SG is specified during launch.

---

## 8. Summary / Key Takeaways
- SGs are stateful; NACLs are stateless.  
- Outbound ephemeral ports are **required for NACLs** so that return traffic can reach EC2.  
- Real-world SG rules:
  - SSH (22) → Admin access  
  - HTTP (80) → Web traffic  
  - HTTPS (443) → Secure web traffic  
  - ICMP → Ping and network testing  
- Locking/unlocking EC2 using SGs demonstrates practical control.  

---

