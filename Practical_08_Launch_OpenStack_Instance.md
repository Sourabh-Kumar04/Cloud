# Practical 8: Launch Your First Instance on OpenStack

**Objective:** Create a virtual machine (VM) using OpenStack by setting up projects, uploading images, defining flavors, and launching an instance via Horizon dashboard or CLI.

---

## Prerequisites
- A working OpenStack environment (refer to Practical 7)
- Access to the **OpenStack Horizon Dashboard** or CLI
- An Ubuntu cloud image downloaded (`.qcow2` format)

---

## Background Concepts

| Term | Description |
|---|---|
| Project (Tenant) | Logical container for grouping users and resources in OpenStack |
| User | An identity in OpenStack that can log in and manage resources |
| Role | A set of permissions assigned to a user within a project |
| Image (Glance) | A pre-built OS disk image used to boot instances |
| Flavor | A hardware profile specifying vCPU, RAM, and disk for an instance |
| Instance | A running virtual machine in OpenStack (managed by Nova) |
| Keystone | OpenStack's identity and authentication service |

---

## Step 1: Create a Project

A **Project** (also called a tenant) is the organizational unit in OpenStack.

### Via Horizon Dashboard:
1. Log in to Horizon as **admin** at `http://<your-ip>/dashboard`.
2. In the top navigation, switch to the **Admin** perspective.
3. Go to **Identity → Projects**.
4. Click **"Create Project"**.
5. Fill in:
   - **Name:** `CloudLab`
   - **Description:** `Student lab project`
   - **Enabled:** ✅ checked
6. Click **"Create Project"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Identity → Projects page in Horizon]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Create Project" dialog with name "CloudLab" filled in]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Projects list showing the newly created CloudLab project]
> ```

### Via CLI:
```bash
source /opt/stack/devstack/openrc admin admin
openstack project create --description "Student lab project" --enable CloudLab
openstack project list
```

---

## Step 2: Create a User and Assign Roles

### Create a New User:
#### Via Horizon:
1. Go to **Identity → Users**.
2. Click **"Create User"**.
3. Fill in:
   - **User Name:** `student1`
   - **Email:** `student1@example.com`
   - **Password:** `Student@123`
   - **Primary Project:** `CloudLab`
   - **Role:** `member`
4. Click **"Create User"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Create User" dialog with student1 details filled in]
> ```

#### Via CLI:
```bash
# Create user
openstack user create --project CloudLab --password Student@123 --enable student1

# Assign 'member' role to the user in the project
openstack role add --project CloudLab --user student1 member

# Verify
openstack user list
openstack role assignment list --project CloudLab
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CLI output showing user list with student1]
> ```

---

## Step 3: Upload an Image to Glance

**Glance** is OpenStack's image service. We'll upload an Ubuntu cloud image.

### Download the Ubuntu Cloud Image:
```bash
cd /tmp
wget https://cloud-images.ubuntu.com/jammy/current/jammy-server-cloudimg-amd64.img
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: wget downloading the Ubuntu cloud image]
> ```

### Upload via Horizon:
1. Go to **Admin → Compute → Images**.
2. Click **"Create Image"**.
3. Fill in:
   - **Image Name:** `Ubuntu-22.04`
   - **Image Description:** `Ubuntu 22.04 Jammy cloud image`
   - **Image Source:** File
   - Click **"Browse"** and select the downloaded `.img` file
   - **Format:** QCOW2 - QEMU Emulator
   - **Architecture:** x86_64
   - **Minimum Disk (GB):** 10
   - **Minimum RAM (MB):** 512
   - **Visibility:** Public
4. Click **"Create Image"**.
5. Wait for the status to change from **"Saving"** to **"Active"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Create Image" dialog with Ubuntu-22.04 details filled in]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Image list showing Ubuntu-22.04 with "Active" status]
> ```

### Upload via CLI:
```bash
source /opt/stack/devstack/openrc admin admin

openstack image create "Ubuntu-22.04" \
  --file /tmp/jammy-server-cloudimg-amd64.img \
  --disk-format qcow2 \
  --container-format bare \
  --public

# Verify upload
openstack image list
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CLI output of openstack image list showing Ubuntu-22.04 as active]
> ```

---

## Step 4: Define a Flavor

A **Flavor** specifies the compute resources (vCPUs, RAM, disk) for a VM.

### Create a Custom Flavor via Horizon:
1. Go to **Admin → Compute → Flavors**.
2. Click **"Create Flavor"**.
3. Fill in:
   - **Name:** `small.lab`
   - **ID:** Auto
   - **vCPUs:** `1`
   - **RAM (MB):** `512`
   - **Root Disk (GB):** `10`
   - **Ephemeral Disk (GB):** `0`
   - **Swap Disk (MB):** `0`
4. Click **"Create Flavor"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: "Create Flavor" dialog with small.lab configuration]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Flavors list showing small.lab flavor added]
> ```

