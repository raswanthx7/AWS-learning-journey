# VPC Routing Deep Dive 

## Topics Covered
- What is the VPC Router
- Why AWS Manages the Router
- What is a Route Table
- How Route Tables Decide Traffic Flow
- Route Table Association with Subnets
- Main Route Table vs Custom Route Table
- Internet Gateway + Routing Logic
- Full End-to-End Scenario: User → IGW → Router → Subnet → EC2 → Response Back
- Return Path & Stateful Routing

---

## 1. What Is the VPC Router
AWS provides **one logical router per VPC**. You do not configure or see this router.
It is responsible for moving traffic between:
- Subnets
- Internet Gateway
- NAT Gateway
- Load Balancer nodes
- VPC Peering
- Endpoints
- Transit Gateway

You never touch the router directly. You only influence it using route tables.

---

## 2. Why AWS Manages the Router
AWS controls the router because routing must be:
- Highly available
- Scalable
- Secure
- Consistent across all AZs

User-managed routers would break VPC isolation and increase failure points.

---

## 3. What Is a Route Table
A route table is the **instruction sheet** for the VPC router.
It contains rules like:

Destination → Target → Meaning
- 10.0.0.0/16 → local → traffic stays inside VPC
- 0.0.0.0/0 → igw-xxxx → send internet traffic to IGW
- 0.0.0.0/0 → nat-xxxx → send internet traffic from private subnet to NAT

The route table **does not forward packets**. It only tells the VPC router what to do.

---

## 4. Route Tables Are Attached to Subnets
Each subnet must be associated with **exactly one** route table.

Examples:
- Public subnet → route table with IGW
- Private subnet → route table with NAT or no external route

Association means:
"Router, use **this** rule set for traffic leaving **this** subnet."

---

## 5. Main Route Table vs Custom Route Table
- Main route table: default for the VPC
- Custom route tables: used for public, private, or special subnets

A subnet uses the main route table **only if you don’t manually associate it**.

---

## 6. Internet Gateway + Routing
To reach the internet, three conditions must be true:
1. Subnet route table has: `0.0.0.0/0 → igw-xxxx`
2. Instance has a public IP or attached EIP
3. Security group + NACL allow outbound traffic

If any one is missing → no internet.

---

## 7. Full End-to-End Scenario (Detailed)
"User accessing a website hosted inside an EC2 instance in a Public Subnet"

### End-to-End Traffic Flow (User → EC2 → User)

1. User enters the domain name (or load balancer DNS).
2. DNS resolves to the public endpoint (Load Balancer DNS → public IPs, or directly an EC2 public IP if no LB).
3. The request travels across the internet and reaches the VPC edge (Internet Gateway).
4. The packet enters the VPC. The AWS-managed VPC router evaluates forwarding based on the route table associated with the destination subnet.
5. The router finds a matching route for the destination (the VPC contains an automatic `local` route such as `10.0.0.0/16 → local`).
6. The router forwards the packet to the destination ENI (EC2 private IP) inside the correct subnet.
7. Subnet-level Network ACLs and the instance's Security Group are evaluated; if allowed, the EC2 instance receives the request.
8. The EC2 instance processes the request and generates a response.
9. For the response, the instance sends packets to its default gateway (the VPC router). The router consults the route table associated with the instance's subnet.
10. If the destination is outside the VPC, the router matches the default route (for example `0.0.0.0/0 → igw-xxxx`) and forwards the packet to the Internet Gateway.
11. The Internet Gateway performs any required public/private mapping (1:1 NAT for EC2 public IPs) and sends the packet back over the internet to the client.
12. The client receives the response and the TCP/HTTP session is complete.

Notes:
-Every AWS Load Balancer automatically gets a DNS name from AWS.
- User types:
http://your-domain.com

DNS CNAME resolves to:
- Load Balancer’s DNS name
The LB DNS then resolves to multiple public IPs (Elastic LB IP ranges).

So when I said:

> User enters the domain name (or load balancer DNS)

I meant:

- The “Load balancer DNS” = the public domain name Amazon gives your LB.
- DNS → LB DNS → LB public IPs → EC2 private IPs
- Security Groups are stateful: they automatically allow return traffic for an established outbound connection. Network ACLs are stateless and require explicit allow entries for both directions.

---

## 8. Return Path (Stateful Routing)
The VPC router remembers incoming connections.
It automatically allows return traffic even if the route table is not symmetric.

This is why internet responses reach back correctly.

---
