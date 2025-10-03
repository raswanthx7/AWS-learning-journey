# Module 1 – Introduction to cloud

## Topics Covered
- Describe the client-server model at fundamental level
- What is cloud computing
- Cloud Deployment Types
- Types of cloud computing
- Key Benefits of Cloud Computing
- AWS Regions and Availability Zones
- Customer vs AWS Responsibilities
- High Availability and Fault Tolerance

---

## 1. Describe the client-server model at fundamental level

The client-server model is a network architecture where the client requests services or resources and the server provides them. Examples include web browsers requesting web pages from web servers.

---

## 2. What is cloud computing

- **Cloud computing is the on-demand delivery of IT resources over the Internet with pay-as-you-go pricing.**
- Instead of buying, owning, and maintaining physical data centers and servers, you can access technology services, such as computing power, storage, and databases, on an as-needed basis from a cloud provider like Amazon Web Services (AWS).

---

## 3. Cloud Deployment Types

**There are three types: Cloud-Based (fully in the cloud), On-Premises (runs on local servers), and Hybrid (combination of cloud and on-premises).**

1. Cloud-Based Deployment

- Applications and services run entirely on a cloud provider (e.g., AWS).
- Accessible over the internet.
- No need to manage physical servers.

2. On-Premises Deployment

- Applications run on your organization’s own physical servers.
- You manage everything: hardware, software, and network.

3. Hybrid Deployment

- Combination of cloud and on-premises.
- Lets you move workloads between cloud and local servers for flexibility and scalability.

---

## 4. Types of cloud computing

There are three main types of cloud computing:

1. **IaaS – Infrastructure as a Service:** Provides virtual hardware like servers, storage, and networking. You manage the operating system and applications, while the provider manages the infrastructure.

- Example: AWS EC2, Azure Virtual Machines.

2. **PaaS – Platform as a Service:** Provides a platform to develop, run, and manage applications without worrying about the underlying infrastructure.

- Example: AWS Elastic Beanstalk, Google App Engine.

3. **SaaS – Software as a Service:** Provides fully functional software over the internet. You just use it; the provider manages everything.

- Example: Gmail, Office 365, Salesforce.

---

## 5. Key Benefits of Cloud Computing

- **The main benefits are `cost efficiency`, `scalability`, `elasticity`, `global reach`, `security`, and `agility`. Cloud computing lets organizations reduce costs, scale resources as needed, and deploy applications faster with strong security.**

**Key Benefits of Cloud Computing**

1. Cost Efficiency
- Reduces upfront hardware and infrastructure costs.
- Pay only for what you use.

2. Scalability
- Easily scale resources up or down based on demand.

3. Elasticity
- Automatically adjusts resources to handle varying workloads.

4. Global Reach
- Deploy applications and services in multiple regions worldwide.

5. Security
- Built-in security features and compliance standards provided by cloud providers.

6. Agility & Innovation
- Faster deployment of applications, enabling rapid testing and innovation.

---

## 6. AWS Regions and Availability Zones

- **An AWS Region is a geographical area with multiple data centers, and an Availability Zone (AZ) is a separate data center within a region. Using multiple AZs ensures high availability and fault tolerance.**
- As of September 2025, Amazon Web Services has 36 geographic regions and 114 Availability Zones (AZs), with plans for expansion in the future.  

1. AWS Region

- A geographical area where AWS has multiple data centers.
- Example: US East (N. Virginia), Asia Pacific (Mumbai).
- Purpose: Allows deploying resources close to your users for low latency and disaster recovery.

2. Availability Zone (AZ)

- A physically separate data center within a region.
- Each region has multiple AZs.
- Purpose: Ensures high availability and fault tolerance by running applications across multiple AZs.

### high availability and fault tolerance?

**High Availability ensures minimal downtime by distributing resources across multiple AZs, while Fault Tolerance ensures the system continues running without interruption even if some components fail.**

---

## 7. Customer vs AWS Responsibilities

- **AWS is responsible for the security of the cloud, including infrastructure and hardware. The customer is responsible for security in the cloud, such as data, applications, and access management. This is called the Shared Responsibility Model.**

1. AWS Responsibilities (**Security of the Cloud**)

- AWS manages the physical infrastructure: servers, storage, networking, and facilities.
- Ensures hardware, software, and networking security.

2. Customer Responsibilities (**Security in the Cloud**)

- Customers manage their data, applications, identity & access management (IAM), and OS configurations.
- Responsible for securing what they deploy in the cloud.

---

## 8. High Availability and Fault Tolerance

**High Availability ensures minimal downtime by distributing resources across multiple AZs, while Fault Tolerance ensures the system continues running without interruption even if some components fail.**

1. High Availability (HA)

- Ensures that applications and services are always accessible even during failures.
- Achieved by running resources across multiple Availability Zones (AZs).
- Goal: Minimize downtime.

2. Fault Tolerance (FT)

- Ensures the system continues to operate even if components fail.
- Achieved through redundancy and automatic failover.
- Goal: Zero downtime for critical systems.

---
