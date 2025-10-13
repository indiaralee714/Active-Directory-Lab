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


### Step 1 — Create resource group & VMs in Azure
1. In Azure Portal → **Resource groups** → **Create** → Name: `ad-lab-rg` → Location: *your region*.  
2. Create VM `DC-1` (Windows Server 2019). Select the virtual network and subnet. Set the network interface **private IP** to static (example: `10.0.0.4`). Allow inbound RDP for setup.  
   <img width="1415" height="456" alt="01_create_vm_dc1" src="https://github.com/user-attachments/assets/3c0ea02a-0ac3-47cf-aa53-506fe0021f6a" />


3. Create VM `C-1` (Windows 10). Leave DNS pointing to DC-1 private IP (or change later in step 5).  
   <img width="893" height="547" alt="02_create_vm_c1" src="https://github.com/user-attachments/assets/b125c8fd-ce49-49f3-bafb-9a476476fa4a" />


### Step 2 — RDP into DC-1 and set static IP (if not set in Azure)
1. Remote Desktop → login to DC-1.  
2. Open **Network Connections** → NIC → Properties → IPv4 → set static IP: `10.0.0.4` / Mask `255.255.255.0` / Gateway `10.0.0.1` / DNS server `10.0.0.4`.  
     <img width="656" height="735" alt="03_dc1_static_ip" src="https://github.com/user-attachments/assets/6956f70f-0b6f-47cf-aa90-7b4e579c8510" />

   *DC-1 NIC IPv4 properties showing static IP and DNS pointing to itself.*

### Step 3 — Install AD DS role & promote to domain controller
1. On DC-1 open **Server Manager** → **Add roles and features** → choose **Active Directory Domain Services (AD DS)** → Install.  
 <img width="982" height="700" alt="04_ad_role_install" src="https://github.com/user-attachments/assets/932c31d7-72fd-4d0c-9e1d-fa392fef7526" />


2. After installation, in Server Manager → Notifications → **Promote this server to a domain controller** → Select **Add a new forest** → Root domain: `mydomain.local` (or `itlab.local`) → Set Directory Services Restore Mode (DSRM) password → Install and reboot.  
 <img width="952" height="700" alt="05_promote_to_dc" src="https://github.com/user-attachments/assets/5019ca4c-5469-4279-bc7e-6d8853b7a96a" />


3. Verify DNS is installed and the forward lookup zone exists.  
  <img width="507" height="402" alt="06_dns_manager" src="https://github.com/user-attachments/assets/b9f9eb96-19ec-4d19-b946-48ef48048de4" />


### Step 4 — Create OUs and a Domain Admin user
1. Open **Active Directory Users and Computers (ADUC)** on DC-1.  
2. Right-click domain → New → OU → name `_Employees` and `_Admins`.  
   <img width="701" height="402" alt="07_create_ous" src="https://github.com/user-attachments/assets/d859c1f3-06b2-4fa1-ab57-25b469f558d1" />


3. Under `_Admins` → New → User → create `Jane Doe` (username: `jane.doe`) → set password → add to **Domain Admins** group (or delegate appropriate rights).  
  <img width="547" height="467" alt="08_create_admin_user" src="https://github.com/user-attachments/assets/ccac6413-e5b2-423d-9e79-2f7acb2b49ed" />


### Step 5 — Configure client C-1 DNS & join domain
1. On C-1 set NIC DNS server to DC-1 private IP (`10.0.0.4`).  
   <img width="788" height="651" alt="09_c1_dns_settings" src="https://github.com/user-attachments/assets/80181287-b1b8-44c3-8f8c-e7af7a34a419" />

2. System → About → **Rename this PC (Change)** → Select **Domain** → enter `mydomain.local` → when prompted, provide `jane.doe` credentials → Restart.  
<img width="987" height="483" alt="10_c1_join_domain" src="https://github.com/user-attachments/assets/c3dc767e-a32d-4cb3-ba70-07a2e9e17a5a" />

3. After reboot, sign in using domain account: `MYDOMAIN\jane.doe` to confirm successful join.  
  <img width="931" height="685" alt="11_domain_login_success" src="https://github.com/user-attachments/assets/d134356d-6a67-435a-9727-373d73cf8168" />


### Step 6 — Verify connectivity & name resolution
- On C-1 open Command Prompt:
  - `ipconfig /all` → confirm DNS server is `10.0.0.4`.
  - <img width="651" height="562" alt="12_c1_ipconfig" src="https://github.com/user-attachments/assets/ab5e0c9c-5f62-4877-aa28-429699d4c7d7" />

  - `ping 10.0.0.4` → successful replies.  
   <img width="465" height="248" alt="13_ping_dc1" src="https://github.com/user-attachments/assets/66f5966e-c377-4174-be40-0e5d7ed2bfe4" />


---

## Troubleshooting (common fixes)
- If the client cannot find the domain: ensure the client’s DNS is set to the DC’s IP (not public DNS like 8.8.8.8).  
- If domain join fails: verify `DC-1` is reachable (ping), AD DS is running on DC-1, and firewall rules allow necessary traffic (temporarily disable Windows Firewall to test).  
- If DHCP/addresses are conflicting: check Azure NIC settings and confirm static/private IP used.

---

## Evidence / Screenshots
All screenshots are in the `/screenshots` folder and are named to match the step numbers above (e.g., `01_create_vm_dc1.png`, `05_promote_to_dc.png`, etc.). 

---

## What I learned / Next steps
- AD depends on correct DNS; misconfigured DNS is the most common domain-join blocker.  
- Next: implement Group Policy to enforce password complexity and test user/group policies.
