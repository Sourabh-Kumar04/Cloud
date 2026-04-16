# Practical 1: Introduction to Cloud Platforms

**Objective:** Familiarize students with cloud platforms and their interfaces.

---

## Prerequisites
- A valid email address (for account creation)
- A credit/debit card (required for verification; free-tier will not be charged)
- A modern web browser (Chrome or Firefox recommended)

---

## Part A: Create Free-Tier Accounts

### 1. Amazon Web Services (AWS)

1. Open your browser and go to [https://aws.amazon.com/free](https://aws.amazon.com/free)
2. Click **"Create a Free Account"**.
3. Enter your **email address** and choose an **AWS account name**.
4. Click **"Verify email address"** — check your inbox for the OTP and enter it.
5. Set a **root user password** and confirm it.
6. Select **Account Type** → choose **Personal**.
7. Fill in your **contact information** (name, address, phone number).
8. Enter **payment information** (a small $1 authorization charge may appear; it is refunded).
9. Complete **identity verification** via phone (SMS or voice call).
10. Choose the **Basic Support Plan (Free)**.
11. Click **"Complete sign up"**.
12. Sign in at [https://console.aws.amazon.com](https://console.aws.amazon.com) using your email and password.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: AWS Free Tier homepage showing "Create a Free Account" button]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: AWS Management Console home page after successful login]
> ```

---

### 2. Microsoft Azure

1. Go to [https://azure.microsoft.com/free](https://azure.microsoft.com/free)
2. Click **"Start free"**.
3. Sign in with a **Microsoft account** (or create one at [account.microsoft.com](https://account.microsoft.com)).
4. Fill in your **country/region** and **date of birth**.
5. Verify your identity using your **phone number**.
6. Enter **payment details** for verification (you get $200 credits for 30 days + free services for 12 months).
7. Agree to the **subscription agreement** and click **"Sign up"**.
8. After sign-up, go to [https://portal.azure.com](https://portal.azure.com) and log in.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Azure free account sign-up page]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Azure Portal dashboard after successful login]
> ```

---

### 3. Google Cloud Platform (GCP)

1. Visit [https://cloud.google.com/free](https://cloud.google.com/free)
2. Click **"Get started for free"**.
3. Sign in with your **Google account** (or create one).
4. Select your **country** and agree to the **Terms of Service**.
5. Fill in **account type** (Individual), your **name**, and **address**.
6. Enter **payment information** (you receive $300 in free credits for 90 days).
7. Click **"Start my free trial"**.
8. Access the console at [https://console.cloud.google.com](https://console.cloud.google.com).

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: GCP free trial sign-up page]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: GCP Console home page after login]
> ```

---

## Part B: Explore Dashboards and Identify Key Services

### AWS Console Exploration

1. Log in to the **AWS Management Console**.
2. At the top, click the **search bar** and explore the following services:
   - **Compute:** Search for `EC2` → click to open the EC2 Dashboard.
   - **Storage:** Search for `S3` → click to open S3 Buckets page.
   - **Networking:** Search for `VPC` → click to open the VPC Dashboard.
3. Note the **Region selector** (top-right corner) — observe how services are region-specific.
4. Click **"Services"** in the top navigation to see the full list of AWS services organized by category.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: AWS Console showing EC2 Dashboard]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: AWS Console showing S3 service page]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: AWS Console showing VPC Dashboard]
> ```

---

### Azure Portal Exploration

1. Log in to [https://portal.azure.com](https://portal.azure.com).
2. On the left sidebar, explore:
   - **Compute:** Click **"Virtual Machines"**.
   - **Storage:** Click **"Storage Accounts"**.
   - **Networking:** Click **"Virtual Networks"**.
3. Use the **search bar** at the top to search for any service by name.
4. Click **"All Services"** in the left sidebar to browse all available Azure services.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Azure Portal showing Virtual Machines section]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Azure Portal showing All Services page]
> ```

---

### GCP Console Exploration

1. Log in to [https://console.cloud.google.com](https://console.cloud.google.com).
2. Click the **hamburger menu (☰)** on the top-left to open the navigation menu.
3. Explore:
   - **Compute:** Navigate to **Compute Engine → VM Instances**.
   - **Storage:** Navigate to **Cloud Storage → Buckets**.
   - **Networking:** Navigate to **VPC Network**.
4. Use the **search bar** at the top to find any service quickly.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: GCP Console showing Compute Engine section]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: GCP Console navigation menu open with services listed]
> ```

---

## Part C: Understand Pricing Calculators

### AWS Pricing Calculator

1. Go to [https://calculator.aws/pricing/2/home](https://calculator.aws/pricing/2/home)
2. Click **"Create estimate"**.
3. In the search box, type **EC2** and click **"Configure"**.
4. Set the following:
   - **Region:** US East (N. Virginia)
   - **Instance type:** t2.micro
   - **Usage:** 750 hours/month
5. Click **"Add to my estimate"** and observe the monthly cost.
6. Try adding **S3** storage (e.g., 5 GB) and see the cost update.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: AWS Pricing Calculator showing EC2 estimate]
> ```

---

### Azure Pricing Calculator

1. Visit [https://azure.microsoft.com/pricing/calculator](https://azure.microsoft.com/pricing/calculator)
2. Search for **Virtual Machines** and add it to the estimate.
3. Configure:
   - **Region:** East US
   - **Operating System:** Linux
   - **Tier:** Free / B1S (1 vCPU, 1 GB RAM)
4. Scroll down to see the **monthly cost estimate**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Azure Pricing Calculator showing VM estimate]
> ```

---

### GCP Pricing Calculator

1. Open [https://cloud.google.com/products/calculator](https://cloud.google.com/products/calculator)
2. Click **"Add to estimate"** → search for **Compute Engine**.
3. Configure:
   - **Machine type:** e2-micro
   - **Region:** us-central1
   - **Hours:** 730 hours/month
4. Review the estimated monthly cost on the right panel.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: GCP Pricing Calculator showing Compute Engine estimate]
> ```

---

## Summary Table

| Feature | AWS | Azure | GCP |
|---|---|---|---|
| Free Tier Duration | 12 months + always free | 12 months + $200 credits | 90 days + $300 credits |
| VM Service | EC2 | Virtual Machines | Compute Engine |
| Storage Service | S3 | Blob Storage | Cloud Storage |
| Networking | VPC | Virtual Network | VPC Network |
| Pricing Calculator | calculator.aws | azure.microsoft.com/pricing | cloud.google.com/calculator |

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Create free-tier accounts on AWS, Azure, and GCP
- ✅ Navigate the dashboards and locate compute, storage, and networking services
- ✅ Use pricing calculators to estimate cloud costs
