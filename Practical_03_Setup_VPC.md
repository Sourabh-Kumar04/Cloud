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
> 
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/dec7827a-f63d-4278-b70e-67b9feb08c95" />
> 

> 📸 **Screenshot Placeholder**
>
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/d7f9a49e-baf7-4d63-a5d3-70483b33424f" />
> 

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
>
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/be70428d-7a36-4dd3-a34e-1c2997898a58" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/ca29661c-a584-4e8e-b22c-6d2afd657815" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/a5f22444-100a-43b4-9a28-19413022cd45" />
> 


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
>
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/833bcbb8-47c8-4687-ae5a-7e0ec5030957" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/ab696b91-0c83-43e5-86b0-09e7d1c262dc" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/6d877cba-a2f4-4a55-a1bf-b422c0ac5001" />

> 

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
> 
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/88ea68c3-fd94-47a5-8a4e-10a95a46a663" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/dd729000-7580-46e1-ab06-2c0771321ea4" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/f2d8ef82-597a-4bf0-9a4c-d1c0b69a0736" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/9df92b14-7679-4821-a9dc-525d3f08bbc0" />
>

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
> 
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/d9f04650-82a9-42dd-9efa-8d5144d8fafa" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/51ab2817-1539-4b47-a9a5-87682815871b" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/b048d1b5-e7b1-4c9c-be36-830268e56519" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/5e6ccb82-86f1-4924-ab6e-2eb1bd47886a" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/9d47ed1c-0ff8-4f81-8a24-7edd3da9bc0a" />
> 

---

## Step 6: Enable Auto-Assign Public IP for the Public Subnet

1. Go back to **"Subnets"** in the left menu.
2. Select **PublicSubnet**.
3. Click **"Actions"** → **"Edit subnet settings"**.
4. Check **"Enable auto-assign public IPv4 address"**.
5. Click **"Save"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/244a20ac-fb56-46d3-878a-f64068bd75c7" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/2d691f81-401e-4e36-a18d-21f0e7439863" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/7836cb37-3e2b-4b5f-ad1d-7601e9f9095d" />

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
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/0862bb4c-dec1-4026-8f29-22b911aaa5c4" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/a2a1de5a-3541-48b4-ad34-4cb6c625fc5f" />

> 

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
>
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/7684004e-877d-47fc-b009-fa7cc0d7dfd0" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/ab2bba27-6068-4180-bab3-8fc8b689ba4f" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/bed97de9-bcde-4572-99ad-5e8611d92531" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/645e9dd6-12dd-4ad2-bffb-828838ce1975" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/4a5aa53d-1061-472f-a486-362ad401b33e" />
> 

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
> 
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/6e6516f5-9185-4772-b9f1-7e1ffadbc32b" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/a5be8111-f126-4879-aab7-cbce6e57b269" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/c21bc02f-740c-4101-a65e-71af59da773f" />


### Launch an instance in the Private Subnet
1. Launch another EC2 instance.
2. Under **"Network settings"**:
   - VPC: `MyCustomVPC`
   - Subnet: `PrivateSubnet`
   - Auto-assign public IP: **Disable**
3. Use the same key pair and a security group that allows SSH from `10.0.1.0/24` (the public subnet CIDR).
4. Launch the instance.

> 📸 **Screenshot Placeholder**
>
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/84aa4b63-089a-43af-90a7-2a5e2d515e27" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/49f8ce7d-5893-4694-ae0a-d51b672e00f4" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/0854a385-eb25-4d3e-89ad-7e070e051ac3" />

> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/670fb2bb-ef86-48dc-8ac0-5aba695e8a66" />

> 

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

> <img width="602" height="543" alt="image" src="https://github.com/user-attachments/assets/538591e5-cf46-45c2-a3b3-a69a8f1fb3e2" />
> <img width="825" height="521" alt="image" src="https://github.com/user-attachments/assets/63bb2f38-5dd4-4dce-aeac-3ff24901367d" />
> <img width="825" height="521" alt="image" src="https://github.com/user-attachments/assets/ee9afdc6-51b7-46c2-9def-f84f430a9849" />
> <img width="602" height="543" alt="image" src="https://github.com/user-attachments/assets/0559f81e-86be-4708-b520-ed02991de132" />


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
