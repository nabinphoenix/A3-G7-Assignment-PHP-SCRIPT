# A3-G7-Assignment-PHP-SCRIPT
# Custom IAM Role Creation Guide for AWS EC2

This guide provides step-by-step instructions for creating a custom IAM role to replace the `Inventory-App-Role` used in your PHP application deployment. This role allows your EC2 instances to securely communicate with **AWS Systems Manager (SSM)** and **Parameter Store**.

---

## Technical Specifications
- **Role Name:** `A3-G7-CSVC-App-Role`
- **Trusted Entity:** Amazon EC2 (`ec2.amazonaws.com`)
- **Permissions:** Systems Manager access (SSM) and Parameter Store access.

---

## TASK 1: Create the IAM Role

**Navigation:**
1. Log in to the **AWS Management Console**.
2. Search for **"IAM"** in the search bar and click on it.
3. In the left navigation pane, click on **Roles**.
4. Click the **Create role** (orange button) on the top right.

### Step 1.1: Select Trusted Entity
1. **Trusted entity type:** Select **AWS service**.
2. **Service or use case:** Select **EC2**.
3. **Use case:** Select **EC2** from the options (Allows EC2 instances to call AWS services on your behalf).
4. Click **Next**.

### Step 1.2: Add Permissions
In the **Permissions policies** search box, find and select the following managed policies:

| Policy Name | Description |
|---|---|
| `AmazonSSMManagedInstanceCore` | **(Required)** Enables SSM Session Manager and core SSM features. |
| `AmazonSSMReadOnlyAccess` | Allows the application to read parameters from Parameter Store. |

1. Search for `AmazonSSMManagedInstanceCore` → Check the box.
2. Search for `AmazonSSMReadOnlyAccess` → Check the box.
3. Click **Next**.

### Step 1.3: Name, Review, and Create
1. **Role name:** `A3-G7-CSVC-App-Role`
2. **Description:** `IAM role for A3-G7 PHP App to access SSM and Parameter Store.`
3. **Step 1: Select trusted entities:** Verify that "Service: ec2.amazonaws.com" is listed.
4. **Step 2: Add permissions:** Verify both policies are listed.
5. Click **Create role** (bottom right).

---

## TASK 2: Attach the Role to Your EC2 Instance

Now that the role exists, you must "attach" it to your web server instance.

**Navigation:**
1. Go to the **EC2 Dashboard**.
2. Click on **Instances (running)**.
3. Select your instance: **`A3-G7-CSVC-EC2-AZ1`**.
4. Click **Actions** (top right) → **Security** → **Modify IAM role**.

**Configuration:**
1. **IAM role:** Select **`A3-G7-CSVC-App-Role`** from the dropdown menu.
2. Click **Update IAM role**.

---

## TASK 3: Verification

### 3.1 Verify via Console
- In the EC2 instance details, look at the **Details** tab.
- Find the **IAM role** field. It should now show `A3-G7-CSVC-App-Role`.

### 3.2 Verify via Session Manager
1. Go to **Systems Manager** → **Session Manager**.
2. Click **Start session**.
3. If the role is working correctly, your instance will appear in the list.
4. Connect and run:
   ```bash
   # Check which role is currently active on the instance
   curl http://169.254.169.254/latest/meta-data/iam/info
   ```
   *Expected Output: You should see `"InstanceProfileArn": ".../A3-G7-CSVC-App-Role"`*

---

## Why a Custom Role is Better?
1. **Least Privilege**: You can restrict access to *only* specific parameter paths (e.g., `/example/*`) instead of giving full SSM access.
2. **Ownership**: It follows the project's naming convention (`A3-G7-CSVC-...`), making it easier to audit and manage compared to lab-provided roles.
3. **Portability**: You can delete and recreate this role in any AWS account without relying on pre-provisioned lab resources.
