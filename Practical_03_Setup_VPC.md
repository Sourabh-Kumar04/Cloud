# Practical 3: Set Up a Virtual Private Cloud (VPC)

**Objective:** Create and configure a Virtual Private Cloud (VPC) with public and private subnets, Internet Gateway, and NAT Gateway.

---

## Prerequisites
- An active AWS account
- Basic understanding of IP addressing and CIDR notation
- Familiarity with EC2 (see Practical 2)

---

## Background: Key Networking Concepts

| Term | Description |
|---|---|
| VPC | Isolated virtual network within AWS where you launch resources |
| Subnet | A range of IP addresses within a VPC; can be public or private |
| CIDR Block | Notation for defining IP address ranges (e.g., `10.0.0.0/16`) |
| Internet Gateway (IGW) | Allows internet traffic in/out of the public subnet |
| NAT Gateway | Lets private subnet instances access the internet without being publicly accessible |
| Route Table | Rules that determine where network traffic is directed |

---

## Step 1: Create a Custom VPC

1. Log in to the **AWS Management Console**.
2. Search for **VPC** in the search bar and open the **VPC Dashboard**.
3. In the left menu, click **"Your VPCs"**.
4. Click **"Create VPC"**.
5. Fill in the following:
   - **Resources to create:** VPC only
   - **Name tag:** `MyCustomVPC`
   - **IPv4 CIDR block:** `10.0.0.0/16`  *(This gives you 65,536 IP addresses)*
   - **IPv6 CIDR block:** No IPv6 CIDR block
   - **Tenancy:** Default
6. Click **"Create VPC"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Create VPC" form with CIDR 10.0.0.0/16 filled in]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: VPC successfully created with VPC ID shown]
> ```

---

## Step 2: Create a Public Subnet

1. In the **VPC Dashboard**, click **"Subnets"** in the left menu.
2. Click **"Create subnet"**.
3. Fill in:
   - **VPC ID:** Select `MyCustomVPC`
   - **Subnet name:** `PublicSubnet`
   - **Availability Zone:** Select any (e.g., `us-east-1a`)
   - **IPv4 CIDR block:** `10.0.1.0/24`  *(256 addresses)*
4. Click **"Create subnet"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Create subnet form for PublicSubnet with CIDR 10.0.1.0/24]
> ```

---

## Step 3: Create a Private Subnet

1. Click **"Create subnet"** again.
2. Fill in:
   - **VPC ID:** Select `MyCustomVPC`
   - **Subnet name:** `PrivateSubnet`
   - **Availability Zone:** Select any (e.g., `us-east-1b`)
   - **IPv4 CIDR block:** `10.0.2.0/24`
3. Click **"Create subnet"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Create subnet form for PrivateSubnet with CIDR 10.0.2.0/24]
> ```

---

## Step 4: Create and Attach an Internet Gateway

An **Internet Gateway (IGW)** is required so that instances in the public subnet can communicate with the internet.

1. In the left menu, click **"Internet Gateways"**.
2. Click **"Create internet gateway"**.
3. Enter:
   - **Name tag:** `MyIGW`
4. Click **"Create internet gateway"**.
5. On the next page, click **"Actions"** → **"Attach to VPC"**.
6. Select `MyCustomVPC` from the dropdown.
7. Click **"Attach internet gateway"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Internet Gateway created and showing "Detached" state]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Attach to VPC" dialog with MyCustomVPC selected]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Internet Gateway now showing "Attached" state]
> ```

---

## Step 5: Create a Public Route Table

1. In the left menu, click **"Route Tables"**.
2. Click **"Create route table"**.
3. Fill in:
   - **Name:** `PublicRouteTable`
   - **VPC:** `MyCustomVPC`
4. Click **"Create route table"**.

### Add a Route to the Internet
1. Select `PublicRouteTable` from the list.
2. Click the **"Routes"** tab → **"Edit routes"**.
3. Click **"Add route"**:
   - **Destination:** `0.0.0.0/0`  *(all traffic)*
   - **Target:** Select **"Internet Gateway"** → choose `MyIGW`
4. Click **"Save changes"**.

### Associate the Public Subnet with this Route Table
1. Click the **"Subnet associations"** tab → **"Edit subnet associations"**.
2. Check the box next to **PublicSubnet**.
3. Click **"Save associations"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Public route table showing route 0.0.0.0/0 pointing to Internet Gateway]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Subnet associations tab showing PublicSubnet linked]
> ```

---

## Step 6: Enable Auto-Assign Public IP for the Public Subnet

1. Go back to **"Subnets"** in the left menu.
2. Select **PublicSubnet**.
3. Click **"Actions"** → **"Edit subnet settings"**.
4. Check **"Enable auto-assign public IPv4 address"**.
5. Click **"Save"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Subnet settings page with auto-assign public IP enabled]
> ```

