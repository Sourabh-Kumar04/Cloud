# Practical 6: Monitor Resources Using AWS CloudWatch

**Objective:** Use AWS CloudWatch to monitor EC2 metrics, create alarms, configure SNS email notifications, and test the setup by simulating high CPU usage.

---

## Prerequisites
- An active AWS account
- A running EC2 instance (refer to Practical 2)
- A valid email address to receive SNS notifications

---

## Background Concepts

| Term | Description |
|---|---|
| CloudWatch | AWS monitoring and observability service |
| Metric | A time-series data point (e.g., CPU utilization %) sent by AWS services |
| Alarm | A CloudWatch rule that triggers an action when a metric crosses a threshold |
| SNS | Simple Notification Service — sends email/SMS/push notifications |
| SNS Topic | A communication channel to which subscribers (email, Lambda, etc.) listen |

---

## Step 1: Ensure You Have a Running EC2 Instance

1. Go to **EC2 → Instances**.
2. Confirm at least one instance is in the **Running** state.
3. Note the **Instance ID** (e.g., `i-0abc12345def67890`).

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/8549d407-9ba6-47d4-8766-f2a8ffab3af0" />


---

## Step 2: Enable Detailed Monitoring on EC2 (Optional but Recommended)

By default, EC2 sends metrics to CloudWatch every **5 minutes** (Basic Monitoring). You can enable **Detailed Monitoring** (1-minute intervals) for faster detection.

1. Select your EC2 instance.
2. Click **"Actions"** → **"Monitor and troubleshoot"** → **"Manage detailed monitoring"**.
3. Check **"Enable"** and click **"Save"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/1c1a8eaa-30e4-45ea-a634-b6532c954df7" />


---

## Step 3: View EC2 Metrics in CloudWatch

1. Search for **CloudWatch** in the AWS Console and open it.
2. In the left panel, click **"Metrics"** → **"All metrics"**.
3. Under **"AWS namespaces"**, click **"EC2"**.
4. Click **"Per-Instance Metrics"**.
5. Search for your instance ID or look for **CPUUtilization**.
6. Check the box next to your instance's **CPUUtilization** metric.
7. A graph will appear showing CPU usage over time.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/a8689f1a-cc64-4d8c-abb9-c5afc943b390" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/3da1e9e1-5d28-4b98-9212-579961131bc3" />

---

## Step 4: Create an SNS Topic for Email Notifications

Before creating an alarm, set up the email notification channel.

1. In the AWS Console, search for **SNS** (Simple Notification Service).
2. In the left panel, click **"Topics"**.
3. Click **"Create topic"**.
4. Fill in:
   - **Type:** Standard
   - **Name:** `EC2HighCPUAlert`
   - **Display name:** `EC2 CPU Alert`
5. Click **"Create topic"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/8fa3a9ca-282a-404b-97b4-b4ba85c81560" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/a054c3b8-4b4a-4c44-a78a-c45a0c9d2947" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/16faf03a-4451-4999-b63f-6bde9153eb08" />



---

## Step 5: Subscribe an Email Address to the SNS Topic

1. On the **EC2HighCPUAlert** topic page, click **"Create subscription"**.
2. Fill in:
   - **Topic ARN:** *(auto-filled)*
   - **Protocol:** Email
   - **Endpoint:** Your email address (e.g., `student@example.com`)
3. Click **"Create subscription"**.
4. Check your inbox — you will receive a **"Subscription Confirmation"** email from AWS.
5. Click the **"Confirm subscription"** link in that email.
6. The subscription status in AWS will change from **Pending confirmation** to **Confirmed**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/f0080541-4174-41b6-a818-dd19ad4429fd" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/28a5c6b5-3074-4c1c-b356-00b507b9f12d" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/ad190af0-e141-435b-83ef-063196c8d9e0" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/95e95051-6b6d-46d7-ada3-70c93c3dcaf8" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/6ba18fe2-e7f6-4789-9c78-2b7e93fec638" />


---

## Step 6: Create a CloudWatch Alarm

1. Return to **CloudWatch**.
2. In the left panel, click **"Alarms"** → **"All alarms"**.
3. Click **"Create alarm"**.
4. Click **"Select metric"**.
5. Navigate to **EC2 → Per-Instance Metrics**.
6. Find and select **CPUUtilization** for your instance.
7. Click **"Select metric"**.