### Create via CLI:
```bash
openstack flavor create --vcpus 1 --ram 512 --disk 10 --public small.lab
openstack flavor list
```

---

## Step 5: Create a Key Pair (for SSH Access)

### Via Horizon:
1. Switch to the **Project** perspective (top-left dropdown).
2. Select project **CloudLab**.
3. Go to **Project → Compute → Key Pairs**.
4. Click **"Create Key Pair"**.
5. Enter **Key Pair Name:** `lab-keypair`
6. Click **"Create Key Pair"** — this downloads a `lab-keypair.pem` file.
7. Save it securely.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Key Pairs page with "Create Key Pair" dialog]
> ```

### Via CLI:
```bash
source /opt/stack/devstack/openrc student1 CloudLab
openstack keypair create lab-keypair > ~/lab-keypair.pem
chmod 600 ~/lab-keypair.pem
openstack keypair list
```

---

## Step 6: Create a Security Group

1. Go to **Project → Network → Security Groups**.
2. Click **"Create Security Group"**.
3. Name: `lab-sg`, Description: `Allow SSH and ICMP`
4. Click **"Create Security Group"**.
5. Click **"Manage Rules"** on the new group.
6. Click **"Add Rule"** → set:
   - **Rule:** SSH (port 22)
   - **Remote:** CIDR — `0.0.0.0/0`
7. Click **"Add"**.
8. Click **"Add Rule"** again → set:
   - **Rule:** All ICMP
   - **Remote:** CIDR — `0.0.0.0/0`
9. Click **"Add"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Security Group rules for lab-sg showing SSH and ICMP rules]
> ```

---

## Step 7: Launch an Instance

### Via Horizon Dashboard:
1. Go to **Project → Compute → Instances**.
2. Click **"Launch Instance"**.

#### Tab 1 — Details:
- **Instance Name:** `MyFirstVM`
- **Description:** `Test VM`
- **Availability Zone:** nova
- **Count:** 1

#### Tab 2 — Source:
- **Select Boot Source:** Image
- Click the **▲** arrow next to `Ubuntu-22.04` to add it to the "Allocated" section

#### Tab 3 — Flavor:
- Click the **▲** arrow next to `small.lab`

#### Tab 4 — Networks:
- Click the **▲** arrow next to the available network (e.g., `public` or `private`)

#### Tab 6 — Security Groups:
- Remove `default`, add `lab-sg`

#### Tab 7 — Key Pair:
- Select `lab-keypair`

3. Click **"Launch Instance"**.
4. Wait for the Status to change from **"Build"** → **"Active"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Launch Instance wizard — Details tab with instance name MyFirstVM]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Launch Instance wizard — Source tab with Ubuntu-22.04 allocated]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Launch Instance wizard — Flavor tab with small.lab allocated]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Instances list showing MyFirstVM with "Active" status]
> ```

---

### Via CLI:
```bash
source /opt/stack/devstack/openrc admin admin

# Get required IDs
openstack image list
openstack flavor list
openstack network list

# Launch the instance
openstack server create \
  --image "Ubuntu-22.04" \
  --flavor small.lab \
  --key-name lab-keypair \
  --security-group lab-sg \
  --network private \
  MyFirstVM

# Check the status (repeat until ACTIVE)
openstack server show MyFirstVM
openstack server list
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CLI output showing openstack server list with MyFirstVM in ACTIVE state]
> ```

---

## Step 8: Access the Instance Console (via Horizon)

1. Click on the instance name `MyFirstVM` in the instances list.
2. Go to the **"Console"** tab.
3. You should see the VM's VNC console.
4. You may log in with default credentials (varies by image) or via SSH.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Horizon VNC console tab showing the Ubuntu login prompt]
> ```

---

## Step 9: Verify the Instance is Running

```bash
# From CLI — check instance details
openstack server show MyFirstVM

# Check console log
openstack console log show MyFirstVM
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: openstack server show output with IP address, status, and image info]
> ```

---

## Summary of Components Used

| Component | Service | What We Did |
|---|---|---|
| Project | Keystone | Created `CloudLab` project |
| User/Role | Keystone | Created `student1`, assigned `member` role |
| Image | Glance | Uploaded Ubuntu 22.04 cloud image |
| Flavor | Nova | Created `small.lab` (1 vCPU, 512MB RAM) |
| Key Pair | Nova | Created `lab-keypair` for SSH access |
| Instance | Nova | Launched `MyFirstVM` |

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Create a project and assign user roles in OpenStack (Keystone)
- ✅ Upload a cloud image to the Glance image service
- ✅ Define a custom flavor with specific compute resources
- ✅ Launch a VM instance using the Horizon dashboard and CLI
- ✅ Access the instance console via the Horizon VNC viewer
