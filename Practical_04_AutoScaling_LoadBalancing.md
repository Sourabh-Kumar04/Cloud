# Practical 4: Configure Auto Scaling and Load Balancing

**Objective:** Set up an Auto Scaling Group and Application Load Balancer on AWS, configure scaling policies, and test the setup by simulating high traffic.

---

## Prerequisites
- An active AWS account
- A VPC with at least two public subnets in different Availability Zones
- Basic familiarity with EC2 instances (refer to Practical 2 and 3)

---

## Background Concepts

| Term | Description |
|---|---|
| Launch Template | Blueprint that defines the EC2 configuration for new instances |
| Auto Scaling Group (ASG) | Manages a fleet of EC2 instances, adding or removing them based on demand |
| Scaling Policy | Rules that define when and how many instances to add/remove |
| Application Load Balancer (ALB) | Distributes incoming HTTP/HTTPS traffic across multiple EC2 instances |
| Target Group | A group of EC2 instances that the ALB routes traffic to |

---

## Step 1: Create a Launch Template

A **Launch Template** defines what kind of EC2 instance will be created during auto-scaling events.

1. Log in to the **AWS Management Console** and navigate to **EC2**.
2. In the left panel, click **"Launch Templates"** under **Instances**.
3. Click **"Create launch template"**.
4. Fill in:
   - **Launch template name:** `MyLaunchTemplate`
   - **Description:** `Template for auto scaling web servers`
   - **Auto Scaling guidance:** ✅ Check the box
5. Under **Application and OS Images (AMI)**:
   - Click **"Quick Start"** → select **Amazon Linux 2 AMI** (free tier eligible)
6. Under **Instance type:** select `t2.micro`
7. Under **Key pair:** select your existing key pair (e.g., `my-ec2-keypair`)
8. Under **Network settings → Firewall (Security groups):**
   - Click **"Create security group"**
   - Name: `WebServerSG`
   - Add **Inbound rules:**
     - SSH (TCP port 22) — Source: My IP
     - HTTP (TCP port 80) — Source: Anywhere (0.0.0.0/0)
