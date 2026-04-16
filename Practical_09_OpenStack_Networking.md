# Practical 9: Set Up Networking in OpenStack (Neutron)

**Objective:** Configure OpenStack Neutron to provide networking for instances by creating private and public networks, attaching a router, and assigning floating IPs for external access.

---

## Prerequisites
- A running OpenStack environment (refer to Practical 7)
- An instance running in OpenStack (refer to Practical 8)
- Admin or project-member access to the Horizon dashboard or CLI
- Source the admin credentials in terminal:
  ```bash
  source /opt/stack/devstack/openrc admin admin
  ```

---

## Background Concepts

| Term | Description |
|---|---|
| Neutron | OpenStack's networking service |
| Network | A virtual L2 segment (like a LAN) |
| Subnet | An IP range defined within a network |
| Router | A virtual device that routes traffic between networks |
| Floating IP | A public IP that can be associated with an instance for external access |
| External Network | The network that connects OpenStack to the outside world (internet) |
| DHCP | Automatically assigns IP addresses to instances within a subnet |

---

## Step 1: Verify the External Network (Public Network)

In DevStack, an external network called `public` is usually created automatically.

### Via CLI:
```bash
source /opt/stack/devstack/openrc admin admin
openstack network list
```

Look for a network named `public` or `external` with `--external` flag.

```bash
# Show details of the public network
openstack network show public
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CLI output of openstack network list showing "public" external network]
> ```

### Via Horizon:
1. Go to **Admin → Network → Networks**.
2. Look for a network marked as **"External"** (usually named `public`).

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Horizon Networks list showing the public external network]
> ```

---

## Step 2: Create a Private Network

### Via Horizon:
1. Switch to the **Project** view, select project `CloudLab`.
2. Go to **Project → Network → Networks**.
3. Click **"Create Network"**.

#### Network tab:
- **Network Name:** `private-net`
- **Enable Admin State:** ✅
- **Create Subnet:** ✅
- **Availability Zone Hints:** (leave blank)

#### Subnet tab:
- **Subnet Name:** `private-subnet`
- **Network Address:** `192.168.100.0/24`
- **IP Version:** IPv4
- **Gateway IP:** `192.168.100.1`
- **Disable Gateway:** ❌ (leave unchecked)

#### Subnet Details tab:
- **Enable DHCP:** ✅
- **DNS Name Servers:** `8.8.8.8` (Google DNS)
- Leave Allocation Pools blank (auto-generates from CIDR)

4. Click **"Create"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Create Network dialog — Network tab with name private-net]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Create Network dialog — Subnet tab with CIDR 192.168.100.0/24]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Network list showing private-net added successfully]
> ```

### Via CLI:
```bash
# Create the private network
openstack network create private-net

# Create a subnet for the private network
openstack subnet create private-subnet \
  --network private-net \
  --subnet-range 192.168.100.0/24 \
  --gateway 192.168.100.1 \
  --dns-nameserver 8.8.8.8

# Verify
openstack network list
openstack subnet list
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CLI output showing private-net and private-subnet created]
> ```

---

## Step 3: Create a Router and Attach Networks

A **router** connects the private network to the external (public) network.

### Via Horizon:
1. Go to **Project → Network → Routers**.
2. Click **"Create Router"**.
3. Fill in:
   - **Router Name:** `lab-router`
   - **Admin State:** UP
   - **External Network:** Select `public` (the external/internet-facing network)
4. Click **"Create Router"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Create Router dialog with name lab-router and external network set to public]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Router list showing lab-router with external gateway set]
> ```

5. Click on **`lab-router`** to open its details.
6. Go to the **"Interfaces"** tab.
7. Click **"Add Interface"**.
8. Fill in:
   - **Subnet:** `private-subnet (192.168.100.0/24)`
   - **IP Address:** Leave blank (auto-assigned: `192.168.100.1`)
9. Click **"Submit"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Router Interfaces tab showing Add Interface dialog with private-subnet selected]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Router Interfaces tab showing the interface added with 192.168.100.1]
> ```