### Configure the Alarm Conditions
8. Under **"Conditions"**:
   - **Threshold type:** Static
   - **Whenever CPUUtilization is:** Greater than
   - **than:** `70` (percent)
9. Under **"Additional configuration"**:
   - **Datapoints to alarm:** `2 out of 2` *(alarm triggers only if threshold is exceeded for 2 consecutive periods)*
   - **Missing data treatment:** Treat missing data as missing
10. Click **"Next"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/4740a716-97d8-4c28-bbd0-d3bca1d2ebd4" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/e7ddd2f8-26bb-4530-ad23-cbd100258660" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/279f66f1-4a62-49a1-b7bb-a7c51f872b06" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/1d461d60-085b-4ac2-b950-e6724dde2687" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/f060a4de-bd1a-4ff0-8d63-1cc8fe9b21a1" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/5bd67c42-c785-4cb4-b19f-45b246db30b6" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/6ce5207b-ecb8-4908-ab25-91871414a2fb" />


### Configure Notifications
11. Under **"Notification"**:
    - **Alarm state trigger:** In alarm
    - **Select an SNS topic:** Choose **"Select an existing SNS topic"** → select `EC2HighCPUAlert`
12. Click **"Next"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/425e1a10-0dfb-4e4f-9fd2-3566bb6f8010" />


### Name the Alarm
13. **Alarm name:** `EC2-HighCPU-Alarm`
14. **Description:** `Triggers when EC2 CPU exceeds 70% for 2 consecutive periods`
15. Click **"Next"** → **"Create alarm"**.

> 📸 **Screenshot Placeholder**
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/06508f06-2ed6-423d-920b-2c81b8f115e6" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/d701a74f-50a8-4963-9d80-9a39e04c7b7e" />
> <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/b70a81bf-cbd6-4452-85f6-26aad9982246" />


---
## Step 7: Test the Setup — Simulate High CPU Usage

SSH into your EC2 instance and generate CPU load.

### Install the stress utility
```bash
# For Amazon Linux
sudo amazon-linux-extras install epel -y
sudo yum install stress -y

# For Ubuntu
sudo apt-get update
sudo apt-get install stress -y
```

### Run the stress test
```bash
# Stress all CPUs for 5 minutes (300 seconds)
stress --cpu $(nproc) --timeout 300
```

This will spike CPU utilization to near 100%.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Terminal showing stress command running with CPU spiking]
> ```

---

## Step 8: Watch the Alarm Trigger

1. Go to **CloudWatch → Alarms**.
2. Within a few minutes (depending on monitoring interval), the alarm state will change:
   - **OK** → **In alarm**
3. You should receive an **email notification** from AWS SNS.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CloudWatch Alarm showing "In alarm" (red) state]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Email inbox showing alarm notification from AWS Notifications]
> ```

4. After the stress test ends (or you stop it with `Ctrl+C`), the CPU will drop and the alarm will return to **OK** state.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CloudWatch Alarm graph showing CPU spike followed by return to normal]
> ```

---

## Step 9: Explore CloudWatch Dashboard (Bonus)

1. In CloudWatch, click **"Dashboards"** in the left panel.
2. Click **"Create dashboard"** → name it `EC2Monitor`.
3. Click **"Add widget"** → choose **"Line"** → select the **CPUUtilization** metric for your instance.
4. Click **"Create widget"** → **"Save dashboard"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CloudWatch Dashboard showing EC2 CPU utilization widget]
> ```

---

## Summary of What Was Configured

```
EC2 Instance
     |
     | (sends metrics every 1–5 minutes)
     ↓
CloudWatch Metrics (CPUUtilization)
     |
     | (evaluated against threshold)
     ↓
CloudWatch Alarm (CPU > 70%)
     |
     | (when In Alarm state)
     ↓
SNS Topic (EC2HighCPUAlert)
     |
     | (notifies subscribers)
     ↓
Email Notification → student@example.com
```

---

## Clean Up (Optional)
1. Delete the **CloudWatch Alarm**.
2. Delete the **SNS subscription** and **topic**.
3. Terminate the EC2 instance if no longer needed.

---

## Outcome
By the end of this practical, you should be able to:
- ✅ View real-time EC2 CPU metrics in CloudWatch
- ✅ Create an SNS topic and subscribe an email address to it
- ✅ Configure a CloudWatch Alarm with a threshold-based trigger
- ✅ Simulate high CPU usage and observe the alarm state change
- ✅ Receive email notifications when the alarm is triggered
