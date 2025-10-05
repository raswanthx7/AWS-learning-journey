#  AWS Global Infrastructure

##  Topics Covered
1. AWS Regions  
2. Availability Zones (AZs)  
3. Edge Locations  
4. Latency & High Availability  
5. Key AWS Regions and Availability Zones List  

---

##  AWS Regions

**Definition:**  
An AWS Region is a **physical location** around the world where AWS clusters data centers. Each region is isolated from others for fault tolerance and high availability.

**Example:**  
Mumbai (`ap-south-1`) is a region in India.

---

##  Availability Zones (AZs)

**Definition:**  
Availability Zones are **separate, isolated data centers within a region**. They are connected via low-latency networks, allowing applications to be highly available and fault-tolerant.

**Example:**  
`ap-south-1a`, `ap-south-1b`, `ap-south-1c` are AZs in the Mumbai region.

---

##  Edge Locations

**Definition:**  
Edge Locations are **AWS data centers used to deliver content closer to users**, primarily for services like Amazon CloudFront (CDN). They reduce latency and improve user experience globally.

**CDN** is a network of servers spread across the world that deliver content (like images, videos, files) to users faster.Instead of every user fetching data from the main server, the nearest edge server gives the content.

**CloudFront** is AWS’s Content Delivery Network (CDN) service. It delivers your website, videos, APIs, or other content fast to users globally by using edge locations close to them.It caches content at edge locations, so users don’t always hit the main server.

**Example:**  
New Delhi, Mumbai, Sydney, Tokyo have edge locations for CloudFront.

---

##  Latency & High Availability

- **Latency:** Time taken for data to travel from user to AWS resources. Edge locations help reduce latency.  
- **High Availability:** Deploying applications across multiple AZs ensures services remain up even if one AZ fails.  

---

##  Key AWS Regions & Availability Zones (Professional Reference)

### **Asia Pacific (APAC)**
| Region | Code | Availability Zones |
|--------|------|------------------|
| Mumbai, India | ap-south-1 | 3 AZs (`a`, `b`, `c`) |
| Hyderabad, India | ap-south-2 | 3 AZs |
| Singapore | ap-southeast-1 | 3 AZs |
| Sydney, Australia | ap-southeast-2 | 3 AZs |
| Jakarta, Indonesia | ap-southeast-3 | 3 AZs |
| Tokyo, Japan | ap-northeast-1 | 4 AZs |
| Seoul, South Korea | ap-northeast-2 | 3 AZs |
| Osaka, Japan | ap-northeast-3 | 3 AZs |

### **USA (North America)**
| Region | Code | Availability Zones |
|--------|------|------------------|
| Northern Virginia | us-east-1 | 6 AZs |
| Ohio | us-east-2 | 3 AZs |
| Oregon | us-west-2 | 4 AZs |
| Northern California | us-west-1 | 3 AZs |
| Canada Central | ca-central-1 | 3 AZs |

### **Europe**
| Region | Code | Availability Zones |
|--------|------|------------------|
| Ireland | eu-west-1 | 3 AZs |
| London, UK | eu-west-2 | 3 AZs |
| Paris, France | eu-west-3 | 3 AZs |
| Frankfurt, Germany | eu-central-1 | 3 AZs |
| Milan, Italy | eu-south-1 | 3 AZs |
| Stockholm, Sweden | eu-north-1 | 3 AZs |

### **South America**
| Region | Code | Availability Zones |
|--------|------|------------------|
| São Paulo, Brazil | sa-east-1 | 3 AZs |

### **Africa**
| Region | Code | Availability Zones |
|--------|------|------------------|
| Cape Town, South Africa | af-south-1 | 3 AZs |

### **Middle East**
| Region | Code | Availability Zones |
|--------|------|------------------|
| Bahrain | me-south-1 | 3 AZs |

---

 *End of AWS Global Infrastructure Reference*

