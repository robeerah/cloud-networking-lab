# Cloud Networking Lab: Hybrid Cloud Architecture

## 📋 Project Overview
This repository documents the step-by-step implementation of a production-grade hybrid cloud network architecture using Azure Cloud services. This project simulates an enterprise environment where a cloud-based web application securely consumes resources from a simulated on-premises network via encrypted VPN tunnels.

## 🎯 Key Objectives Achieved
1. Designed and deployed isolated Virtual Networks (VNets) with segmented subnets.
2. Established a bidirectional Site-to-Site (S2S) IPsec tunnel between two networks.
3. Configured a Point-to-Site (P2S) VPN to securely connect a cloud Web App to the network.
4. Implemented Easy Auth (App Service Authentication) to secure web application access.
5. Successfully validated cross-network connectivity and port-specific routing.

---

## 🏗️ Phase 1: Networks & Virtual Machines

### 💡 Core Concepts
* **VNet (Virtual Network):** A private, isolated network in the XYZ Cloud.
* **Subnet:** A smaller, segmented section inside a VNet (e.g., separating web and database tiers).
* **VM (Virtual Machine):** A cloud-based compute instance.

### ⚙️ Step-by-Step Implementation
1. **Create Resource Group:** 
   - Name: `BootCamp` | Region: `XYZ-Central-US`
2. **Deploy VNet1:** 
   - Name: `Vnet1` | Address Space: `172.17.0.0/16`
   - Subnet: `sub1` | Range: `172.17.0.0/24`
   - *Security:* Enable Service Endpoint for `XYZ.Sql`.
3. **Create Public IP:** 
   - Name: `PublicIP-VM1` (Standalone resource).
4. **Deploy VM1:** 
   - Image: `Free XYZ SQL Server License: SQL Server 2017 Express on Windows Server 2016`
   - Size: `Standard_B2s` | Network: `Vnet1` / `sub1` | Public IP: `PublicIP-VM1`
   - *Networking:* Ensure inbound RDP (3389) is allowed.
5. **Deploy VNet2:** 
   - Name: `VNet2` | Address Space: `172.18.0.0/16`
   - Subnet `sub2a`: `172.18.0.0/24` (Enable `XYZ.Sql` Service Endpoint)
   - Subnet `sub2b`: `172.18.1.0/24` (Added post-creation for network segmentation practice).
6. **Deploy VM2:** 
   - Image/Size: Same as VM1 | Network: `VNet2` / `sub2a`
   - Public IP: Generated dynamically during VM creation wizard.

---

## 🌉 Phase 2: VPN Gateways & Site-to-Site (S2S)

### 💡 Core Concepts
* **VPN Gateway:** A specialized routing instance that connects networks securely.
* **Site-to-Site (S2S):** An encrypted IPsec tunnel connecting two entire networks.
* **Local Network Gateway:** A configuration object defining the remote network's public IP and private address space.

### ⚙️ Step-by-Step Implementation
1. **Deploy VPN Gateways** *(Note: Takes 30-40 mins to provision)*:
   - **GW1:** Network `Vnet1` | Type: `VPN` | VPN Type: `Route-based` | SKU: `VpnGw1` | Public IP: `PIP-GW1`
   - **GW2:** Network `VNet2` | Type: `VPN` | VPN Type: `Route-based` | SKU: `VpnGw1` | Public IP: `PIP-GW2`
2. **Configure Local Network Gateways**:
   - **Local-GW1:** Points to GW2's Public IP | Address Space: `172.18.0.0/16` (VNet2's range).
   - **Local-GW2:** Points to GW1's Public IP | Address Space: `172.17.0.0/16` (VNet1's range).
