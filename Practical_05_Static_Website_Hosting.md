# Practical 5: Deploying a Static Website on the Cloud

**Objective:** Host a static website using cloud storage services on AWS S3, Azure Blob Storage, or GCP Cloud Storage.

---

## Prerequisites
- An active account on AWS, Azure, or GCP
- A simple static website (HTML/CSS files) ready to upload
- Basic familiarity with cloud console navigation

---

## Sample Website Files

Create a folder called `my-website` on your computer with the following files:

**`index.html`**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My Cloud Website</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Welcome to My Cloud-Hosted Website! ☁️</h1>
  <p>This page is hosted on cloud storage.</p>
</body>
</html>
```

**`style.css`**
```css
body {
  font-family: Arial, sans-serif;
  background-color: #f0f4f8;
  text-align: center;
  padding: 50px;
}
h1 { color: #2c3e50; }
p  { color: #555; }
```

---

---

# Option A: AWS S3 Static Website Hosting

---

## Step 1: Create an S3 Bucket

1. Log in to the **AWS Management Console**.
2. Search for **S3** and open the S3 service.
3. Click **"Create bucket"**.
4. Fill in:
   - **Bucket name:** `my-static-website-<yourname>` *(must be globally unique)*
   - **AWS Region:** Select a region close to you (e.g., `us-east-1`)
5. Under **"Object Ownership":** select **ACLs disabled (recommended)**.
6. Under **"Block Public Access settings for this bucket":**
   - **Uncheck** the box **"Block all public access"**.
   - Check the acknowledgment box that appears.
7. Leave all other settings as default.
8. Click **"Create bucket"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: S3 bucket creation form with name filled and Block Public Access unchecked]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: S3 bucket successfully created in the buckets list]
> ```

---

## Step 2: Upload Website Files

1. Click on your newly created bucket.
2. Click **"Upload"**.
3. Click **"Add files"** and select `index.html` and `style.css` from your computer.
4. Leave all settings as default.
5. Click **"Upload"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: S3 Upload page showing index.html and style.css ready to upload]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Upload complete — both files listed in the bucket]
> ```

---

## Step 3: Enable Static Website Hosting

1. Go to the **"Properties"** tab of your bucket.
2. Scroll down to **"Static website hosting"** → click **"Edit"**.
3. Select **"Enable"**.
4. Set:
   - **Hosting type:** Host a static website
   - **Index document:** `index.html`
   - **Error document:** `error.html` *(optional)*
5. Click **"Save changes"**.
6. Note the **Bucket website endpoint** URL (e.g., `http://my-static-website.s3-website-us-east-1.amazonaws.com`).

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Static website hosting configuration with index.html set as index document]
> ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Static website hosting enabled, showing the endpoint URL]
> ```

---

## Step 4: Add a Bucket Policy for Public Read Access

1. Go to the **"Permissions"** tab of your bucket.
2. Scroll to **"Bucket policy"** → click **"Edit"**.
3. Paste the following policy (replace `YOUR-BUCKET-NAME` with your actual bucket name):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}
```

4. Click **"Save changes"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Bucket policy editor with the public read policy pasted]
> ```

---

## Step 5: Test the Website

1. Copy the **Bucket website endpoint** from the Properties tab.
2. Open it in your browser.
3. You should see your static website with the heading and paragraph.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Browser showing the static website hosted on S3]
> ```

---

---

# Option B: Azure Blob Storage Static Website

---

## Step 1: Create a Storage Account

