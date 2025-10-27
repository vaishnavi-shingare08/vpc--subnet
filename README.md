# ☁️ Task 3: Create and Configure a Virtual Private Cloud (VPC) with Subnets

## 🎯 Objective

To learn how cloud networking works by creating a **Virtual Private Cloud (VPC)** with **public and private subnets**, and enabling controlled internet access. This demonstrates how resources are isolated and protected using route tables, internet gateways, and CIDR planning.

---

## 🛠 Tools Used

* **AWS Free Tier (VPC, Subnets, Route Tables, Internet Gateway, Security Groups)**
* **Console or AWS CLI**

---

## 📦 Deliverables

* Screenshot of the **VPC dashboard showing subnets**
* Screenshot of the **route table configuration** (public route to IGW)
* Short description: CIDR range, number of subnets, region, and whether internet access is enabled

---

## 🔎 VPC Details (Actual)

| Parameter               | Value                                                |
| ----------------------- | ---------------------------------------------------- |
| **VPC ID**              | `vpc-01ed880b965e76691`                              |
| **State**               | Available                                            |
| **IPv4 CIDR**           | `10.0.0.0/16`                                        |
| **Tenancy**             | default                                              |
| **Default VPC**         | No (custom VPC)                                      |
| **DNS resolution**      | Enabled                                              |
| **DNS hostnames**       | Disabled (enable if instances need public hostnames) |
| **Main route table**    | `rtb-0333499ab2e46f7b6`                              |
| **Main network ACL**    | `acl-067ad882d3ccb1f74`                              |
| **DHCP option set**     | `dopt-03309238de0ff4027`                             |
| **Block Public Access** | Off                                                  |
| **Owner ID**            | `253490...`                                          |

> Note: The `Public-Subnet` previously listed as `subnet-098b204218a03fda0` may have been deleted or is unavailable (console returned "subnet does not exist").

---

## 🧭 Recommended Non-overlapping Subnet Layout (fits inside 10.0.0.0/16)

Use these CIDR blocks to avoid overlap and errors when creating subnets:

* **Public Subnet:** `10.0.1.0/24` (Auto-assign public IP: Enabled)
* **Private Subnet:** `10.0.2.0/24` (Auto-assign public IP: Disabled)

These `/24` subnets are small, safe for testing, and will not overlap each other.

---

## 🚀 Step-by-step: Create VPC & Subnets (Console)

1. **Open AWS Console → VPC → Your VPCs** → Click **Create VPC**

   * Name: `MyVPC`
   * IPv4 CIDR block: `10.0.0.0/16`
   * Tenancy: `default`
   * Create

2. **Create Public Subnet**

   * VPC: `vpc-01ed880b965e76691`
   * Subnet name: `Public-Subnet`
   * Availability Zone: choose one (e.g., `ap-south-1a`)
   * IPv4 CIDR block: `10.0.1.0/24`
   * Create → Select subnet → Actions → Edit auto-assign IPv4 → Enable

3. **Create Private Subnet**

   * Same VPC
   * Subnet name: `Private-Subnet`
   * Availability Zone: different AZ if desired (e.g., `ap-south-1b`)
   * IPv4 CIDR block: `10.0.2.0/24`
   * Create (do not enable auto-assign public IP)

4. **Create and Attach Internet Gateway (IGW)**

   * VPC → Internet Gateways → Create Internet Gateway
   * Name: `MyVPC-IGW`
   * Create → Select → Actions → Attach to VPC → select `vpc-01ed880b965e76691`

5. **Create Public Route Table and Route**

   * VPC → Route Tables → Create Route Table
   * Name: `Public-RT` → Select VPC `vpc-01ed880b965e76691`
   * Create → Select `Public-RT` → Routes → Edit routes → Add route `0.0.0.0/0` → Target: Internet Gateway (`MyVPC-IGW`) → Save
   * Subnet associations → Edit subnet associations → Select `Public-Subnet` → Save

6. **Create Private Route Table**

   * Create Route Table → Name: `Private-RT` → Associate with VPC
   * Do **not** add `0.0.0.0/0` route (keeps subnet isolated)
   * Associate `Private-Subnet` with `Private-RT`

7. **Verify**

   * VPC → Subnets: confirm `Public-Subnet` shows auto-assign public IP enabled and is associated with `Public-RT`.
   * Launch a small EC2 in `Public-Subnet` (test) and confirm it has internet access.
   * Launch a test EC2 in `Private-Subnet` (no public IP) and confirm it cannot reach internet without NAT.

---

## ⚠️ Common Issues & Fixes

* **CIDR overlap error:** Use smaller, non-overlapping CIDR blocks (example `10.0.1.0/24`, `10.0.2.0/24`).
* **"Subnet ID does not exist" when deleting:** Refresh the console, verify via AWS CLI (`aws ec2 describe-subnets --subnet-ids <id>`). If CLI returns `InvalidSubnetID.NotFound`, the subnet is already deleted; wait and refresh UI.
* **Subnet deletion blocked:** Ensure no EC2 instances, ENIs, NAT gateways, or other resources remain in the subnet.

---

## 🖼 Screenshots to Capture (for submission)

1. VPC dashboard listing `vpc-01ed880b965e76691` and subnets
2. Route Table (`Public-RT`) showing route `0.0.0.0/0 → igw-...`
3. Subnet detail pages showing `Auto-assign public IPv4` status for public subnet

---

## 📄 Short Description (for deliverable)

* **VPC ID:** `vpc-01ed880b965e76691`
* **CIDR Range:** `10.0.0.0/16`
* **Number of Subnets:** 2 (Public: `10.0.1.0/24`, Private: `10.0.2.0/24`)
* **Region:** Asia Pacific (Mumbai) `ap-south-1` (example)
* **Internet Access:** Enabled for public subnet via attached IGW; private subnet isolated (no internet route)

---

## ✅ Notes

* If you need DNS hostnames for public instances, enable `DNS hostnames` in VPC settings.
* To enable internet access from private subnets, create a NAT Gateway in the public subnet and add a route in the Private-RT pointing `0.0.0.0/0` to the NAT Gateway.

---

**Author:** Vaishnavi
**Task:** Cloud Internship — Task 3
**Date:** October 2025
