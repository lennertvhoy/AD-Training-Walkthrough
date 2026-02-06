# AD.Training.com: Advanced Domain Management Walkthrough

This repository contains an in-depth walkthrough for the "EXTRA" exercise involving Active Directory configuration, User Profiles, and Homefolder Management.

## 🎯 Scenario Overview
You are tasked with setting up a complex organizational structure on `SRV-DC-01` within the `AD.training.com` domain, including specific storage requirements and profile types for different departments.

---

## 🛠️ Step 1: Organizational Unit (OU) Hierarchy
Log in to **SRV-DC-01** as the Domain Administrator and open **Active Directory Users & Computers**.

1. Create a root OU named **"EXTRA"**.
2. Under "EXTRA", create the following sub-OUs:
   - `Servers`: To house all domain server objects.
   - `Workstations`: To house client machine objects.
   - `Departments`: To house the departmental OUs.
3. Under "Departments", create:
   - `PRODUCTION`
   - `HR`
   - `TECHNICAL`
   - `MANAGEMENT`
4. **User Creation**: In each departmental OU, create 2 standard user accounts (e.g., `ProdUser1`, `ProdUser2`).

---

## 📂 Step 2: File Server Preparation (SRV-FILE-01)
Log in to **SRV-FILE-01** to prepare the storage infrastructure.

1. Open **Server Manager** > **File and Storage Services** > **Shares**.
2. Create two new SMB Shares:
   - `Test-Homefolders$`: (Hidden share recommended)
   - `Test-Userprofiles$`: (Hidden share recommended)
3. **Permissions**: Ensure "Authenticated Users" or the specific departmental groups have **Modify** NTFS permissions.

---

## 👤 Step 3: Mandatory vs. Roaming Profiles
Configure the user profile paths in the "Profile" tab of each user's AD properties.

### A. Production & HR (Mandatory Profiles + 400MB Limit)
*Mandatory profiles provide a read-only environment. If the user makes changes, they are discarded at logout.*

1. **Path**: Set Profile Path to `\\SRV-FILE-01\Test-Userprofiles$\mandatory`.
2. **Setup**: Rename the `NTUSER.DAT` to `NTUSER.MAN` in the profile folder to make it mandatory.
3. **Homefolder**: 
   - Connect `H:` to `\\SRV-FILE-01\Test-Homefolders$\%username%`.
   - **Hard Limit**: Use **File Server Resource Manager (FSRM)** on SRV-FILE-01 to set a **Hard Quota of 400MB** on the homefolder directory.

### B. Technical (Roaming Profiles + 1GB Limit)
*Roaming profiles sync changes back to the server.*

1. **Path**: Set Profile Path to `\\SRV-FILE-01\Test-Userprofiles$\%username%`.
2. **Homefolder**: 
   - Connect `H:` to `\\SRV-FILE-01\Test-Homefolders$\%username%`.
   - **Hard Limit**: Set a **Hard Quota of 1GB** via FSRM.

### C. Management (Roaming Profiles + 1GB Soft Limit)
1. **Path**: Same as Technical.
2. **Homefolder**: 
   - Connect `H:` to `\\SRV-FILE-01\Test-Homefolders$\%username%`.
   - **Soft Limit**: Set a **Soft Quota of 1GB** via FSRM. *Soft quotas allow users to exceed the limit but trigger an email/log notification to the admin.*

---

## 🔍 Admin Checklist
- [ ] OUs created under "EXTRA"?
- [ ] 2 Users per department?
- [ ] Shares created on SRV-FILE-01?
- [ ] `.MAN` extension verified for Mandatory users?
- [ ] Quotas (Hard vs Soft) applied in FSRM?

---
*Created by Lennert Van Hoyweghen - IT Trainer*
