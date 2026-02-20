# Active Directory IAM Lab Setup

# Step 1: Create the Virtual Machine (DOMAIN-CTRL)
1.1 Open VirtualBox → Click New
Name: testmachine
Type: Microsoft Windows
Version: Windows Server 2019 (64-bit) or 2022 (64-bit)
Press enter or click to view image in full size

# 1.2 Skip Unattended Installation (Important!)
Scroll to the bottom of the “Name and Operating System” section
✅ Check the box: Skip Unattended Installation
❗ Why? We want to install Windows manually so we can learn each step and have full control.

# 1.3 Set Hardware
Memory (RAM): 4096 MB (4 GB)
CPU: 1
Press enter or click to view image in full size

# 1.4 Create Virtual Hard Disk
Create a new disk: ✅ Yes
Type: VDI (VirtualBox Disk Image)
Storage: Dynamically allocated
Size: 50 GB
Press enter or click to view image in full size

# ▶️ Step 2: Install Windows Server
2.1 Start the VM
Click DOMAIN-CTRL → Click Start
You should now see:

Install Windows screen with Language, Time, Keyboard settings


# 2.2 Install the OS
Click Next → then Install Now
Choose: Windows Server Standard (Desktop Experience)
❗ Don’t pick Core — you want the version with a GUI
Select: Custom installation
Pick the 50 GB disk
Let it install (this may take 5–10 minutes)


# 2.3 Create Admin Password
Set a simple but strong password (e.g., Password123!)
Log in when prompted
Press enter or click to view image in full size

# 🎉 You’ve Set Up Your Base Lab!
You now have a Windows Server VM ready to become your Domain Controller.


# 🛠️ Step 3: Install Active Directory & Promote to Domain Controller
3.1 Open Server Manager
When Windows starts, Server Manager should open automatically. If it doesn’t:

Click Start → Search Server Manager → Open it

# 3.2 Add Roles and Features
In Server Manager, click “Add roles and features”
On the “Before you begin” page, click Next
Choose Role-based or feature-based installation → Click Next
Select your server from the list (it should already be selected) → Next
On the “Server Roles” screen:
✅ Check Active Directory Domain Services
Click Add Features when prompted
Click Next until the Install button appears
Click Install and wait (this takes 2–5 minutes)


# 3.3 Promote Server to Domain Controller
After installation, you’ll see a yellow triangle at the top of Server Manager.

Click it → Select “Promote this server to a domain controller”
Choose: Add a new forest
Root domain name: labexampractice
📌 Why? A domain is like your own private network. lab.local is your practice company domain.

Set a Directory Services Restore Mode (DSRM) password: use something simple like Password123!
Keep clicking Next through the DNS and NetBIOS pages (default values are fine)
On the Paths screen: leave everything as default → Click Next
Review your settings → Click Install
The server will reboot when done
Press enter or click to view image in full size

# 3.4 Log Back in with Domain Controller Active
After reboot, the login screen will now show lab\\Administrator — this means your domain is working!

Use your password from earlier to log in
Press enter or click to view image in full size

✅ You’re Ready for IAM Tasks!
We now have:

A working Domain Controller
Your own labexampractice domain
AD tools installed and ready to manage users, groups, and security policies

# ⚙️ Step 4: Create Users in Active Directory with PowerShell
In this step, we will:

Import a list of employees from a CSV file
Automatically create users in Active Directory
Place them in the correct OU (Organizational Unit)
This simulates how HR feeds new employee data to IT, and IT automates account creation.

# 4.1 Create the Organizational Unit (OU)
Click Start → Search for “Active Directory Users and Computers” → Open it
Right-click on your domain (e.g., lab.local) → New > Organizational Unit
Name it: Employees

# 4.2 Download the Sample CSV File
Here’s what our CSV file should look like:

Press enter or click to view image in full size

📌 Why this format? It’s how real-world HR systems export new employee data

# 4.3 Create a PowerShell Script to Add Users
Open PowerShell ISE or Notepad on your VM
Paste the following script:

# 4.4 Run the Script
Open PowerShell as Administrator
Run your script by pressing F5 in PowerShell ISE or executing it directly
Wait for the script to finish

