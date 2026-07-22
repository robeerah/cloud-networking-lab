Phase 1: Lab 1 (Networks & Virtual Machines)

Concept:
VNet (Virtual Network): Your private, isolated network in the cloud.
Subnet: A smaller segment inside your VNet (think of the VNet as a house, and Subnets as rooms).
VM (Virtual Machine): A cloud-based computer.

Step-by-Step:

Create the Resource Group (Container)
Search for Resource groups > Create.
Name: BootCamp | Region: South Central US > Review + Create.

Create VNet1
Search Virtual networks > Create.
Name: Vnet1 | Region: South Central US | Resource Group: BootCamp.
IP Address space: 172.17.0.0/16
Default subnet name: sub1 | Default subnet range: 172.17.0.0/24
Important: Go to the Service Endpoints tab (or Security tab depending on portal version), enable it, and select Microsoft.Sql. > Review + Create.

Create Public IP 1
Search Public IP addresses > Create.
Name: PublicIP-VM1 | Resource Group: BootCamp > Review + Create.

Create VM1
Search Virtual machines > Create.
Name: VM1 | Region: South Central US | Image: Free SQL Server License: SQL Server 2017 Express on Windows Server 2016.
Size: Click "See all sizes" and select Standard_B2s.
Username/Password: (Create something memorable, e.g., azureuser / AzureLab123!).
Public IP: Select PublicIP-VM1.
Virtual Network: Vnet1 | Subnet: sub1.
Go to Networking tab: Ensure Inbound port rules allow RDP (3389). > Review + Create.

Create VNet2
Repeat VNet creation. Name: VNet2.
IP Address space: 172.18.0.0/16 | Subnet: sub2a | Range: 172.18.0.0/24.
Enable Service Endpoint for Microsoft.Sql.
After creating VNet2, go to its Subnets blade and Add a new subnet: Name sub2b, Range 172.18.1.0/24.

Create VM2
Create VM. Name: VM2 | Same SQL Image | Size: B2s.
Public IP: Select Create new (let the wizard generate it).
Virtual Network: VNet2 | Subnet: sub2a.


Phase 2: Lab 2 (VPN Gateways & Site-to-Site)

Concept:
VPN Gateway: A special "bridge" that connects two networks together.
Site-to-Site (S2S): A secure tunnel connecting two entire networks (VNet1 and VNet2).
Local Network Gateway: A configuration object that tells your gateway where the other network is and what its IP addresses are.

Tutorial - Create S2S VPN connection between on-premises network and Azure virtual network: Azure portal - Azure VPN Gateway | Microsoft Learn

Step-by-Step:
Create the Gateways (Start these immediately, they take 30-40 mins!)
Search for Virtual network gateways > Create.
GW1: Name: GW1 | Region: South Central US | Network: Vnet1 | Gateway type: VPN | VPN type: Route-based | SKU: VpnGw1 (or Basic). Create a new Public IP named PIP-GW1.
GW2: Repeat the process for VNet2. Name: GW2, Public IP: PIP-GW2.

Create Local Network Gateways
Search Local network gateways > Create.
Local-GW1: Name: Local-GW1. IP Address: Copy the Public IP of GW2 from the GW2 overview page. Address space: 172.18.0.0/16 (This is VNet2's range).
Local-GW2: Name: Local-GW2. IP Address: Copy the Public IP of GW1 from the GW1 overview page. Address space: 172.17.0.0/16 (This is VNet1's range).

Create the S2S Connections
Go to GW1 > Connections > Add.
Name: Conn-GW1-to-GW2 | Connection type: Site-to-Site (IPsec).
Local network gateway: Local-GW1 | Shared key (PSK): AzureLab123! (Make up a key, but remember it).
Go to GW2 > Connections > Add.
Name: Conn-GW2-to-GW1 | Connection type: Site-to-Site (IPsec).
Local network gateway: Local-GW2 | Shared key: AzureLab123! (Must match exactly).
Wait a few minutes until both connections show a status of Connected.
Test the Connection (Ping)
Go to VM1 > Connect > RDP. Download the RDP file and log in with your VM credentials.
Open Command Prompt as Administrator.
Run this exact command to allow ping through the Windows Firewall:netsh advfirewall firewall add rule name="ICMP Allow incoming V4 echo request" dir=in action=allow enable=yes protocol=icmpv4:8,any
Type ipconfig and note the IPv4 Address of VM1.
Repeat this exact process (RDP, Firewall rule, ipconfig) for VM2.
From VM1's command prompt, type: ping <IP_of_VM2>


Phase 3: Lab 3 (Point-to-Site & Web App)
About Azure Point-to-Site VPN connections - Azure VPN Gateway | Microsoft Learn

Concept:
Point-to-Site (P2S): Connects a single device (in this case, your Web App) to a VNet.
Certificates: Used to authenticate the Web App so Azure trusts it to connect to the VNet.
Kudu / SCM Console: A hidden backend command prompt for your Web App used for debugging.

Step-by-Step:
Generate Certificates (On your local Windows PC)
Open PowerShell on your local computer and run:
Type certmgr.msc and press Enter. Find P2SRootCert under Personal > Certificates.
Right-click > All Tasks > Export. Choose NO, do not export the private key. Choose Base-64 encoded X.509 (.CER). Save it to your desktop.
Open the saved .cer file with Notepad. Copy everything between -----BEGIN CERTIFICATE----- and -----END CERTIFICATE-----.
Configure P2S on GW2
Go to GW2 > Point-to-site configuration > Configure now.
Address pool: 172.19.0.0/24 (Must be different from your VNets!).
Authentication type: Azure certificate.
Root cert name: P2SRootCert. Paste the Notepad text into Public Certificate Data.
Important: Uncheck "IKEv2 VPN" (leave SSTP checked). Save.

Create Web App & VNet Integration
Search App Services > Create.
Name: BootCampWebApp-[yourinitials] | Publish: Code | OS: Windows | Region: South Central US.
Create a new App Service Plan (Basic B1 is fine).
Once deployed, go to the Web App > Networking > VNet Integration > Add VNet.
Select VNet2 > Add.
Update Local Gateway for VM1 Routing
Why? Right now, the Web App can reach VNet2 (VM2). But to reach VNet1 (VM1), the S2S tunnel needs to know about the Web App's P2S IP pool.
Search Local network gateways > Open Local-GW2.
In the Address space section, click Add address space and type 172.19.0.0/24. Save.
Test Pings from Web App (Kudu Console)
Go to your Web App > Advanced Tools (or search Kudu) > Go.
At the top, click Debug console > CMD.
Find the IP of VM2 (from Phase 2) and type: tcpping <VM2_IP>:3389
Find the IP of VM1 and type: tcpping <VM1_IP>:3389



Phase 4: Lab 4 (Easy Auth)

Quickstart - Add app authentication to a web app - Azure App Service | Microsoft Learn
Concept:

Easy Auth (App Service Authentication): A feature that puts a "bouncer" at the door of your Web App. Nobody can see the app unless they log in with a valid identity (like a Microsoft or GitHub account).

Step-by-Step:
Configure Easy Auth
Go to your Web App in the Azure Portal.
On the left menu, click Authentication.
Click Add identity provider.
Identity Provider: Microsoft (or Microsoft Entra ID).
Client secret: Leave as default or create new if prompted.
Unrestricted access: Select Require authentication.
Click Add / Save.
Test the Login



Open a Private/Incognito browser window.
Navigate to your Web App's URL (e.g., https://bootcampwebapp-xxx.azurewebsites.net).
You will be redirected to a Microsoft Login screen. Log in with your Microsoft account.