9. Under **Advanced details → User data**, paste the following script to auto-install and start an Apache web server:

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from $(hostname -f)</h1>" > /var/www/html/index.html
```

10. Click **"Create launch template"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/6dc981a4-393d-4818-b7df-15be1a8772ad" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/e141c9aa-8de6-4470-98e1-7c9d5b86c521" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/e7130730-dd25-4515-a8f5-e7af37511d7a" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/aa052493-acf2-4372-a517-c68557de178b" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/2aaee123-787b-4dcd-af1c-2fa5b258c746" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/9c1a2779-2ea7-47ec-b486-742215d4175c" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/a36bf194-bcf5-4a83-8193-18b882dd1fe9" />
> 

---

## Step 2: Create a Target Group

Before creating the Load Balancer, you need a **Target Group** — a logical group of instances that receives traffic.

1. In the EC2 left panel, scroll down to **"Load Balancing"** → click **"Target Groups"**.
2. Click **"Create target group"**.
3. Fill in:
   - **Target type:** Instances
   - **Target group name:** `MyTargetGroup`
   - **Protocol:** HTTP
   - **Port:** 80
   - **VPC:** Select your VPC (e.g., `MyCustomVPC` from Practical 3, or default VPC)
4. Under **"Health checks"**:
   - **Protocol:** HTTP
   - **Path:** `/`
5. Click **"Next"** → skip registering targets for now (ASG will do this automatically).
6. Click **"Create target group"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/9690e826-428d-4006-bd41-1dfa463b0a48" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/220dae41-f548-4008-8909-914392f2aaa3" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/530c59a8-7c50-4cb8-9302-2e459ce93903" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/595d6e99-91e0-4039-a68a-beffeeb35857" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/35493a02-f4a6-4a4d-89c9-c566faef8d2b" />

---

## Step 3: Create an Application Load Balancer (ALB)

1. In the EC2 left panel, click **"Load Balancers"** → **"Create load balancer"**.
2. Under **"Application Load Balancer"**, click **"Create"**.
3. Fill in:
   - **Load balancer name:** `MyAppLoadBalancer`
   - **Scheme:** Internet-facing
   - **IP address type:** IPv4
4. Under **"Network mapping"**:
   - **VPC:** Select your VPC
   - **Mappings:** Select **at least 2 Availability Zones** and their corresponding **public subnets**
5. Under **"Security groups":**
   - Create or select a security group that allows **HTTP (port 80) from 0.0.0.0/0**
6. Under **"Listeners and routing"**:
   - **Protocol:** HTTP, **Port:** 80
   - **Default action:** Forward to `MyTargetGroup`
7. Click **"Create load balancer"**.
8. Wait for the state to change from **"Provisioning"** to **"Active"** (1–3 minutes).

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/c2ca911a-2ece-4f54-b772-c9b9969a600a" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/43b38913-277f-4b66-98f0-72a3245bf55a" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/fae4f961-2f8e-4594-8b32-b59e6a86cb9e" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/15f9f3d4-3199-49ef-9d2d-e06b08a4cf7b" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/d9e47b6c-ffdf-4e30-bb38-daeabafdeacd" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/2d9c3283-4167-46e1-a66b-6b3f8e11930b" />


---

## Step 4: Create an Auto Scaling Group

1. In the EC2 left panel, click **"Auto Scaling Groups"** under **Auto Scaling**.
2. Click **"Create Auto Scaling group"**.

### Step 4a: Choose Launch Template
- **Name:** `MyAutoScalingGroup`
- **Launch template:** Select `MyLaunchTemplate`
- Click **"Next"**.

### Step 4b: Configure Instance Launch Options
- **VPC:** Select your VPC
- **Availability Zones and subnets:** Select **at least 2 public subnets** (one per AZ)
- Click **"Next"**.

### Step 4c: Configure Advanced Options (Attach Load Balancer)
- Under **"Load balancing"**: select **"Attach to an existing load balancer"**
- Choose **"Choose from your load balancer target groups"**
- Select `MyTargetGroup`
- Under **"Health checks":** enable **ELB health checks**
- Click **"Next"**.

### Step 4d: Configure Group Size and Scaling Policies
- **Desired capacity:** `2`
- **Minimum capacity:** `1`
- **Maximum capacity:** `4`
- Under **"Automatic scaling":** select **"Target tracking scaling policy"**
  - **Scaling policy name:** `CPUScalingPolicy`
  - **Metric type:** Average CPU Utilization
  - **Target value:** `70`  *(scale up if CPU > 70%)*
- Click **"Next"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/856d9a9e-6bee-4d66-96bf-3ff7fe2bf73b" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/22e9edab-b0e3-4883-a9f8-736cd2766b57" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/081eba9a-d606-4ff6-a3f9-71166299d157" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/12a6efa8-3234-4fd9-bf5f-3873717d7568" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/8c5c88b4-b8d7-4138-86a5-837ef6683c56" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/7964b0ac-2e14-4e8b-9094-e4a41820c2b9" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/04135993-12d6-41ab-aa96-043615698e77" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/24196617-ad7f-48a7-83f9-255039a6796e" />


### Step 4e: Add Notifications (Optional)
- Optionally configure an **SNS notification** for scale-out/in events.
- Click **"Next"**.

### Step 4f: Review and Create
- Review all settings.
- Click **"Create Auto Scaling group"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/de6e6400-eb0c-4fa1-b0cd-2fd8421957c9" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/99246531-0e0b-4c2d-81ba-071ea9016f01" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/c10afedd-1c8b-4923-bc55-35f97ee14aec" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/19676eff-4e6c-4b77-a752-2baabf16594d" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/7012ea5b-b27c-4c47-96fe-c0d8b77d91d5" />


---

## Step 5: Verify Instances and Load Balancer

1. Go to **EC2 → Instances** — you should see **2 instances** launched by the ASG (state: running).
2. Go to **EC2 → Target Groups → MyTargetGroup → Targets** tab.
   - Both instances should show **"Healthy"** status after a couple of minutes.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/48ad8378-0fd0-4321-891a-0506bd498df9" />


3. Copy the **DNS name** of your Load Balancer:
   - Go to **EC2 → Load Balancers → MyAppLoadBalancer**
   - Copy the **DNS name** (e.g., `MyAppLoadBalancer-12345.us-east-1.elb.amazonaws.com`)
4. Paste the DNS name in your browser.
5. You should see: **"Hello from ip-10-0-x-x.ec2.internal"**
6. Refresh the page multiple times — the hostname may change, showing traffic being distributed between instances.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/604f9d7b-f26f-4e84-9dcb-dd32cccd0109" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/bd03d0e6-835f-44d4-b1f5-3f4516e0432d" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/a109b548-0679-48d0-9c30-406247de32ce" />


---

## Step 6: Test Auto-Scaling by Simulating High CPU Usage

1. SSH into one of the EC2 instances launched by the ASG.
2. Install the `stress` tool:
   ```bash
   sudo amazon-linux-extras install epel -y
   sudo yum install stress -y
   ```
3. Simulate high CPU load:
   ```bash
   stress --cpu 4 --timeout 300
   ```
   This runs 4 CPU-intensive workers for 300 seconds (5 minutes).

4. Go to the **Auto Scaling Group → Activity** tab in the AWS Console.
5. Watch for a **"Launch"** activity — the ASG should launch additional instances when CPU exceeds 70%.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/2f22a855-364e-4483-8ebf-a03fa8275c4a" />
> <img width="602" height="543" alt="image" src="https://github.com/user-attachments/assets/ebb66ffa-4d50-493a-9ade-67b94e72359c" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/d4bc0926-106b-4dbb-abeb-1dca5d811c56" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/544d8153-53df-413d-a517-b242cb5251f5" />


> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: ASG Activity tab showing "Launching a new EC2 instance" event]
> ```

6. After stopping the `stress` command, the ASG will **scale in** and terminate the extra instances after the cool-down period (default 300 seconds).

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: ASG Activity tab showing "Terminating EC2 instance" after load decreased]
> ```

---

## Step 7: Monitor Scaling via CloudWatch

1. Go to **CloudWatch → Alarms**.
2. You'll find two auto-created alarms for the scaling policy:
   - One for **scale out** (CPU > 70%)
   - One for **scale in** (CPU drops below threshold)
3. Click on an alarm to view its graph and state.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/c357e452-f280-4643-b614-2d9677c450d0" />


---

## Clean Up (to avoid charges)
1. Delete the **Auto Scaling Group** (this will also terminate its instances).
2. Delete the **Application Load Balancer**.
3. Delete the **Target Group**.
4. Delete the **Launch Template**.

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Create an EC2 Launch Template with a User Data script
- ✅ Deploy an Application Load Balancer across multiple Availability Zones
- ✅ Create an Auto Scaling Group attached to the Load Balancer
- ✅ Configure a CPU-based target tracking scaling policy
- ✅ Simulate high load and observe the ASG scaling out automatically
