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
> ```
> [Screenshot: EC2 Instances list showing a running instance with its Instance ID]
> ```

---

## Step 2: Enable Detailed Monitoring on EC2 (Optional but Recommended)

By default, EC2 sends metrics to CloudWatch every **5 minutes** (Basic Monitoring). You can enable **Detailed Monitoring** (1-minute intervals) for faster detection.

1. Select your EC2 instance.
2. Click **"Actions"** → **"Monitor and troubleshoot"** → **"Manage detailed monitoring"**.
3. Check **"Enable"** and click **"Save"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Detailed monitoring dialog with "Enable" checked]
> ```

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
> ```
> [Screenshot: CloudWatch Metrics browser showing CPUUtilization selected for the EC2 instance]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CloudWatch graph displaying CPU utilization over time]
> ```

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
> ```
> [Screenshot: SNS Create topic form with name EC2HighCPUAlert]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: SNS topic created successfully with Topic ARN shown]
> ```

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
> ```
> [Screenshot: SNS Create subscription form with Protocol=Email and endpoint filled]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Confirmation email received in inbox with "Confirm subscription" link]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Subscription status showing "Confirmed"]
> ```

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
> ```
> [Screenshot: Alarm condition configuration showing CPU > 70% threshold]
> ```

### Configure Notifications
11. Under **"Notification"**:
    - **Alarm state trigger:** In alarm
    - **Select an SNS topic:** Choose **"Select an existing SNS topic"** → select `EC2HighCPUAlert`
12. Click **"Next"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Alarm notification configuration with EC2HighCPUAlert SNS topic selected]
> ```

### Name the Alarm
13. **Alarm name:** `EC2-HighCPU-Alarm`
14. **Description:** `Triggers when EC2 CPU exceeds 70% for 2 consecutive periods`
15. Click **"Next"** → **"Create alarm"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Alarm preview summary before creation]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: CloudWatch Alarms list showing EC2-HighCPU-Alarm with "OK" state]
> ```

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
