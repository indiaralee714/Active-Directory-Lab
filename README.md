# Active Directory Lab

## Objective
Deploy a Windows Server VM as a Domain Controller (DC-1), install Active Directory Domain Services (AD DS), create Organizational Units and a Domain Admin user, and join a Windows client VM (C-1) to the domain.

## Environment / Prereqs
- Microsoft Azure (VMs hosted in a single Resource Group)
- Windows Server 2019 (DC-1)
- Windows 10 (C-1)
- Network: private IP addressing in the same virtual network/subnet
- Tools used: Remote Desktop (RDP), Active Directory Users and Computers (ADUC), Server Manager

---

## Demo: Step-by-step walk-through

> **Note:** Replace example IPs below (10.0.0.4) with the actual private IPs used in your lab.

### Step 1 — Create resource group & VMs in Azure
1. In Azure Portal → **Resource groups** → **Create** → Name: `ad-lab-rg` → Location: *your region*.  
2. Create VM `DC-1` (Windows Server 2019). Select the virtual network and subnet. Set the network interface **private IP** to static (example: `10.0.0.4`). Allow inbound RDP for setup.  
   ![01_create_vm_dc1](./screenshots/01_create_vm_dc1.png)  
   *Azure VM creation summary for DC-1.*

3. Create VM `C-1` (Windows 10). Leave DNS pointing to DC-1 private IP (or change later in step 5).  
   ![02_create_vm_c1](./screenshots/02_create_vm_c1.png)

### Step 2 — RDP into DC-1 and set static IP (if not set in Azure)
1. Remote Desktop → login to DC-1.  
2. Open **Network Connections** → NIC → Properties → IPv4 → set static IP: `10.0.0.4` / Mask `255.255.255.0` / Gateway `10.0.0.1` / DNS server `10.0.0.4`.  
   ![03_dc1_static_ip](./screenshots/03_dc1_static_ip.png)  
   *DC-1 NIC IPv4 properties showing static IP and DNS pointing to itself.*

### Step 3 — Install AD DS role & promote to domain controller
1. On DC-1 open **Server Manager** → **Add roles and features** → choose **Active Directory Domain Services (AD DS)** → Install.  
   ![04_ad_role_install](./screenshots/04_ad_role_install.png)

2. After installation, in Server Manager → Notifications → **Promote this server to a domain controller** → Select **Add a new forest** → Root domain: `mydomain.local` (or `itlab.local`) → Set Directory Services Restore Mode (DSRM) password → Install and reboot.  
   ![05_promote_to_dc](./screenshots/05_promote_to_dc.png)  
   *Promotion wizard final confirmation.*

3. Verify DNS is installed and the forward lookup zone exists.  
   ![06_dns_manager](./screenshots/06_dns_manager.png)

### Step 4 — Create OUs and a Domain Admin user
1. Open **Active Directory Users and Computers (ADUC)** on DC-1.  
2. Right-click domain → New → OU → name `_Employees` and `_Admins`.  
   ![07_create_ous](./screenshots/07_create_ous.png)

3. Under `_Admins` → New → User → create `Jane Doe` (username: `jane.doe`) → set password → add to **Domain Admins** group (or delegate appropriate rights).  
   ![08_create_admin_user](./screenshots/08_create_admin_user.png)

### Step 5 — Configure client C-1 DNS & join domain
1. On C-1 set NIC DNS server to DC-1 private IP (`10.0.0.4`).  
   ![09_c1_dns_settings](./screenshots/09_c1_dns_settings.png)

2. System → About → **Rename this PC (Change)** → Select **Domain** → enter `mydomain.local` → when prompted, provide `jane.doe` credentials → Restart.  
   ![10_c1_join_domain](./screenshots/10_c1_join_domain.png)

3. After reboot, sign in using domain account: `MYDOMAIN\jane.doe` to confirm successful join.  
   ![11_domain_login_success](./screenshots/11_domain_login_success.png)

### Step 6 — Verify connectivity & name resolution
- On C-1 open Command Prompt:
  - `ipconfig /all` → confirm DNS server is `10.0.0.4`.  
    ![12_c1_ipconfig](./screenshots/12_c1_ipconfig.png)
  - `ping 10.0.0.4` → successful replies.  
    ![13_ping_dc1](./screenshots/13_ping_dc1.png)
  - `nslookup dc-1.mydomain.local` → shows DC-1 address.

---

## Troubleshooting (common fixes)
- If the client cannot find the domain: ensure the client’s DNS is set to the DC’s IP (not public DNS like 8.8.8.8).  
- If domain join fails: verify `DC-1` is reachable (ping), AD DS is running on DC-1, and firewall rules allow necessary traffic (temporarily disable Windows Firewall to test).  
- If DHCP/addresses are conflicting: check Azure NIC settings and confirm static/private IP used.

---

## Evidence / Screenshots
All screenshots are in the `/screenshots` folder and are named to match the step numbers above (e.g., `01_create_vm_dc1.png`, `05_promote_to_dc.png`, etc.). Each image includes a brief caption and highlights the key UI area.

---

## What I learned / Next steps
- AD depends on correct DNS; misconfigured DNS is the most common domain-join blocker.  
- Next: implement Group Policy to enforce password complexity and test user/group policies.