# 4.5 Verify the Users Were Created
Go back to Active Directory Users and Computers
Click the Employees OU → You should see John, James, and Alice

# 🧩 Step 5: Configure Role-Based Access Control (RBAC) with Security Groups
Now that we’ve created user accounts, it’s time to manage what they can access using security groups — a key part of IAM.

RBAC means users are granted access based on their role (like HR, Finance, or IT), not their individual identity. This keeps systems secure and manageable

# 5.1 Create Security Groups in Active Directory
Open Active Directory Users and Computers (ADUC)
Right-click the Employees OU → New > Group
Create the following groups:
HR_Group

Finance_Group

IT_Group

4. Set Group scope to: Global

5. Set Group type to: Security

# 5.2 Add Users to Their Departmental Group
You can do this manually (for now):
In ADUC, go to the Employees OU
Double-click each user → Go to the Member Of tab
Click Add → Type the group name (e.g., HR_Group) → Click OK
📌 Assignments:

Alice → HR_Group

John → Finance_Group

James → IT_Group


# 🧪 Step 6: Automate Group Assignments with PowerShell
Now that you’ve added users to groups manually, let’s automate it — just like a real IT department would.

You’ll write a PowerShell script that reads from the same CSV and assigns each user to the correct security group based on their department.


# 6.2 Create the Group Assignment Script
Open PowerShell ISE and paste the following:

Press enter or click to view image in full size

📌 What this does:

Loops through each user
Builds the group name from their department (e.g., “HR” → “HR_Group”)
Adds them to the appropriate group

# 6.3 Run the Script
Make sure you run PowerShell as Administrator
Press F5 in PowerShell ISE or right-click the script to run

# 6.4 Verify Group Membership in ADUC
Open Active Directory Users and Computers
Go to the Employees OU
Double-click a user → Go to Member Of tab

# 🧷 Step 7: Apply Group Policy and Simulate User Offboarding
This step simulates how IT teams:

Apply policies to restrict access
Handle employee terminations using deprovisioning procedures

# 🛡️ 7.1 Apply a Group Policy Object (GPO)
Goal: Disable the Control Panel for the IT department only.

👣 Step-by-step:
Open Group Policy Management from the Start Menu
Expand:
Forest: lab.local > Domains > lab.local
Right-click lab.local or the Employees OU
→ Select “Create a GPO in this domain, and Link it here”
Name it: Disable_ControlPanel_IT
Right-click the new GPO → Select Edit
Navigate to:
User Configuration > Policies > Administrative Templates > Control Panel
Double-click “Prohibit access to Control Panel and PC settings”
→ Set it to Enabled → Click OK

# 🎯 7.2 Target the GPO to the IT_Group Only
By default, GPOs apply to everyone. Let’s limit this one.

👣 Step-by-step:
In Group Policy Management, select Disable_ControlPanel_IT
Go to the Delegation tab → Click Advanced
Remove Authenticated Users
Click Add → Type IT_Group → Click OK
Check both boxes:
✅ Read
✅ Apply Group Policy

# 👤 7.3 Simulate Offboarding: Disable & Move a User
Let’s say Carol Taylor (ctaylor) is leaving the company.

👣 Manual Deprovisioning Steps:
Open Active Directory Users and Computers
Right-click on ctaylor → Click Disable Account
Press enter or click to view image in full size

3. Open ctaylor's properties → Member Of tab
→ Remove IT_Group


4. Create a new OU called TerminatedUsers

5. Drag ctaylor into TerminatedUsers OU

Press enter or click to view image in full size

# 🧾 Step 8: IAM Audit Log Review + Portfolio Documentation
🕵️‍♂️ 8.1 View Audit Logs for User + Group Changes
In real-world IAM, changes like creating users, disabling accounts, or modifying groups are logged in Event Viewer.

👣 To View Logs:
Open Event Viewer (Start → search Event Viewer)
Expand:
Windows Logs > Security
Look for events related to:
User account created: Event ID 4720

Account disabled: Event ID 4725

Group membership changed: Event ID 4728 or 4729

These logs validate the hands-on work and demonstrate audit awareness
