# Lab Overview - Azure Cloud Networking

## Overall Objective
By the end of this lab series, I created a simulation of an Azure Cloud VNet connected to an on-premises network with a Web App consuming an on-premises resource. Since we can't connect to an actual on-premises network, we used a second Azure Cloud virtual network as a stand-in.

## What Was Accomplished
1. Created two virtual networks, each with a virtual machine and VPN gateway
2. Connected the two VNets using a Site-to-Site connection between gateways
3. Connected a Web App to one VNet using a Point-to-Site connection
4. Successfully pinged both VMs from each VNet
5. Successfully pinged from Web App's SCM site to both VMs

## Lab Breakdown

### Lab 1: Network Infrastructure Setup
**Objective**: Create multiple VNets, subnets, public IPs, and VMs

**Tasks Completed**:
-  Created Resource Group "BootCamp"
-  Deployed VNet1 (172.17.0.0/16) with subnet sub1
-  Deployed VNet2 (172.18.0.0/16) with subnets sub2a and sub2b
-  Created Public IP addresses
-  Deployed VM1 on VNet1 with SQL Server 2017 Express
-  Deployed VM2 on VNet2 with SQL Server 2017 Express
-  Enabled Service Endpoints for Microsoft.Sql

### Lab 2: Site-to-Site VPN Configuration
**Objective**: Add VPN Gateways and configure S2S connection

**Tasks Completed**:
-  Created VPN Gateway GW1 on VNet1 (Route-based, VpnGw1 SKU)
-  Created VPN Gateway GW2 on VNet2 (Route-based, VpnGw1 SKU)
-  Configured Local Network Gateways
-  Established S2S IPsec connection between GW1 and GW2
-  Verified bidirectional connectivity with ping tests
-  Configured Windows Firewall for ICMP traffic

### Lab 3: Point-to-Site VPN & Web App Integration
**Objective**: Create Web App and connect via P2S VPN

**Tasks Completed**:
-  Generated self-signed root certificate (P2SRootCert)
-  Configured P2S on GW2 with SSTP protocol
-  Created Azure Web App in South Central US region
-  Configured VNet Integration for Web App
-  Added P2S address range to Local-GW2
-  Verified connectivity using Kudu console tcpping
-  Successfully tested connections to VM1 and VM2

### Lab 4: Authentication Implementation
**Objective**: Configure Easy Auth on Web App

**Tasks Completed**:
-  Enabled App Service Authentication
-  Configured Microsoft identity provider
-  Set authentication to "Require authentication"
-  Tested login flow with Microsoft account
-  Verified protected access to Web App

## Network Configuration Summary

| Resource | Name | Address Space | Region |
|----------|------|---------------|--------|
| VNet1 | VNet1 | 172.17.0.0/16 | South Central US |
| VNet2 | VNet2 | 172.18.0.0/16 | South Central US |
| P2S Pool | - | 172.19.0.0/24 | - |

## Testing Results
- **S2S Connection**:  Connected (both directions)
- **VM1 to VM2 Ping**:  0% packet loss
- **VM2 to VM1 Ping**:  0% packet loss
- **Web App to VM2**:  TCP 3389 successful
- **Web App to VM1**:  TCP 3389 successful