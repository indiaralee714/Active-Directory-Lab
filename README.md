<h1>Active Directory Lab</h1>


<h2>Description</h2>
Hands-on labs from my Active Directory Fundamentals training. Includes screenshots, steps, and lessons learned.
<br />


<h2>Utilities Used</h2>

- <b>Active Directive User & Computers</b>
- <b>Remote Desktop Connection</b>

<h2>Environments Used </h2>

- <b>Virtual Machines (Windows)</b>
- <b>Microsoft Azure</b>

<h2>Program walk-through: Set-up of Active Directory</h2>

<p align="center">
Virtual machine C-1 (Client-1) set-up through azure *Also set-up a Virtual machine for DC-1 (Domain Controller) but screenshot was lost: <br/>
<img src=https://i.imgur.com/MfgTN4f.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Successfullly connected to Virtual machine DC-1 (Domain Controller) using remote desktop: <br/>
<img src=https://i.imgur.com/DCXOj8c.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Disabling firewall for DC-1: <br/>
<img src=https://i.imgur.com/JblC7VE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Adjusting C-1's DNS settings to match DC-1 private IP: <br/>
<img src=https://i.imgur.com/gIvfpvX.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Pinging DC-1's IP address from C-1 VM, Successful: <br/>
<img src=https://i.imgur.com/IH0B0yH.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Using "ipconfig" the DNS server reflects the same as DC-1 (10.0.0.4): <br/>
<img src="https://i.imgur.com/GGjPNAr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
  
<h2>Installing Active Directory</h2>
<p align="center"> 
Installation of Active Directory services: <br/>
<img src="https://i.imgur.com/OCuoO64.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />

 <h2>Creating a Domain Admin within DC-1:</h2> 
 <p align="center"> 
Using ADUC (Active Directory User & Controls), two organizational units were created and labeled under "_Employees" & "_Admins"<br/>
<img src="https://i.imgur.com/BqMahGk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Creating admin user "Jane Doe": <br/>
<img src="https://i.imgur.com/WptuXWy.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Configuring properties of user to reflect admin control: <br/>
<img src="https://i.imgur.com/C7zFIhW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Signing in to DC-1 under Jane admin user, then joining C-1 to the same network as DC-1 (mydomain.com): <br/>
<img src="https://i.imgur.com/UalXFsj.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />





































