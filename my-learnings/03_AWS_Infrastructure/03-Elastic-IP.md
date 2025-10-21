# Connect and Manage EC2 Instance – Continued (Elastic IPs)

##  Topics Covered
- Understanding Public vs Private IPs in AWS  
- Elastic IP (Static IP) concept  
- Allocating and Associating Elastic IP  
- Behavior during EC2 stop/start  
- AWS billing logic for Elastic IP  
- Safe practice before stopping an instance  

---

## 1. Public vs Private IP
- **Public IP** is assigned automatically when an EC2 instance is launched.
- It **changes every time** the instance is stopped and started again.
- **Private IP** remains **permanent** within the same VPC (internal communication only).

---

## 2. Elastic IP (EIP)
- **Elastic IP** is a **static public IPv4 address** that you can allocate and attach to any EC2 instance.
- It allows you to **retain a consistent public IP** even after stopping or restarting your instance.
- When an EIP is attached to an EC2 instance, it replaces the temporary public IP.

---

## 3. Correct Workflow for Cloud Engineers
1. Launch an EC2 instance.  
2. Allocate an Elastic IP from **EC2 → Elastic IPs → Allocate Elastic IP**.  
3. Associate the Elastic IP to the **running** EC2 instance.  
4. Connect to the instance using the new Elastic IP instead of the old public IP.  
5. When you plan to stop the instance:  
   - Disassociate the EIP.  
   - (Optional) Release it if you don’t need it again.  
   - Then stop the EC2 instance.

---

## 4. AWS Billing Logic
| Condition | Charge | Explanation |
|------------|--------|--------------|
| EIP attached to **running instance** | ❌ No | Actively in use |
| EIP **not attached** to any instance | ✅ Yes | Unused resource |
| EIP attached to a **stopped instance** | ✅ Yes | Considered idle |

**Example:**  
If you stop your EC2 instance without releasing or disassociating the Elastic IP, AWS will charge you hourly (around $0.005/hr).

---

## 5. Real-World Note
Cloud engineers usually **test** with the default public IP first to confirm the app runs correctly.  
After confirming, they **allocate and attach an Elastic IP** for production stability.  
Before stopping instances, they always **disassociate** EIPs to avoid unwanted billing.

---

##  Quick Recap
- Elastic IP = Permanent Public IP  
- Public IP = Temporary, changes on reboot  
- Always disassociate/release EIP before stopping instances  
- AWS charges for idle or unattached Elastic IPs  

---




