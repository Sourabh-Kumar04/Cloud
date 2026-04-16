# Practical 10: Cloud Security

**Objective:** Understand and implement core cloud security practices including IAM roles and policies, least-privilege access, data encryption for storage, and firewall rule configuration and testing.

---

## Prerequisites
- An active AWS account with admin access
- An existing S3 bucket (from Practical 5, or create a new one)
- A running EC2 instance (from Practical 2 or 4)
- Familiarity with the AWS Console

---

## Background Concepts

| Term | Description |
|---|---|
| IAM | Identity and Access Management — controls who can do what in AWS |
| IAM User | A person or application that interacts with AWS services |
| IAM Role | A set of permissions that can be assumed by users, services, or applications |
| IAM Policy | A JSON document that specifies allowed or denied actions on resources |
| Least Privilege | The principle of granting only the minimum permissions needed |
| S3 SSE | Server-Side Encryption on S3 buckets |
| Security Group | A virtual firewall for EC2 instances |
| NACL | Network Access Control List — subnet-level firewall in VPC |

---

---

# Part A: Implement IAM Roles and Policies

---

## Step 1: Create an IAM Policy (Least-Privilege)

We'll create a policy that allows a user to **only read objects from a specific S3 bucket**.

1. Log in to the **AWS Management Console**.
2. Search for **IAM** and open it.
3. In the left panel, click **"Policies"** → **"Create policy"**.
4. Click the **"JSON"** tab.
5. Paste the following policy (replace `YOUR-BUCKET-NAME` with your actual bucket):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadOnly",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::YOUR-BUCKET-NAME",
        "arn:aws:s3:::YOUR-BUCKET-NAME/*"
      ]
    }
  ]
}
```

6. Click **"Next"** → **"Next"**.
7. Enter:
   - **Policy name:** `S3ReadOnlyPolicy`
   - **Description:** `Allows read-only access to a specific S3 bucket`
8. Click **"Create policy"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: IAM Create policy JSON editor with the S3 read-only policy]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Policy S3ReadOnlyPolicy created successfully in the policies list]
> ```

---

## Step 2: Create IAM Users with Least-Privilege Roles

### Create a User:
1. In IAM, click **"Users"** → **"Create user"**.
2. Fill in:
   - **User name:** `dev-student1`
3. Check **"Provide user access to the AWS Management Console"** (optional for web access).
4. Set a **custom password** or auto-generate.
5. Click **"Next"**.

### Attach the Least-Privilege Policy:
6. Under **"Set permissions"**, select **"Attach policies directly"**.
7. Search for `S3ReadOnlyPolicy` and check it.
8. Click **"Next"** → **"Create user"**.
9. Save the console login link and credentials.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: IAM Create user form with user name dev-student1]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Permissions step showing S3ReadOnlyPolicy attached to the user]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: User dev-student1 created successfully with login credentials shown]
> ```

---

## Step 3: Test the Least-Privilege User

1. Open a new **incognito/private browser window**.
2. Log in to the AWS Console using `dev-student1` credentials.
3. Try to **access S3** → browse to `YOUR-BUCKET-NAME` → should work (read allowed).
4. Try to **delete** an object from S3 — it should fail with an **"Access Denied"** message.
5. Try to **open EC2** — should be denied or show no resources.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: dev-student1 successfully listing objects in the S3 bucket]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Access Denied error when dev-student1 tries to delete an S3 object]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Access Denied when dev-student1 tries to access EC2]
> ```

---

## Step 4: Create an IAM Role for EC2

An **IAM Role** for EC2 allows an instance to securely access AWS services without hardcoding credentials.

1. In IAM, click **"Roles"** → **"Create role"**.
2. Under **"Trusted entity type"**, select **"AWS service"**.
3. Under **"Use case"**, select **EC2**.
4. Click **"Next"**.
5. Search for and attach `AmazonS3ReadOnlyAccess` policy.
6. Click **"Next"**.
7. Enter:
   - **Role name:** `EC2-S3-ReadOnly-Role`
   - **Description:** `Allows EC2 to read from S3`
8. Click **"Create role"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: IAM Create role — trusted entity = EC2, policy = AmazonS3ReadOnlyAccess]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Role EC2-S3-ReadOnly-Role created successfully]
> ```

### Attach the Role to an EC2 Instance:
1. Go to **EC2 → Instances**, select your instance.
2. Click **"Actions"** → **"Security"** → **"Modify IAM role"**.
3. Select `EC2-S3-ReadOnly-Role`.
4. Click **"Update IAM role"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Modify IAM role dialog with EC2-S3-ReadOnly-Role selected]
> ```

### Test the Role from Inside the Instance:
SSH into the EC2 instance and run:
```bash
# Install AWS CLI if not already present
sudo yum install -y awscli

# Test S3 access using the attached role (no credentials needed)
aws s3 ls s3://YOUR-BUCKET-NAME

# Try to write (should fail — role is read-only)
echo "test" > /tmp/test.txt
aws s3 cp /tmp/test.txt s3://YOUR-BUCKET-NAME/test.txt
```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: EC2 terminal showing successful s3 ls and failed s3 cp (access denied)]
> ```

---

---

# Part B: Configure Data Encryption for S3

---

## Step 5: Enable Server-Side Encryption on an S3 Bucket

1. Go to **S3** and open your bucket.
2. Click the **"Properties"** tab.
3. Scroll to **"Default encryption"** → click **"Edit"**.
4. Under **"Encryption type"**, select:
   - **Server-side encryption with Amazon S3 managed keys (SSE-S3)**
5. Under **"Bucket Key"**: select **Enable** (reduces encryption costs).
6. Click **"Save changes"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: S3 Default Encryption settings with SSE-S3 enabled]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Encryption settings saved successfully]
> ```

