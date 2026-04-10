# Practical 10 — Cloud Security

---

## 📌 Objective

Implement cloud security best practices including IAM roles and policies with the principle of least privilege, data encryption for storage, and firewall rules — and test each configuration.

---

## 🧠 Conceptual Background (Know-How)

### The Shared Responsibility Model

This is the foundation of all cloud security:

```
┌─────────────────────────────────────────────────────────┐
│              Shared Responsibility Model                │
│                                                         │
│  ┌───────────────────────────────────────────────────┐  │
│  │            CUSTOMER Responsibility               │  │
│  │                                                  │  │
│  │  • Customer data                                 │  │
│  │  • Identity & Access Management (IAM)            │  │
│  │  • Application configuration                     │  │
│  │  • OS patches (for IaaS/EC2)                     │  │
│  │  • Network traffic protection                    │  │
│  │  • Firewall configuration                        │  │
│  │  • Encryption of data at rest and in transit     │  │
│  └───────────────────────────────────────────────────┘  │
│                                                         │
│  ┌───────────────────────────────────────────────────┐  │
│  │                AWS Responsibility                 │  │
│  │                                                  │  │
│  │  • Physical hardware                             │  │
│  │  • Data center security                          │  │
│  │  • Hypervisor                                    │  │
│  │  • Managed service patching (RDS, S3, etc.)      │  │
│  │  • Global infrastructure                         │  │
│  └───────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────┘
```

### IAM Core Concepts

```
┌──────────────────────────────────────────────────────────┐
│                    AWS IAM Structure                     │
│                                                          │
│    User                  Role                           │
│  (Long-term           (Short-term                       │
│   credentials)         credentials)                     │
│     │                     │                             │
│     └──────┬──────────────┘                             │
│            │                                            │
│         Policy ──── Permissions                         │
│         (JSON        (Allow/Deny                        │
│          doc)         on Resources)                     │
│                                                          │
│  Group → Contains multiple Users                        │
│  Role  → Assumed by Users, Services, or EC2 instances   │
└──────────────────────────────────────────────────────────┘
```

### Principle of Least Privilege

**"Grant only the minimum permissions required to perform a task"**

```
❌ BAD — Overly permissive:
   Effect: Allow
   Action: "*"
   Resource: "*"
   (This gives full admin access to EVERYTHING)

✅ GOOD — Least privilege:
   Effect: Allow
   Action: ["s3:GetObject", "s3:PutObject"]
   Resource: "arn:aws:s3:::my-bucket/*"
   (Only allows reading/writing to ONE specific bucket)
```

---

## 🛠️ Step-by-Step Guide

### Step A — Implement IAM Roles and Policies

#### Part 1: Create IAM Users Without Console Access (Programmatic Only)

```
AWS Console → IAM → Users → Create user
  User name:     s3-reader-user
  Access type:   ✅ Access key – Programmatic access
  (No console access)
→ Next
```

#### Part 2: Create Least-Privilege Policies

**Example 1: Read-Only S3 Policy for a Specific Bucket**