### Via CLI:
```bash
# Create the router with external gateway
openstack router create lab-router
openstack router set lab-router --external-gateway public

# Add the private subnet as an interface on the router
openstack router add subnet lab-router private-subnet

# Verify
openstack router list
openstack router show lab-router
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CLI output of openstack router show lab-router with interfaces listed]
> ```

---

## Step 4: Verify Network Topology

### Via Horizon:
1. Go to **Project → Network → Network Topology**.
2. You should see a visual diagram showing:
   - `public` network ↔ `lab-router` ↔ `private-net` ↔ instances

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Network Topology diagram showing public network, router, and private network connected]
> ```

---

## Step 5: Assign a Floating IP to an Instance

A **Floating IP** is a public IP from the external network that you associate with an instance so it can be reached from outside.

### Allocate a Floating IP:

#### Via Horizon:
1. Go to **Project → Network → Floating IPs**.
2. Click **"Allocate IP to Project"**.
3. Select **Pool:** `public`
4. Click **"Allocate IP"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Allocate Floating IP dialog with pool set to "public"]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Floating IPs list showing a newly allocated IP address]
> ```

#### Via CLI:
```bash
# Allocate a floating IP from the public network
openstack floating ip create public

# List all floating IPs
openstack floating ip list
```

---

### Associate the Floating IP with Your Instance:

#### Via Horizon:
1. From the **Floating IPs** list, click **"Associate"** next to the new floating IP.
2. In the dialog:
   - **Port to be associated:** Select your instance `MyFirstVM`'s port
3. Click **"Associate"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Associate Floating IP dialog with MyFirstVM selected]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Instances list showing MyFirstVM with floating IP in the IP column]
> ```

#### Via CLI:
```bash
# Get the floating IP address
FLOAT_IP=$(openstack floating ip list -f value -c "Floating IP Address" | head -1)

# Associate with the instance
openstack server add floating ip MyFirstVM $FLOAT_IP

# Verify
openstack server show MyFirstVM | grep addresses
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CLI showing openstack server show with floating IP in addresses]
> ```

---

## Step 6: Test External Access via Floating IP

1. From your local machine or the OpenStack host, try to ping the floating IP:

```bash
ping -c 4 <FLOATING_IP>
```

2. Then SSH into the instance:
```bash
ssh -i ~/lab-keypair.pem ubuntu@<FLOATING_IP>
```

> ℹ️ **Note:** The default username depends on the image:
> - Ubuntu: `ubuntu`
> - CentOS: `centos`
> - Amazon Linux: `ec2-user`

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing successful ping to floating IP]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing successful SSH login to the OpenStack instance via floating IP]
> ```

3. Once logged in, verify internet connectivity from inside the instance:

```bash
ping -c 4 google.com
curl https://ifconfig.me   # Should show the floating IP
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Inside the instance — ping to google.com succeeds]
> ```

---

## Step 7: Disassociate and Release a Floating IP (Clean Up)

```bash
# Disassociate floating IP from the instance
openstack server remove floating ip MyFirstVM <FLOATING_IP>

# Release (delete) the floating IP
openstack floating ip delete <FLOATING_IP>
```

---

## Architecture Summary

```
 Internet / Host Network
         |
    [External Network: public]
         |
      [lab-router]
         |
    [private-net: 192.168.100.0/24]
         |
    [private-subnet: DHCP enabled]
         |
  [MyFirstVM: 192.168.100.x + Floating IP]
```

---

## Quick Reference: Neutron CLI Commands

```bash
# Network operations
openstack network list
openstack network create <name>
openstack network delete <name>

# Subnet operations
openstack subnet list
openstack subnet create --network <net> --subnet-range <cidr> <name>

# Router operations
openstack router list
openstack router create <name>
openstack router set <router> --external-gateway <network>
openstack router add subnet <router> <subnet>

# Floating IP operations
openstack floating ip create <network>
openstack floating ip list
openstack server add floating ip <server> <ip>
openstack server remove floating ip <server> <ip>
openstack floating ip delete <ip>
```

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Identify and use the external (public) network in OpenStack
- ✅ Create a private network and subnet with DHCP
- ✅ Create a router and connect it to both private and external networks
- ✅ Allocate and associate a floating IP with an instance
- ✅ Test external SSH and internet connectivity through the floating IP