7. Upload a new file to the bucket.
8. Click on the file → go to **"Properties"** → scroll to **"Server-side encryption"** — it should show **"SSE-S3"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: S3 object properties showing Server-side encryption as SSE-S3]
> ```

---

## Step 6: Enable KMS Encryption (AWS-Managed Keys)

For stronger control, you can use **KMS (Key Management Service)** keys.

1. Go to S3 bucket → **Properties** → **Default encryption** → **Edit**.
2. Select **"Server-side encryption with AWS Key Management Service keys (SSE-KMS)"**.
3. Under **AWS KMS key**, select **"AWS managed key (aws/s3)"**.
4. Click **"Save changes"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: S3 encryption settings with SSE-KMS and AWS managed key selected]
> ```

---

---

# Part C: Configure a Firewall Rule and Test

---

## Step 7: Modify EC2 Security Group Rules

Security groups act as instance-level firewalls.

### Add a New Rule (Allow HTTP on port 80):
1. Go to **EC2 → Security Groups**.
2. Click on the security group attached to your instance (e.g., `my-ec2-sg`).
3. Click the **"Inbound rules"** tab → **"Edit inbound rules"**.
4. Click **"Add rule"**:
   - **Type:** HTTP
   - **Protocol:** TCP
   - **Port range:** 80
   - **Source:** Anywhere-IPv4 (`0.0.0.0/0`)
5. Click **"Save rules"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Inbound rules editor showing SSH (22) and HTTP (80) rules]
> ```

### Test HTTP Access:
1. SSH into your EC2 instance.
2. Install and start Apache:
```bash
sudo yum install -y httpd
sudo systemctl start httpd
echo "<h1>Firewall Test - Port 80 Open</h1>" | sudo tee /var/www/html/index.html
```
3. Open a browser and visit `http://<YOUR_INSTANCE_PUBLIC_IP>`.
4. You should see the test page.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Browser showing "Firewall Test - Port 80 Open" page served from EC2]
> ```

---

## Step 8: Block HTTP Access (Test Firewall Rule)

1. Go back to **Security Groups → Inbound rules → Edit inbound rules**.
2. Delete the **HTTP (port 80)** rule.
3. Click **"Save rules"**.

4. Refresh the browser — the page should now **time out** or show "This site can't be reached".

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Inbound rules after HTTP rule deleted — only SSH remains]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Browser showing connection timeout after HTTP rule removed]
> ```

---

## Step 9: Add a Network ACL Rule (Subnet-Level Firewall)

Network ACLs operate at the **subnet level** (in addition to security groups).

1. Go to **VPC → Network ACLs**.
2. Select the ACL associated with your public subnet.
3. Click **"Inbound rules"** tab → **"Edit inbound rules"**.
4. Add a rule to **DENY** a specific IP (simulate blocking an attacker):
   - **Rule number:** `50` *(lower numbers have higher priority)*
   - **Type:** All traffic
   - **Source:** `203.0.113.0/24` *(test IP; use a real IP if you know one to block)*
   - **Allow/Deny:** **DENY**
5. Click **"Save changes"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Network ACL inbound rules showing a DENY rule added]
> ```

> ℹ️ **Note:** NACLs are **stateless** — you must also add outbound rules if needed. Security Groups are **stateful** — a return traffic is automatically allowed.

---

## Step 10: Enable CloudTrail for Security Auditing (Bonus)

**CloudTrail** records all API calls in your AWS account — essential for security auditing.

1. Search for **CloudTrail** in the AWS Console.
2. Click **"Create trail"**.
3. Fill in:
   - **Trail name:** `SecurityAuditTrail`
   - **Storage location:** Create a new S3 bucket
   - **Log file SSE-KMS encryption:** Enabled (optional)
4. Under **"Events"**:
   - **Management events:** Read and Write
5. Click **"Create trail"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CloudTrail Create trail form with SecurityAuditTrail name]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CloudTrail trail created and showing "Logging" status]
> ```

---

## Security Best Practices Summary

| Practice | Implementation |
|---|---|
| Least Privilege | Create narrow IAM policies with only required permissions |
| Use IAM Roles over Users | Attach roles to EC2/Lambda instead of hardcoding credentials |
| Enable MFA | Require MFA for console login (IAM → Users → Security credentials) |
| S3 Encryption | Enable SSE-S3 or SSE-KMS as default encryption |
| Block Public Access | Keep S3 Block Public Access enabled unless explicitly needed |
| Security Groups | Restrict inbound rules to specific IPs and ports only |
| Network ACLs | Add subnet-level DENY rules for known bad actors |
| CloudTrail | Enable audit logging for all management events |

---

## Clean Up
1. Delete the IAM user `dev-student1`.
2. Detach and delete the IAM role `EC2-S3-ReadOnly-Role`.
3. Delete the IAM policy `S3ReadOnlyPolicy`.
4. Revert security group rules as needed.
5. Delete the CloudTrail trail if not needed.

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Create a least-privilege IAM policy and attach it to a user
- ✅ Create and assign an IAM role to an EC2 instance
- ✅ Test access controls to confirm the least-privilege principle is enforced
- ✅ Enable server-side encryption on an S3 bucket
- ✅ Configure and test EC2 Security Group and Network ACL firewall rules
- ✅ Enable CloudTrail for security auditing
