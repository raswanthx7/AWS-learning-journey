# AWS Architecture Layers

##  Topics Covered
- What is a Layer in Cloud Computing  
- Why Layers Exist in AWS  
- AWS Layered Architecture (Physical → Virtualization → Infrastructure → Platform → Application → Management/Security)  
- Who Manages Each Layer (AWS vs Customer)  
- Real-World Scenario (EC2 + VPC + ALB)  

---

##  What is a Layer in Cloud Computing?

A **layer** is a logical division of resources and services that separates responsibilities.  
Each layer provides specific functionality and hides the complexity of layers below it.  

> Layers make cloud computing easier to manage, scale, and secure.

---

##  Why Layers Exist in AWS

- **Simplifies complexity:** Customers don’t manage hardware or hypervisors.  
- **Isolation & security:** Problems in one layer do not affect other layers.  
- **Scalability:** Each layer can scale independently.  
- **Modularity:** Troubleshooting is faster because each layer has a clear responsibility.

---

##  AWS Layered Architecture

| Layer | Description | Who Manages It | Key AWS Examples | Customer Interaction |
|-------|-------------|----------------|-----------------|-------------------|
| **Physical Layer** | Actual servers, storage, network equipment, and data centers | AWS | Regions, Availability Zones, Edge Locations | None |
| **Virtualization Layer** | Software layer dividing physical servers into VMs | AWS | Nitro Hypervisor, EC2 host servers, container hosts | None (abstracted) |
| **Infrastructure Layer** | Core resources you configure directly | Customer | EC2 instances, EBS volumes, VPCs, Subnets | Full control (setup/configuration) |
| **Platform Layer** | Managed runtime services and environments | AWS + Customer | RDS, ECS, EKS, Lambda, Elastic Beanstalk | Deploy/manage applications, less control over underlying OS |
| **Application Layer** | Fully managed end-user services | AWS | WorkSpaces, S3 static websites, Connect | Use and configure, minimal backend management |
| **Management & Security Layer** | Monitoring, auditing, access control, and security | Customer + AWS | IAM, CloudWatch, CloudTrail, Config | Configure policies, monitor, secure, audit resources |

---

##  Real-World Scenario: Web Application Deployment

Imagine deploying a web app with EC2, VPC, and ALB:

| Component | AWS Layer | Who Manages | Role |
|-----------|-----------|------------|------|
| EC2 Instance | Infrastructure | Customer | Runs the web app code |
| EBS Volume | Infrastructure | Customer | Persistent storage |
| VPC/Subnets | Infrastructure | Customer | Network isolation and routing |
| ALB (Application Load Balancer) | Application | AWS | Routes HTTP/HTTPS traffic |
| IAM Roles & Security Groups | Management/Security | Customer | Control access and permissions |
| Hypervisor/Nitro | Virtualization | AWS | Provides isolated virtual machines |
| Physical Hardware | Physical | AWS | Servers, networking, storage |

> Each component is part of a layer, and each layer has a **clear owner** — this distinction is critical for cloud architecture and troubleshooting.

---

##  Summary

- AWS architecture is **layered**, separating responsibilities from physical hardware to customer-facing applications.  
- Understanding **who manages what** helps in design, deployment, and troubleshooting.  
- Layers ensure **scalability, isolation, modularity, and security**.  

