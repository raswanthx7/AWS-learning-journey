#  Cloud Models – IaaS, PaaS, SaaS

##  Topics Covered
- Cloud models
- Infrastructure as a Service (IaaS)
- Platform as a Service (PaaS)
- Software as a Service (SaaS)
- Core Components Explanation
- Responsibility Table (Customer vs Cloud Provider)

---

## Cloud models

- **A cloud model defines what part of the IT stack the customer manages and what the cloud provider manages.** 
- We use cloud models to save costs, scale easily, focus on development, and leave infrastructure management to the cloud provider.

---

##  Infrastructure as a Service (IaaS)

**Definition:**  
Infrastructure as a Service provides virtualized computing resources over the internet such as servers, storage, networking, and virtualization.  
The **customer** manages everything above the infrastructure — like applications, data, OS, and runtime.  

**Example:** AWS EC2, Google Compute Engine, Azure Virtual Machines  

---

##  Platform as a Service (PaaS)

**Definition:**  
Platform as a Service provides a ready-made platform for developing, testing, and deploying applications.  
The **customer** focuses only on the application and data, while the **cloud provider** manages the rest (OS, runtime, servers, storage, networking).  

**Example:** AWS Elastic Beanstalk, Google App Engine, Azure App Services  

---

##  Software as a Service (SaaS)

**Definition:**  
Software as a Service provides complete applications over the internet.  
The **customer** just uses the application, while the **cloud provider** manages everything (infrastructure, platform, and data).  

**Example:** Gmail, Salesforce, Microsoft 365  

---

##  Cloud Model Components Explained

| Component | Description |
|------------|--------------|
| **Applications** | The actual software or tools users interact with (e.g., WhatsApp, Gmail, ERP system). |
| **Data** | The information stored and managed by the application — the customer stores and secures customer-related data. |
| **Runtime** | The environment where the application’s code runs (e.g., Python runtime, Java runtime). |
| **Middleware** | Acts as a bridge between the operating system and applications, enabling smooth communication and data exchange. |
| **Operating System (OS)** | The base software that controls hardware and allows applications to run (e.g., Linux, Windows). |
| **Virtualization** | Technology that creates multiple virtual machines on physical servers, allowing independent environments. |
| **Servers** | Powerful computers that host applications and make them accessible online. |
| **Storage** | Used to store application data, user files, logs, and backups (e.g., AWS S3, EBS). |
| **Networking** | Connects all components together — includes internet connection, firewalls, subnets, and virtual private clouds (VPC). |

---

##  Responsibility Comparison Table

| Component | On-Premise | IaaS | PaaS | SaaS |
|------------|-------------|------|------|------|
| Applications | Customer | Customer | Customer | Cloud Provider |
| Data | Customer | Customer | Customer | Cloud Provider |
| Runtime | Customer | Customer | Cloud Provider | Cloud Provider |
| Middleware | Customer | Customer | Cloud Provider | Cloud Provider |
| Operating System | Customer | Customer | Cloud Provider | Cloud Provider |
| Virtualization | Customer | Cloud Provider | Cloud Provider | Cloud Provider |
| Servers | Customer | Cloud Provider | Cloud Provider | Cloud Provider |
| Storage | Customer | Cloud Provider | Cloud Provider | Cloud Provider |
| Networking | Customer | Cloud Provider | Cloud Provider | Cloud Provider |

---

 **In short:**  
- **IaaS:** Customer manages most components (infrastructure is provided).  
- **PaaS:** Customer manages only the application and data.  
- **SaaS:** Customer simply uses the application; everything else is managed by the provider.

---