3. **Establish S2S Connections**:
   - On **GW1**: Create Connection `Conn-GW1-to-GW2` → Type: `Site-to-Site (IPsec)` → Local Gateway: `Local-GW1` → Shared Key: `XYZLab123!`
   - On **GW2**: Create Connection `Conn-GW2-to-GW1` → Type: `Site-to-Site (IPsec)` → Local Gateway: `Local-GW2` → Shared Key: `XYZLab123!` *(Must match exactly)*.
4. **Validate Connectivity**:
   - RDP into both VMs. Open **Administrator Command Prompt** on each.
   - Enable ICMP (Ping) through the Windows Firewall on both VMs:
     ```cmd
     netsh advfirewall firewall add rule name="ICMP Allow incoming V4 echo request" dir=in action=allow enable=yes protocol=icmpv4:8,any
     ```
   - Run `ipconfig` on both VMs to note their private IPv4 addresses.
   - Execute `ping <IP_of_Other_VM>` from each machine to verify bidirectional tunnel success (0% packet loss).

---

## 📱 Phase 3: Point-to-Site (P2S) & Web App Integration

### 💡 Core Concepts
* **Point-to-Site (P2S):** Connects a single resource (the Web App) to a VNet.
* **Certificates:** Cryptographic "ID badges" used to authenticate the Web App to the XYZ Cloud gateway.
* **Kudu / SCM Console:** A backend diagnostic command-line interface for XYZ Web Apps.

### ⚙️ Step-by-Step Implementation
1. **Generate Root Certificate** (Local Windows PC):
   - Run in PowerShell (Admin):
     ```powershell
     $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
     ```
   - Open `certmgr.msc`, locate `P2SRootCert` under *Personal > Certificates*.
   - Export → **No, do not export the private key** → **Base-64 encoded X.509 (.CER)**.
   - Open the `.cer` file in Notepad and copy the text between `-----BEGIN CERTIFICATE-----` and `-----END CERTIFICATE-----`.
2. **Configure P2S on GW2**:
   - Address Pool: `172.19.0.0/24` *(Must not overlap with existing VNets)*.
   - Authentication: `XYZ certificate`.
   - Root Cert Name: `P2SRootCert` | Paste the Base-64 text into Public Certificate Data.
   - *Crucial:* Uncheck "IKEv2 VPN" (leave only SSTP checked). Save.
3. **Deploy Web App & VNet Integration**:
   - Name: `BootCampWebApp-[initials]` | OS: `Windows` | Plan: `Basic B1` (Required for VNet Integration).
   - Navigate to **Networking** → **VNet Integration** → Add → Select `VNet2`.
4. **Update Routing for Cross-Network Access**:
   - Open **Local-GW2** → Add Address Space: `172.19.0.0/24` (The P2S pool). This allows the S2S tunnel to route Web App traffic back to VNet1.
5. **Validate Connectivity via Kudu**:
   - Open Web App → **Advanced Tools** (Kudu) → **Debug Console** → **CMD**.
   - Test connection to VM2: `tcpping <VM2_Private_IP>:3389`
   - Test connection to VM1: `tcpping <VM1_Private_IP>:3389` *(Validates the full P2S → S2S path)*.

---

## 🔒 Phase 4: Easy Auth (App Service Authentication)

### 💡 Core Concepts
* **Easy Auth:** A built-in XYZ Cloud feature that acts as a "bouncer," intercepting all web traffic and requiring valid identity provider authentication before granting access to the application.

### ⚙️ Step-by-Step Implementation
1. **Configure Authentication**:
   - Navigate to Web App → **Authentication** → **Add identity provider**.
   - Provider: `XYZ Identity Provider` (or XYZ Entra ID).
   - App Registration: `Create new`.
   - Unrestricted Access: Select **Require authentication**.
   - Save configuration.
2. **Validate Login Flow**:
   - Open an Incognito/Private browser window.
   - Navigate to `https://bootcampwebapp-[initials].xyzwebsites.net`.
   - Verify automatic redirection to the XYZ Login screen.
   - Authenticate successfully to view the default Web App landing page.

---

