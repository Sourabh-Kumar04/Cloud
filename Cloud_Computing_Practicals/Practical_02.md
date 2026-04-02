# 🚀 Practical 02 — Launch Your First Amazon EC2 Instance

---

## 📌 Objective

Deploy a virtual machine on AWS using **Amazon EC2**, configure security groups for secure SSH access, and connect remotely to the instance.

---

## 🧠 Conceptual Background (Know-How)

### 🌐 What is Amazon EC2?

**Amazon Elastic Compute Cloud (EC2)** provides scalable virtual computing capacity in the cloud.

> 💡 Each EC2 instance is a virtual machine — like renting a computer from AWS.

---

## 🖼️ EC2 Architecture Overview

### 🔷 EC2 Ecosystem

![EC2 Architecture](https://miro.medium.com/v2/resize\:fit:1400/1*Z0J7XwJLdnOjFkWDXLI4Yg.png)

---

### 🔷 Security Group (Firewall Flow)

![Security Group](https://docs.aws.amazon.com/images/vpc/latest/userguide/images/security-groups-diagram.png)

---

### 🔷 Instance Lifecycle

![Lifecycle](https://docs.aws.amazon.com/images/AWSEC2/latest/UserGuide/images/instance-state-diagram.png)

---

## 🧩 Core EC2 Concepts

```
AMI → Instance → Security Group
        ↓
       EBS
        ↓
     Key Pair
```

---

## 🔑 Key Terminology

| Term                  | Definition                        |
| --------------------- | --------------------------------- |
| **AMI**               | Pre-configured OS template        |
| **Instance Type**     | Hardware configuration (CPU, RAM) |
| **Security Group**    | Virtual firewall                  |
| **Key Pair**          | SSH authentication mechanism      |
| **EBS**               | Persistent storage                |
| **Elastic IP**        | Static public IP                  |
| **Region**            | AWS data center location          |
| **Availability Zone** | Isolated data centers             |

---

## ⚙️ EC2 Instance Type Naming

```
t3.micro
│  │
│  └── Size
└───── Family + Generation
```

| Family | Purpose               |
| ------ | --------------------- |
| t      | Burstable (Free Tier) |
| m      | General               |
| c      | Compute optimized     |
| r      | Memory optimized      |

---

## 💿 What is an AMI?

An **AMI (Amazon Machine Image)** is a pre-configured OS snapshot.

### Common Choices:

* Amazon Linux 2023 ✅ (Recommended)
* Ubuntu
* Windows Server
* Marketplace AMIs

---

## 🛠️ Step-by-Step Guide

### ✅ Prerequisites

* AWS Account (Free Tier)
* SSH Client (Terminal / PuTTY)
* Basic Linux knowledge

---

## 🚀 Step A — Launch EC2 Instance

1. Go to AWS Console
2. Search **EC2**
3. Click **Launch Instance**

### 🔧 Configuration

**Name:**

```
my-first-ec2
```

**AMI:**

* Amazon Linux 2023
* 64-bit (x86)

**Instance Type:**

```
t3.micro (Free Tier)
```

**Key Pair:**

* Name: `my-ec2-keypair`
* Format: `.pem` / `.ppk`

> ⚠️ Download once — cannot be recovered

**Network Settings:**

* Auto-assign Public IP → ✅ Enabled
* Security Group:

  * Allow SSH from **My IP**

**Storage:**

```
8 GiB (gp3)
```

Click **Launch Instance**

---

## 🔐 Step B — Security Group Configuration

### SSH Rule

```
Type: SSH
Port: 22
Source: My IP
```

> ❌ Never use `0.0.0.0/0` (security risk)

---

## 🔌 Step C — Connect Using SSH

### 1️⃣ Get Public IP

```
EC2 → Instances → Public IPv4
```

---

### 2️⃣ Set Permissions

```bash
chmod 400 my-ec2-keypair.pem
```

---

### 3️⃣ Connect

```bash
ssh -i my-ec2-keypair.pem ec2-user@<PUBLIC-IP>
```

---

### 👤 Default Users

| AMI          | Username |
| ------------ | -------- |
| Amazon Linux | ec2-user |
| Ubuntu       | ubuntu   |
| Debian       | admin    |

---

### 4️⃣ First-Time Login

Type:

```
yes
```

---

### 5️⃣ Connected 🎉

---

## 🧪 Basic Commands

```bash
# System info
whoami
uname -a
cat /etc/os-release

# Resources
free -h
df -h

# Network
ip addr
curl ifconfig.me

# Update system
sudo dnf update -y

# Install Apache
sudo dnf install httpd -y
sudo systemctl start httpd
```

---

## 📊 EC2 Instance States

```
Pending → Running → Stopped → Terminated
```

### 💡 Stop vs Terminate

* **Stop** → Can restart, not billed for compute
* **Terminate** → Permanent deletion

---

## 💰 Cost Awareness

```
Free Tier: 750 hours/month
```

✔ Run 1 instance full-time
❌ Multiple instances share usage

### 💡 Tips:

* Stop unused instances
* Delete unused volumes
* Monitor billing

---

## 🔐 Security Best Practices

1. Restrict SSH to **My IP**
2. Avoid exposing port 22 publicly
3. Use IAM roles (not access keys)
4. Enable AWS CloudTrail
5. Use **SSM Session Manager** instead of SSH (advanced)

---

## ✅ Learning Outcomes

* [ ] Launch EC2 instance
* [ ] Configure security groups
* [ ] Connect using SSH
* [ ] Understand EC2 architecture
* [ ] Manage cost and security

---

## 📚 Further Reading

* AWS EC2 Documentation
* EC2 Instance Types
* AMI Guide
* Security Groups

---
