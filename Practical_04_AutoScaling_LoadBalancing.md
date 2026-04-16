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
> ```
> [Screenshot: Launch Template creation form with AMI, instance type, and User Data filled in]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Launch Template successfully created confirmation page]
> ```

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
> ```
> [Screenshot: Target Group creation form with HTTP port 80 and health check configured]
> ```

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
> ```
> [Screenshot: ALB creation page showing network mapping with 2 subnets selected]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: ALB successfully created with "Active" state]
> ```

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
> ```
> [Screenshot: ASG Group size settings with desired=2, min=1, max=4]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Scaling policy configuration showing CPU target at 70%]
> ```

### Step 4e: Add Notifications (Optional)
- Optionally configure an **SNS notification** for scale-out/in events.
- Click **"Next"**.

### Step 4f: Review and Create
- Review all settings.
- Click **"Create Auto Scaling group"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: ASG created successfully, showing 2 instances being launched]
> ```

---

## Step 5: Verify Instances and Load Balancer

1. Go to **EC2 → Instances** — you should see **2 instances** launched by the ASG (state: running).
2. Go to **EC2 → Target Groups → MyTargetGroup → Targets** tab.
   - Both instances should show **"Healthy"** status after a couple of minutes.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Target Group Targets tab showing 2 instances as "Healthy"]
> ```

3. Copy the **DNS name** of your Load Balancer:
   - Go to **EC2 → Load Balancers → MyAppLoadBalancer**
   - Copy the **DNS name** (e.g., `MyAppLoadBalancer-12345.us-east-1.elb.amazonaws.com`)
4. Paste the DNS name in your browser.
5. You should see: **"Hello from ip-10-0-x-x.ec2.internal"**
6. Refresh the page multiple times — the hostname may change, showing traffic being distributed between instances.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Browser showing "Hello from ip-10-0-1-x.ec2.internal" web page]
> ```

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
> ```
> [Screenshot: Terminal showing stress command running]
> ```

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
> ```
> [Screenshot: CloudWatch Alarm showing CPU utilization exceeding 70% threshold]
> ```

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
