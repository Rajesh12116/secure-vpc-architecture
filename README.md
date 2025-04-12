# 🔐 Secure VPC Network with Public & Private Subnets and Bastion Host

## 🎯 Goal
Build a secure architecture where only public-facing resources are exposed to the internet, and sensitive/internal servers (like databases or private EC2) are isolated in a private subnet.  
Access private servers **indirectly** via a **bastion host (jump box)**.

---

## 🔹 1. VPC (Virtual Private Cloud)
**What is it?**
- A VPC is your own isolated private network in AWS.
- You define the IP address range, create subnets, and control routing, firewalls, and internet access.

**Why it's important:**  
Just like an office network is private and secure, a VPC lets you isolate and protect your infrastructure in the cloud.

---

## 🔹 2. Subnets

### ✅ Public Subnet:
- Connected to the Internet Gateway.
- Instances launched here can have public IPs and talk to the internet.

### ✅ Private Subnet:
- No direct internet access.
- Used for sensitive resources like databases, backend servers, APIs.

**Why this design?**
- Keep web servers publicly accessible.
- Keep data & logic (backend) private and secure.

---

## 🔹 3. Internet Gateway (IGW)

**What is it?**
- A component that allows your VPC to communicate with the internet.

**Why needed?**
- Only subnets with a route to the IGW can reach the internet.
- Public EC2 needs this to be reachable from your laptop.

---

## 🔹 4. Route Table Association

You did two things here:

1. **Public subnet** → route to Internet Gateway (`0.0.0.0/0 → IGW`)  
2. **Private subnet** → no internet route (stays isolated)

**Why this matters:**  
It’s like assigning network rules to each room in a building:
- Room A (public) has internet access.
- Room B (private) is isolated unless explicitly allowed.

---

## 🔹 5. EC2 Instances

### ✅ Public EC2 (Bastion Host)
- Has a public IP.
- Deployed in public subnet.
- You connect via SSH (`port 22`) from your laptop.

### ✅ Private EC2
- No public IP.
- Deployed in private subnet.
- Cannot be accessed directly from internet.
- Only accessible from within the VPC.

**Why this is real-world design:**
- You don’t want private servers exposed to hackers.
- You limit access using a secure "gateway" (the bastion host).

---

## 🔹 6. Security Groups (Firewalls)

You configured Security Groups like this:

### 🔐 Public EC2 Security Group:
- Allow SSH (`port 22`) from your laptop’s IP.

### 🔐 Private EC2 Security Group:
- Allow SSH only from the **public EC2’s private IP**.

**Why important:**
- Only the public EC2 is open to the world.
- Private EC2 is hidden and protected.

---
### In Git Bash
## 🔹 7. SSH into Public EC2


ssh -i your-key.pem ec2-user@<public-ec2-public-ip>
scp -i your-key.pem private-ec2.pem ec2-user@<bastion-public-ip>:~
