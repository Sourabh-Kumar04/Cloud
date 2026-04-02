# Practical 01 — Introduction to Cloud Platforms (Hands-On Laboratory)

---

## 📌 Objective  
Develop a strong practical and conceptual understanding of modern cloud platforms by creating free-tier accounts on **AWS, Azure, and GCP**, exploring their interfaces, identifying core services, and analyzing pricing mechanisms.

---

## 🧠 Conceptual Background (Know-How)

### What is Cloud Computing?

Cloud computing is the **on-demand delivery of computing resources** — including compute power, storage, networking, and databases — over the internet with a **pay-as-you-go pricing model**.

> Instead of owning physical infrastructure, users provision **virtualized, scalable resources** from global data centers.

---

### The Three Major Cloud Providers

| Feature | AWS | Azure | GCP |
|--------|-----|------|-----|
| Full Name | Amazon Web Services | Microsoft Azure | Google Cloud Platform |
| Founded | 2006 | 2010 | 2011 |
| Market Share | ~31% | ~25% | ~11% |
| Compute | EC2 | Virtual Machines | Compute Engine |
| Storage | S3 | Blob Storage | Cloud Storage |
| Serverless | Lambda | Azure Functions | Cloud Functions |

---

### Cloud Service Models

┌─────────────────────────────────────────────┐ │              SaaS (Software)                │  ← Fully managed applications ├─────────────────────────────────────────────┤ │              PaaS (Platform)                │  ← Managed runtime environments ├─────────────────────────────────────────────┤ │         IaaS (Infrastructure)               │  ← Virtual machines, storage, networking └─────────────────────────────────────────────┘ ↑ Less control        ↑ More control ↑ More abstraction    ↑ More responsibility

---

### Cloud Deployment Models

- **Public Cloud** → Shared infrastructure managed by provider  
- **Private Cloud** → Dedicated infrastructure for a single organization  
- **Hybrid Cloud** → Combination of public + private  
- **Multi-Cloud** → Use of multiple cloud providers  

---

## 🛠️ Practical Execution (Verified Hands-On)

---

## 🔹 Step A — Free-Tier Account Setup

### AWS Free Tier

- Account created via AWS Free Tier portal  
- Accessed **AWS Management Console**  
- Selected **Basic Support Plan**

**Key Observations:**
- Region selection impacts service availability and pricing  
- Billing dashboard available for monitoring usage  
- Free-tier limits clearly indicated  

---

### Azure Free Tier

- Account created via Azure Free portal  
- Received **$200 credit (30 days)**  
- Accessed **Azure Portal**

**Key Observations:**
- Resource-based architecture (resource groups)  
- Subscription and billing structure well-defined  
- Integrated cost tracking tools  

---

### GCP Free Tier

- Account created via Google Cloud Free Tier  
- Received **$300 credit (90 days)**  
- Accessed **GCP Console**

**Key Observations:**
- Project-based resource organization  
- Minimalistic and intuitive UI  
- Strong integration with analytics and AI services  

---

## 🔹 Step B — Dashboard Exploration & Core Services

---

### 🟠 AWS Management Console

**Navigation:**

Console → Services → Search / Categories

**Services Explored:**

| Category | Service | Insight |
|----------|--------|--------|
| Compute | EC2 | Instance types, scaling options |
| Storage | S3 | Bucket structure, access control |
| Networking | VPC | Default network architecture |
| Security | IAM | Users, roles, policies |
| Monitoring | CloudWatch | Metrics and logs |

---

### 🔵 Azure Portal

**Navigation:**

portal.azure.com → All Services → Search

**Services Explored:**

| Category | Service | Insight |
|----------|--------|--------|
| Compute | Virtual Machines | Guided VM deployment |
| Storage | Blob Storage | Container-based storage |
| Networking | Virtual Network | IP/subnet management |
| Security | Azure AD | Identity and access control |
| Monitoring | Azure Monitor | Centralized observability |

---

### 🟢 GCP Console

**Navigation:**

console.cloud.google.com → ☰ Menu → Services

**Services Explored:**

| Category | Service | Insight |
|----------|--------|--------|
| Compute | Compute Engine | Predefined machine types |
| Storage | Cloud Storage | Bucket creation and access |
| Networking | VPC Network | Auto-configured networks |
| Database | Cloud SQL | Managed relational DB |
| Monitoring | Cloud Monitoring | Metrics and alerting |

---

## 🧪 Hands-On Validation Checklist

- [x] Accessed compute services across all platforms  
- [x] Reviewed VM configurations (CPU, RAM, OS, region)  
- [x] Created and explored storage buckets  
- [x] Investigated IAM / identity systems  
- [x] Examined default networking setups  
- [x] Accessed billing and usage dashboards  

---

## 🔹 Step C — Pricing Calculator Analysis

---

### AWS Pricing Calculator

- Configured:
  - EC2 instance (t2.micro equivalent)
  - Basic storage

**Estimated Cost:**

Compute:     ~$8–9/month Storage:     ~$0.80/month Total:       ~$9–10/month

---

### Azure Pricing Calculator

**Observations:**
- Region-based cost variation  
- Enterprise-focused pricing structure  
- Separate dev/test pricing tiers  

---

### GCP Pricing Calculator

**Observations:**
- Transparent cost breakdown  
- Automatic discount application  
- Simplified estimation process  

---

## 💰 Cost Optimization Concepts

| Strategy | Description |
|----------|------------|
| On-Demand | Pay only for usage |
| Reserved | Long-term commitment, lower cost |
| Spot/Preemptible | Cheapest, but interruptible |
| Savings Plans | Flexible pricing commitments |

---

## 📊 Comparative Overview

AWS              AZURE              GCP
    ┌──────┐         ┌──────┐          ┌──────┐

Free    │$0    │         │$200  │          │$300  │ Credit  │      │         │30days│          │90days│ └──────┘         └──────┘          └──────┘

### Platform Strengths

- **AWS** → Extensive service ecosystem, industry standard  
- **Azure** → Strong enterprise and Microsoft integration  
- **GCP** → Clean UI, strong in data and AI workloads  

---

## 🔐 Security Best Practices (Adopted)

- Enabled **Multi-Factor Authentication (MFA)**  
- Avoided use of root account for daily operations  
- Explored IAM roles and access policies  
- Reviewed billing alerts and cost monitoring tools  

---

## ✅ Learning Outcomes

After completing this practical:

- Able to navigate AWS, Azure, and GCP interfaces confidently  
- Understand mapping of equivalent services across platforms  
- Capable of estimating infrastructure cost using calculators  
- Developed foundational understanding of cloud architecture  
- Gained practical exposure to real-world cloud environments  

---

## 🧾 Final Reflection

This practical establishes a foundational understanding of cloud platforms as **distributed, scalable computing systems** rather than isolated tools.

> Cloud platforms function as **programmable infrastructure layers**, enabling rapid deployment, scalability, and cost-efficient system design.

Mastery of these platforms requires not only usage, but also:
- Architectural thinking  
- Cost awareness  
- Security discipline  

---