1. Log in to the **Azure Portal** at [https://portal.azure.com](https://portal.azure.com).
2. Click **"Create a resource"** → search for **"Storage account"**.
3. Click **"Create"**.
4. Fill in:
   - **Resource group:** Create new → `MyStaticWebRG`
   - **Storage account name:** `mystaticwebsite<yourname>` *(must be lowercase, 3–24 chars, globally unique)*
   - **Region:** East US (or nearest region)
   - **Performance:** Standard
   - **Redundancy:** LRS (Locally Redundant Storage)
5. Click **"Review + create"** → **"Create"**.
6. Wait for deployment to complete, then click **"Go to resource"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Azure Storage Account creation form]
> ```

---

## Step 2: Enable Static Website Hosting

1. In the storage account, go to **"Data management"** → **"Static website"** in the left menu.
2. Click **"Enabled"**.
3. Set:
   - **Index document name:** `index.html`
   - **Error document path:** `404.html` *(optional)*
4. Click **"Save"**.
5. Note the **Primary endpoint** URL (e.g., `https://mystaticwebsite.z13.web.core.windows.net/`).

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Azure Static website setting with index.html set, showing Primary endpoint]
> ```

---

## Step 3: Upload Files to the $web Container

1. Go to **"Data storage"** → **"Containers"** in the left menu.
2. Click on the **`$web`** container (automatically created when static hosting is enabled).
3. Click **"Upload"**.
4. Upload `index.html` and `style.css`.
5. Click **"Upload"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: $web container with index.html and style.css files uploaded]
> ```

---

## Step 4: Test the Website

1. Navigate to the **Primary endpoint** URL.
2. Your static website should be visible in the browser.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Browser showing the website served from Azure Blob Storage]
> ```

---

---

# Option C: GCP Cloud Storage Static Website

---

## Step 1: Create a Cloud Storage Bucket

1. Log in to [https://console.cloud.google.com](https://console.cloud.google.com).
2. Navigate to **Cloud Storage → Buckets**.
3. Click **"Create"**.
4. Fill in:
   - **Name:** `my-static-website-<yourname>` *(globally unique)*
   - **Location type:** Region → select nearest region
   - **Storage class:** Standard
   - **Access control:** Uniform
5. Click **"Create"**.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: GCP Cloud Storage bucket creation form]
> ```

---

## Step 2: Upload Files

1. Open your bucket.
2. Click **"Upload files"** → select `index.html` and `style.css`.
3. Wait for the upload to complete.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: GCP bucket showing uploaded website files]
> ```

---

## Step 3: Make Bucket Publicly Accessible

1. Click the **"Permissions"** tab.
2. Click **"Grant Access"**.
3. Fill in:
   - **New principals:** `allUsers`
   - **Role:** `Storage Object Viewer`
4. Click **"Save"** → confirm the warning dialog.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: GCP bucket permissions with allUsers set as Storage Object Viewer]
> ```

---

## Step 4: Set Website Configuration

1. In **Cloud Shell** (click the terminal icon at the top of GCP Console), run:
   ```bash
   gsutil web set -m index.html gs://my-static-website-<yourname>
   ```
2. The website URL will be:
   ```
   https://storage.googleapis.com/my-static-website-<yourname>/index.html
   ```

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Cloud Shell showing gsutil web set command executed successfully]
> ```

---

## Step 5: Test the Website

1. Open the URL in your browser.
2. Your static website should load correctly.

> 📸 **Screenshot Placeholder**
> ```
> [Screenshot: Browser showing the static website hosted on GCP Cloud Storage]
> ```

---

## Comparison Table

| Feature | AWS S3 | Azure Blob Storage | GCP Cloud Storage |
|---|---|---|---|
| Enable static hosting | Properties → Static website | Static website setting | gsutil web set |
| Public access | Bucket policy with s3:GetObject | allUsers on $web container | allUsers Storage Viewer |
| Free tier storage | 5 GB for 12 months | 5 GB LRS for 12 months | 5 GB standard |
| Custom domain support | Yes (with Route 53) | Yes (with Azure CDN) | Yes (with Cloud CDN) |

---

## Outcome
By the end of this practical, you should be able to:
- ✅ Create a cloud storage bucket/container on AWS, Azure, or GCP
- ✅ Upload static HTML/CSS files to the storage service
- ✅ Enable static website hosting and configure the index document
- ✅ Set correct permissions to allow public read access
- ✅ Access and verify the hosted website via its public URL