---

## Step 7: Create a NAT Gateway for the Private Subnet

A **NAT Gateway** allows instances in the private subnet to access the internet (for updates, etc.) without exposing them publicly.

1. In the left menu, click **"NAT Gateways"**.
2. Click **"Create NAT gateway"**.
3. Fill in:
   - **Name:** `MyNATGateway`
   - **Subnet:** Select **PublicSubnet** *(NAT Gateway must reside in the public subnet)*
   - **Connectivity type:** Public
4. Click **"Allocate Elastic IP"** to assign a new Elastic IP automatically.
5. Click **"Create NAT gateway"**.
6. Wait for the state to change from **"Pending"** to **"Available"** (may take 1–2 minutes).

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: NAT Gateway creation form with PublicSubnet selected]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: NAT Gateway in "Available" state with Elastic IP assigned]
> ```

---

## Step 8: Create a Private Route Table

1. Go to **"Route Tables"** → click **"Create route table"**.
2. Fill in:
   - **Name:** `PrivateRouteTable`
   - **VPC:** `MyCustomVPC`
3. Click **"Create route table"**.

### Add Route to NAT Gateway
1. Select `PrivateRouteTable`.
2. Click **"Routes"** → **"Edit routes"** → **"Add route"**:
   - **Destination:** `0.0.0.0/0`
   - **Target:** Select **"NAT Gateway"** → choose `MyNATGateway`
3. Click **"Save changes"**.

### Associate the Private Subnet
1. Click **"Subnet associations"** → **"Edit subnet associations"**.
2. Check **PrivateSubnet**.
3. Click **"Save associations"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Private route table with route 0.0.0.0/0 pointing to NAT Gateway]
> ```

---

## Step 9: Launch EC2 Instances in Each Subnet

### Launch an instance in the Public Subnet
1. Go to **EC2 → Launch instance**.
2. Use **Amazon Linux 2 AMI**, instance type `t2.micro`.
3. Under **"Network settings"**:
   - VPC: `MyCustomVPC`
   - Subnet: `PublicSubnet`
   - Auto-assign public IP: **Enable**
4. Configure a security group to allow **SSH (port 22)** from your IP.
5. Select your key pair and launch.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: EC2 launch wizard showing VPC = MyCustomVPC, Subnet = PublicSubnet]
> ```

### Launch an instance in the Private Subnet
1. Launch another EC2 instance.
2. Under **"Network settings"**:
   - VPC: `MyCustomVPC`
   - Subnet: `PrivateSubnet`
   - Auto-assign public IP: **Disable**
3. Use the same key pair and a security group that allows SSH from `10.0.1.0/24` (the public subnet CIDR).
4. Launch the instance.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: EC2 launch wizard showing VPC = MyCustomVPC, Subnet = PrivateSubnet, no public IP]
> ```

---

## Step 10: Test Connectivity

### Test Public Subnet Instance
1. From your local machine, SSH into the **public instance**:
   ```bash
   ssh -i my-ec2-keypair.pem ec2-user@<PUBLIC_INSTANCE_IP>
   ```
2. Ping a public address to verify internet access:
   ```bash
   ping -c 4 google.com
   ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing successful ping from public EC2 instance]
> ```

### Test Private Subnet Instance (via Bastion/Jump)
1. From the public instance, SSH to the private instance using its **private IP**:
   ```bash
   ssh -i my-ec2-keypair.pem ec2-user@<PRIVATE_INSTANCE_PRIVATE_IP>
   ```
2. On the private instance, verify internet access through NAT:
   ```bash
   ping -c 4 google.com
   curl https://ifconfig.me   # Should show the NAT Gateway's Elastic IP
   ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing successful ping from private EC2 instance via NAT Gateway]
> ```

---

## Architecture Diagram

```
Internet
    |
    | (public traffic)
    |
[Internet Gateway]
    |
    |--- [PublicSubnet 10.0.1.0/24] --- [Public EC2 Instance]
    |           |
    |    [NAT Gateway]
    |           |
    |--- [PrivateSubnet 10.0.2.0/24] --- [Private EC2 Instance]

All inside: MyCustomVPC (10.0.0.0/16)
```

---

## Clean Up (to avoid charges)
1. Terminate both EC2 instances.
2. Delete the **NAT Gateway** (charges apply per hour even if idle).
3. Release the **Elastic IP** associated with the NAT Gateway.
4. Detach and delete the **Internet Gateway**.
5. Delete the **Subnets**, **Route Tables**, and finally the **VPC**.

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Create a custom VPC with a defined CIDR block
- ✅ Create public and private subnets in different Availability Zones
- ✅ Attach and configure an Internet Gateway for public internet access
- ✅ Deploy a NAT Gateway to provide outbound internet access for private instances
- ✅ Launch and connect to EC2 instances in both subnet types