```
IAM → Policies → Create policy → JSON tab
```

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ListSpecificBucket",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "arn:aws:s3:::my-specific-bucket"
        },
        {
            "Sid": "ReadObjectsInBucket",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": "arn:aws:s3:::my-specific-bucket/*"
        }
    ]
}
```

```
Policy name:        S3ReadOnly-MyBucket
Description:        Read-only access to my-specific-bucket only
→ Create policy
```

**Example 2: EC2 Read-Only with Region Restriction**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "EC2ReadOnlyUsEast1",
            "Effect": "Allow",
            "Action": [
                "ec2:Describe*",
                "ec2:Get*",
                "ec2:List*"
            ],
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestedRegion": "us-east-1"
                }
            }
        }
    ]
}
```

**Example 3: Deny Expensive Instance Types**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyExpensiveInstances",
            "Effect": "Deny",
            "Action": "ec2:RunInstances",
            "Resource": "arn:aws:ec2:*:*:instance/*",
            "Condition": {
                "ForAnyValue:StringNotLike": {
                    "ec2:InstanceType": [
                        "t2.*",
                        "t3.*",
                        "t3a.*"
                    ]
                }
            }
        }
    ]
}
```

#### Part 3: Create an IAM Role for EC2

IAM Roles allow EC2 instances to access AWS services **without hardcoding credentials**.

```
IAM → Roles → Create role
  Trusted entity type:    AWS service
  Use case:              EC2
→ Next

Permissions:
  Search and add: AmazonS3ReadOnlyAccess
→ Next

Role details:
  Role name:     EC2-S3-ReadOnly-Role
  Description:   Allows EC2 instances to read from S3
→ Create role
```

**Attach the role to an EC2 instance:**
```
EC2 → Instances → Select instance → Actions →
Security → Modify IAM role → Select EC2-S3-ReadOnly-Role →
Update IAM role
```

**Test from inside the instance:**
```bash
# SSH into the instance
ssh -i keypair.pem ec2-user@<ip>

# Test S3 access (should work — role grants permission)
aws s3 ls s3://my-specific-bucket/

# Test creating a bucket (should fail — no permission)
aws s3 mb s3://new-bucket-test
# Error: An error occurred (AccessDenied) ...
```

---

### Step B — Create and Assign Least-Privilege Roles to Users

#### Create IAM User Groups with Specific Permissions

```
IAM → User groups → Create group
  Group name:   developers
  
  Attach policies:
    - EC2ReadOnlyAccess (AWS managed)
    - S3ReadOnly-MyBucket (custom policy created above)
→ Create group
```

```
IAM → User groups → Create group
  Group name:   data-engineers
  
  Attach policies:
    - AmazonS3FullAccess (scoped by condition)
    - AmazonRDSReadOnlyAccess
→ Create group
```

**Add Users to Groups:**
```
IAM → Users → Select user → Groups tab → Add user to groups →
Select "developers" → Add to groups
```

#### IAM Policy Testing with IAM Policy Simulator

```
AWS Console → IAM → Policy Simulator (https://policysim.aws.amazon.com/)

1. Select a user or role
2. Select service (e.g., S3)
3. Select actions (e.g., PutObject)
4. Click "Run Simulation"
→ See if the action is Allowed or Denied and why
```

#### MFA Enforcement Policy (Best Practice)

Force users to enable MFA before accessing any resource:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowViewAccountInfo",
            "Effect": "Allow",
            "Action": [
                "iam:GetAccountPasswordPolicy",
                "iam:ListVirtualMFADevices"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowManageOwnMFA",
            "Effect": "Allow",
            "Action": [
                "iam:CreateVirtualMFADevice",
                "iam:EnableMFADevice",
                "iam:ListMFADevices"
            ],
            "Resource": [
                "arn:aws:iam::*:mfa/${aws:username}",
                "arn:aws:iam::*:user/${aws:username}"
            ]
        },
        {
            "Sid": "DenyAllExceptListedIfNoMFA",
            "Effect": "Deny",
            "NotAction": [
                "iam:CreateVirtualMFADevice",
                "iam:EnableMFADevice",
                "iam:GetUser",
                "iam:ListMFADevices",
                "sts:GetSessionToken"
            ],
            "Resource": "*",
            "Condition": {
                "BoolIfExists": {
                    "aws:MultiFactorAuthPresent": "false"
                }
            }
        }
    ]
}
```

---

### Step C — Configure Data Encryption for S3

#### Enable Default Encryption on an S3 Bucket

```
S3 → Select bucket → Properties tab → Default encryption → Edit
  Server-side encryption: Enable
  Encryption type: SSE-S3 (Amazon S3-managed keys) [free]
  OR
  Encryption type: SSE-KMS (AWS KMS-managed keys) [more control]
  AWS KMS key: aws/s3 (default AWS-managed key)
→ Save changes
```

Now **every object uploaded to this bucket is automatically encrypted at rest**.

#### Enforce Encryption via Bucket Policy (Deny Unencrypted Uploads)

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyUnencryptedObjectUploads",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::my-secure-bucket/*",
            "Condition": {
                "StringNotEquals": {
                    "s3:x-amz-server-side-encryption": "AES256"
                }
            }
        },
        {
            "Sid": "DenyNonSSLRequests",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::my-secure-bucket",
                "arn:aws:s3:::my-secure-bucket/*"
            ],
            "Condition": {
                "Bool": {
                    "aws:SecureTransport": "false"
                }
            }
        }
    ]
}
```

**Test the Encryption Enforcement:**
```bash
# Upload without encryption header (should fail with our policy)
aws s3 cp testfile.txt s3://my-secure-bucket/ 
# Error: AccessDenied (no encryption specified)

# Upload with encryption header (should succeed)
aws s3 cp testfile.txt s3://my-secure-bucket/ \
  --sse AES256
# Success: upload: testfile.txt to s3://my-secure-bucket/testfile.txt

# Verify object is encrypted
aws s3api head-object \
  --bucket my-secure-bucket \
  --key testfile.txt
# Output shows: "ServerSideEncryption": "AES256"
```

#### Enable EBS Volume Encryption (EC2 Disk Encryption)

**For new volumes:**
```
EC2 → Volumes → Create volume →
  ✅ Encrypted
  KMS key: (default) aws/ebs
```

**Enforce EBS encryption by default for the account:**
```
EC2 → Account Attributes → EBS encryption → Manage →
  ✅ Enable — Always encrypt new EBS volumes
→ Update EBS encryption
```

---

### Step D — Set Up a Firewall Rule and Test Functionality

#### Security Group Rules (Instance-Level Firewall)

**Create a restrictive web server security group:**

```
EC2 → Security Groups → Create security group

Name:         webserver-strict-sg
Description:  Strict rules for production web server
VPC:          your-vpc

Inbound rules:
  HTTP    | TCP | 80   | 0.0.0.0/0     | Allow public HTTP
  HTTPS   | TCP | 443  | 0.0.0.0/0     | Allow public HTTPS  
  SSH     | TCP | 22   | 203.0.113.0/24| Allow SSH from office IP range only
  
Outbound rules:
  HTTP    | TCP | 80   | 0.0.0.0/0     | Allow outbound HTTP (updates)
  HTTPS   | TCP | 443  | 0.0.0.0/0     | Allow outbound HTTPS
  DNS     | UDP | 53   | 0.0.0.0/0     | Allow DNS
  (Remove the default "All traffic" outbound rule)

→ Create security group
```

**Test the Security Group:**
```bash
# Test HTTP access (should work if web server running)
curl http://<ec2-public-ip>

# Test SSH from your IP (should work)
ssh -i keypair.pem ec2-user@<ec2-public-ip>

# Test a port that's NOT in the rules (should timeout)
nc -zv <ec2-public-ip> 8080
# Connection timed out (blocked by security group)

# Test from unauthorized IP for SSH (should be blocked)
# From a different IP than what's in the rule:
ssh ec2-user@<ec2-public-ip>
# ssh: connect to host X.X.X.X port 22: Connection timed out
```

#### Network ACL Rules (Subnet-Level Firewall)

```
VPC → Network ACLs → Create network ACL
  Name:  webserver-nacl
  VPC:   your-vpc
→ Create

Inbound rules (add):
  Rule 100 | HTTP  | TCP | 80  | 0.0.0.0/0 | ALLOW
  Rule 110 | HTTPS | TCP | 443 | 0.0.0.0/0 | ALLOW
  Rule 120 | SSH   | TCP | 22  | 203.0.113.0/24 | ALLOW
  Rule 130 | Custom | TCP | 1024-65535 | 0.0.0.0/0 | ALLOW (return traffic!)
  Rule *   | All traffic | | | DENY (default)

Outbound rules (add):
  Rule 100 | All traffic | All | All | 0.0.0.0/0 | ALLOW
  Rule *   | All traffic | | | DENY (default)

Associate with subnet:
  NACL → Subnet associations → Edit → Select your public subnet
```

> ⚠️ **NACL is stateless** — you must explicitly allow return traffic (ephemeral ports 1024-65535). Security Groups are stateful — they automatically allow return traffic.

---

## 📊 Security Layers Summary

```
┌────────────────────────────────────────────────────────┐
│              AWS Security Defense in Depth             │
│                                                        │
│  Layer 1: AWS Account Security                         │
│   → MFA on root, IAM users/roles, CloudTrail           │
│                                                        │
│  Layer 2: Network Security                             │
│   → VPC, Network ACLs, Security Groups                 │
│                                                        │
│  Layer 3: Host Security                                │
│   → OS patches, host-based firewall (iptables)         │
│                                                        │
│  Layer 4: Application Security                         │
│   → WAF (Web Application Firewall), input validation   │
│                                                        │
│  Layer 5: Data Security                                │
│   → Encryption at rest (S3 SSE, EBS encryption)        │
│   → Encryption in transit (TLS/SSL)                    │
│                                                        │
│  Layer 6: Detection & Response                         │
│   → CloudWatch Alarms, GuardDuty, CloudTrail           │
└────────────────────────────────────────────────────────┘
```

---

## 🔐 IAM Best Practices Checklist

```
Root Account:
  ✅ Enable MFA on root account
  ✅ Never use root for daily tasks
  ✅ Delete root access keys

IAM Users:
  ✅ Create individual IAM users (no shared accounts)
  ✅ Use groups to assign permissions
  ✅ Enforce MFA for all users
  ✅ Rotate access keys every 90 days

Permissions:
  ✅ Apply least privilege principle
  ✅ Use IAM roles instead of access keys for EC2
  ✅ Review and remove unused permissions (use IAM Access Analyzer)
  ✅ Use permission boundaries for delegated admins

Monitoring:
  ✅ Enable CloudTrail in all regions
  ✅ Set up Config Rules for compliance
  ✅ Enable GuardDuty for threat detection
```

---

## ✅ Learning Outcomes

After completing this practical, you should be able to:

- [ ] Explain the Shared Responsibility Model
- [ ] Create IAM users, groups, roles, and policies
- [ ] Write custom IAM policies using JSON
- [ ] Apply the principle of least privilege
- [ ] Enforce MFA using IAM policies
- [ ] Configure S3 bucket encryption (SSE-S3 and SSE-KMS)
- [ ] Create and apply bucket policies to enforce encryption and HTTPS
- [ ] Configure Security Groups with restrictive rules
- [ ] Understand the difference between Security Groups and NACLs
- [ ] Test firewall rules and verify access is correctly blocked/allowed

---

## 📚 Further Reading

- [AWS IAM User Guide](https://docs.aws.amazon.com/iam/latest/userguide/)
- [AWS Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [S3 Security Best Practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)
- [AWS GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)
- [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html)
- [OWASP Cloud Security Guidelines](https://owasp.org/www-project-cloud-security/)
