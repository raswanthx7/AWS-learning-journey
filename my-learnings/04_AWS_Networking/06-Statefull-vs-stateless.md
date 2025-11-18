# Stateful vs Stateless – Security Groups & NACLs in AWS

##  Topics Covered
- Core concept: Stateful vs Stateless
- Security Groups (SG) – Stateful
- Network ACLs (NACL) – Stateless
- Quick reference for other components

---

## 1. Core Concept

- **Stateful vs Stateless** applies **only to components that filter traffic based on rules**.  
- The main AWS components using this concept:
  - **Security Groups (SG)** → Stateful  
  - **Network ACLs (NACL)** → Stateless  

Other components like **Router Table, Load Balancer, EC2 Instance, Internet Gateway, NAT Gateway** are part of traffic flow but **do not have stateful/stateless behavior**.

---

## 2. Security Groups – Stateful

- Remember inbound connections and automatically allow return traffic.  
- Instance-level firewall (EC2, Load Balancer).  
- Only one side of the rule (inbound or outbound) is needed for response traffic.

---

## 3. Network ACLs – Stateless

- Do **not** remember connections.  
- Subnet-level firewall.  
- Must explicitly allow **both inbound and outbound** for response traffic.  

---

## 4. Quick Reference – Traffic Flow Components

| Component | Stateful/Stateless? |
|-----------|-------------------|
| Security Group (SG) | Stateful |
| Network ACL (NACL) | Stateless |
| Router Table | N/A |
| Load Balancer | N/A |
| EC2 Instance | N/A |
| Internet Gateway / NAT Gateway | N/A |

---



