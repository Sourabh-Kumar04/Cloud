# Practical 2: Launch Your First Amazon EC2 Instance

**Objective:** Deploy a virtual machine on AWS using Amazon EC2.

---

## Prerequisites
- An active AWS account (free tier)
- A terminal with SSH client installed:
  - **Windows:** Use PuTTY or Windows Terminal (WSL)
  - **Linux/macOS:** SSH is available natively in the terminal

---

## Step 1: Navigate to EC2 in the AWS Console

1. Log in to the **AWS Management Console** at [https://console.aws.amazon.com](https://console.aws.amazon.com).
2. In the **search bar** at the top, type `EC2` and click the **EC2** service.
3. You will land on the **EC2 Dashboard** showing running instances, key pairs, security groups, etc.
4. Click the orange **"Launch instance"** button.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: EC2 Dashboard with "Launch instance" button highlighted]
> ```

---

## Step 2: Configure the Instance

### 2a. Name Your Instance
1. Under **"Name and tags"**, enter a name for your instance, e.g., `MyFirstEC2`.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Name and tags section with "MyFirstEC2" entered]
> ```

---

### 2b. Choose a Pre-Configured AMI (Amazon Machine Image)
1. Under **"Application and OS Images (Amazon Machine Image)"**, click **"Quick Start"**.
2. Select **Amazon Linux** (the first option shown).
3. Confirm the AMI selected is **Amazon Linux 2023 AMI** or **Amazon Linux 2** — both are free-tier eligible (look for the **"Free tier eligible"** tag).

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: AMI selection page showing Amazon Linux 2 with "Free tier eligible" badge]
> ```

---

### 2c. Select Instance Type
1. Under **"Instance type"**, the default `t2.micro` (1 vCPU, 1 GiB RAM) should already be selected.
2. Confirm it shows **"Free tier eligible"**.
3. Leave it as `t2.micro`.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Instance type selection showing t2.micro selected]
> ```

---

### 2d. Create a Key Pair (for SSH access)
1. Under **"Key pair (login)"**, click **"Create new key pair"**.
2. In the dialog:
   - **Key pair name:** `my-ec2-keypair`
   - **Key pair type:** RSA
   - **Private key file format:**
     - Choose `.pem` (for Linux/macOS/WSL)
     - Choose `.ppk` (for PuTTY on Windows)
3. Click **"Create key pair"** — the `.pem` file will **automatically download** to your computer.
4. **Important:** Store this file securely. You cannot download it again.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Create key pair" dialog with name and format filled in]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Key pair file downloaded in browser downloads bar]
> ```

---

## Step 3: Configure Security Groups to Allow SSH Access

1. Scroll down to the **"Network settings"** section.
2. Click **"Edit"** on the right side.
3. Confirm a **VPC** is selected (the default VPC is fine for now).
4. Under **"Firewall (security groups)"**, select **"Create security group"**.
5. Give it a name: `my-ec2-sg`
6. Under **"Inbound Security Group Rules"**, verify the following rule exists:
   - **Type:** SSH
   - **Protocol:** TCP
   - **Port range:** 22
   - **Source type:** My IP *(This restricts SSH access to only your IP — more secure than 0.0.0.0/0)*
7. If the rule is not present, click **"Add security group rule"** and set the above values.

> ⚠️ **Security Note:** Avoid setting the source to `0.0.0.0/0` (open to the entire internet). Always use **"My IP"** for SSH in learning environments.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Security group configuration with SSH rule on port 22, source = My IP]
> ```

---

## Step 4: Configure Storage (optional for free tier)

1. Scroll to **"Configure storage"**.
2. The default is **8 GiB gp3 (General Purpose SSD)** — this is free-tier eligible.
3. Leave it as-is.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Storage configuration showing 8 GiB gp3]
> ```

---

## Step 5: Review and Launch

1. Scroll to the **"Summary"** panel on the right side.
2. Review the configuration:
   - AMI: Amazon Linux 2
   - Instance type: t2.micro
   - Key pair: my-ec2-keypair
   - Security group: my-ec2-sg
3. Click the orange **"Launch instance"** button.
4. A success message will appear. Click **"View all instances"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Successfully initiated launch" confirmation page]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: EC2 Instances list showing the new instance in "running" state]
> ```

---

## Step 6: Connect to the Instance Using SSH

### Prerequisite: Set Correct Permissions on the .pem File

On **Linux/macOS**, open a terminal and run:
```bash
chmod 400 ~/Downloads/my-ec2-keypair.pem
```

On **Windows (WSL)**, navigate to the file location and run the same command.

---

### Get the Public IP Address
1. In the EC2 console, click on your instance.
2. In the **"Details"** tab, copy the **Public IPv4 address** (e.g., `54.234.12.98`).

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: EC2 Instance details panel showing Public IPv4 address]
> ```

---

### SSH into the Instance

Open your terminal and run:

```bash
ssh -i ~/Downloads/my-ec2-keypair.pem ec2-user@<YOUR_PUBLIC_IP>
```

Replace `<YOUR_PUBLIC_IP>` with the actual IP address copied in the previous step.

**Example:**
```bash
ssh -i ~/Downloads/my-ec2-keypair.pem ec2-user@54.234.12.98
```

3. If prompted with:
   ```
   The authenticity of host '...' can't be established. Are you sure you want to continue connecting (yes/no)?
   ```
   Type `yes` and press **Enter**.

4. You should see the Amazon Linux welcome banner:
   ```
        __|  __|_  )
        _|  (     /   Amazon Linux 2 AMI
       ___|\___|___|
   ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal window showing successful SSH login to EC2 instance with Amazon Linux banner]
> ```

---

## Step 7: Basic Commands After Login

Once connected, try these commands to verify your environment:

```bash
# Check the OS version
cat /etc/os-release

# Check hostname
hostname

# Check CPU and memory
nproc
free -h

# Check disk space
df -h

# Update packages
sudo yum update -y
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing output of the above commands]
> ```

---

## Step 8: Stop / Terminate the Instance (to avoid charges)

1. Go back to the **EC2 Console → Instances**.
2. Select your instance by checking the checkbox next to it.
3. Click **"Instance state"** → **"Stop instance"** (pauses the instance; EBS storage still incurs minor charges).
   - Or click **"Terminate instance"** to permanently delete it (no further charges).
4. Confirm the action in the dialog box.

> ⚠️ **Note:** Free tier gives 750 hours/month of t2.micro usage. Always stop or terminate instances when not in use to stay within the free tier.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Stop instance" option in the Instance state dropdown menu]
> ```

---

## Summary of Key Concepts

| Concept | Description |
|---|---|
| AMI | Pre-configured OS image used to launch EC2 instances |
| Instance Type | Defines the hardware (CPU, RAM) allocated to the VM |
| Key Pair | Public/private key pair used for secure SSH authentication |
| Security Group | Virtual firewall that controls inbound/outbound traffic |
| Public IPv4 | The public address used to access the EC2 instance |

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Launch an EC2 instance from the AWS Console using an Amazon Linux AMI
- ✅ Create and use a key pair for SSH authentication
- ✅ Configure a security group to allow SSH access on port 22
- ✅ Connect to a running EC2 instance via SSH from a terminal
