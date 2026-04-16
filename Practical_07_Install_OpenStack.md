# Practical 7: Install OpenStack

**Objective:** Set up a local OpenStack environment for practice using DevStack — the easiest way to install a development OpenStack environment on a single machine.

---

## Prerequisites

| Requirement | Minimum Specification |
|---|---|
| OS | Ubuntu 22.04 LTS (recommended) or 20.04 LTS |
| RAM | 8 GB minimum (16 GB recommended) |
| Disk | 60 GB free disk space minimum |
| CPU | 4 cores (hardware virtualization support: VT-x / AMD-V) |
| Network | Internet access (for downloading packages) |
| User | A non-root user with sudo privileges |

> ⚠️ **Important:** Do NOT run DevStack as the root user. It must be run by a regular user with sudo access.

---

## Option 1: Install on a Physical or Virtual Machine (Recommended)

### Step 1: Prepare an Ubuntu 22.04 Machine

If using VirtualBox or VMware:
1. Download Ubuntu 22.04 ISO from [https://ubuntu.com/download/server](https://ubuntu.com/download/server)
2. Create a VM with:
   - RAM: 8 GB+
   - Disk: 60 GB+
   - Enable hardware virtualization (VT-x/AMD-V in VM settings)
3. Install Ubuntu Server and create a user (e.g., `stack`)

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: VirtualBox VM settings showing 8GB RAM and hardware virtualization enabled]
> ```

---

### Step 2: Update the System

Open a terminal and run:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git curl vim
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing apt update and upgrade completing successfully]
> ```

---

### Step 3: Create the `stack` User (if not already created)

DevStack should run as a dedicated user, not `root` and ideally not your main user.

```bash
# Create a user named 'stack'
sudo useradd -s /bin/bash -d /opt/stack -m stack

# Give the stack user sudo privileges without password prompt
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
sudo chmod 0440 /etc/sudoers.d/stack

# Switch to the stack user
sudo su - stack
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing stack user created and switched to stack user]
> ```

---

### Step 4: Clone the DevStack Repository

```bash
git clone https://opendev.org/openstack/devstack /opt/stack/devstack
cd /opt/stack/devstack
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing git clone of devstack completing]
> ```

---

### Step 5: Create the Local Configuration File

Create a file called `local.conf` inside the devstack directory:

```bash
cat > /opt/stack/devstack/local.conf << 'EOF'
[[local|localrc]]
# Admin credentials
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD

# Host IP (change this to your machine's actual IP)
HOST_IP=127.0.0.1

# Enable essential services
ENABLED_SERVICES=g-api,g-reg,key,n-api,n-crt,n-obj,n-cpu,n-sch,n-novnc,n-cond
ENABLED_SERVICES+=,q-svc,q-agt,q-dhcp,q-l3,q-meta
ENABLED_SERVICES+=,s-proxy,s-object,s-container,s-account
ENABLED_SERVICES+=,horizon,rabbit,mysql

# Logging
LOGFILE=/opt/stack/logs/stack.sh.log
VERBOSE=True
LOG_COLOR=True
SCREEN_LOGDIR=/opt/stack/logs

# Branch — use stable release
OPENSTACK_BRANCH=stable/2023.1
EOF
```

To get your actual IP address:
```bash
ip addr show | grep "inet " | grep -v 127.0.0.1
```

Replace `127.0.0.1` in the config with your machine's IP address if accessing from another machine.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: local.conf file contents displayed in terminal using cat]
> ```

---

### Step 6: Run the DevStack Installer

```bash
cd /opt/stack/devstack
./stack.sh
```

> ⚠️ **This process will take 20–45 minutes** depending on your internet speed and machine power. It downloads and configures all OpenStack services.

You will see output like:
```
This is your host IP address: 192.168.x.x
This is your host IPv6 address: ::1
Horizon is now available at http://192.168.x.x/dashboard
Keystone is serving at http://192.168.x.x/identity/
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: stack.sh running, showing services being installed]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: DevStack installation complete — showing "This is your host IP address" and Horizon URL]
> ```

---

### Step 7: Access the OpenStack Dashboard (Horizon)

1. Open a browser and navigate to:
   ```
   http://<YOUR_HOST_IP>/dashboard
   ```
2. Login credentials:
   - **Username:** `admin`
   - **Password:** `secret` *(or whatever you set in local.conf)*
   - **Domain:** `Default`

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: OpenStack Horizon login page]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: OpenStack Horizon Dashboard showing compute overview after login]
> ```

---

## Option 2: Use an Online OpenStack Lab (No Installation Needed)

If setting up locally is not feasible, use these free browser-based environments:

### TryStack
- Visit: [https://trystack.openstack.org](https://trystack.openstack.org)
- Register with a Facebook or Google account
- You get a free OpenStack environment to practice with

### OpenStack on Katacoda / Killercoda
- Visit: [https://killercoda.com](https://killercoda.com)
- Search for OpenStack scenarios

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: TryStack login page]
> ```

---

## Step 8: Verify OpenStack Services via CLI

After installation, set up the OpenStack CLI:

```bash
# Source the admin credentials
source /opt/stack/devstack/openrc admin admin

# List running OpenStack services
openstack service list

# Check compute endpoints
openstack endpoint list

# Check available images
openstack image list
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing openstack service list output with all services listed]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: openstack image list showing available images]
> ```

---

## Step 9: Explore the Horizon Dashboard

After login, familiarize yourself with the following sections:

1. **Project → Compute → Overview** — shows usage quotas
2. **Project → Compute → Instances** — running VMs
3. **Project → Network → Networks** — virtual networks
4. **Admin → System Information** — shows all running services

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Horizon showing Compute Overview with CPU, RAM, and Instance quotas]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Horizon showing System Information with services listed]
> ```

---

## Troubleshooting

| Issue | Solution |
|---|---|
| stack.sh fails midway | Run `./unstack.sh` then `./clean.sh` then re-run `./stack.sh` |
| Horizon not accessible | Check if Apache is running: `sudo systemctl status apache2` |
| Services not starting | Check logs at `/opt/stack/logs/` |
| Insufficient disk space | Ensure at least 60 GB free: `df -h` |

---

## Stopping and Restarting DevStack

```bash
# To stop all services
cd /opt/stack/devstack
./unstack.sh

# To restart all services
./stack.sh
```

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Prepare an Ubuntu machine for OpenStack installation
- ✅ Configure DevStack's `local.conf` file with appropriate settings
- ✅ Run the DevStack installer and wait for it to complete successfully
- ✅ Log in to the OpenStack Horizon dashboard
- ✅ Use the OpenStack CLI to list services, endpoints, and images
