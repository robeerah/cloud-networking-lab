
---

### **File 4: docs/implementation-guide.md**

```markdown
# Implementation Guide

## Prerequisites
- Azure Cloud Subscription
- Basic networking knowledge (CIDR, subnets, routing)
- Remote Desktop Client
- PowerShell (for certificate generation)

## Phase 1: Infrastructure Setup

### Step 1.1: Create Resource Group
```bash
az group create --name BootCamp --location southcentralus

Step 1.2: Deploy VNet1
az network vnet create \
  --name Vnet1 \
  --resource-group BootCamp \
  --location southcentralus \
  --address-prefix 172.17.0.0/16 \
  --subnet-name sub1 \
  --subnet-prefix 172.17.0.0/24

Step 1.3: Deploy VM1

  az vm create \
  --name VM1 \
  --resource-group BootCamp \
  --location southcentralus \
  --image "MicrosoftSQLServer:SQL2017-WS2016:Express:latest" \
  --size Standard_B2s \
  --admin-username azureuser \
  --admin-password YourPassword123!

Step 1.4: Repeat for VNet2 and VM2
(Use 172.18.0.0/16 address space)

Phase 2: VPN Gateway Configuration
Step 2.1: Create GW1

Phase 2: VPN Gateway Configuration
Step 2.1: Create GW1

az network vnet-gateway create \
  --name GW1 \
  --resource-group BootCamp \
  --vnet Vnet1 \
  --gateway-type Vpn \
  --vpn-type RouteBased \
  --sku VpnGw1 \
  --public-ip-address GW1-PIP

Step 2.2: Create Local Network Gateway
az network local-gateway create \
  --name Local-GW1 \
  --resource-group BootCamp \
  --gateway-ip-address <GW2-Public-IP> \
  --local-address-prefix 172.18.0.0/16

Step 2.3: Create S2S Connection
az network vpn-connection create \
  --name Conn-GW1-to-GW2 \
  --resource-group BootCamp \
  --vnet-gateway1 GW1 \
  --local-gateway2 Local-GW1 \
  --shared-key AzureLab123!


Phase 3: Point-to-Site Configuration

Step 3.1: Generate Root Certificate
$cert = New-SelfSignedCertificate `
  -Type Custom `
  -KeySpec Signature `
  -Subject "CN=P2SRootCert" `
  -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 `
  -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -KeyUsageProperty Sign `
  -KeyUsage CertSign

Step 3.2: Export Certificate

Export-Certificate -Cert $cert -FilePath C:\P2SRootCert.cer

Step 3.3: Configure P2S on GW2
az network vnet-gateway update \
  --name GW2 \
  --resource-group BootCamp \
  --address-prefixes 172.19.0.0/24 \
  --vpn-client-protocols Sstp \
  --root-cert-name P2SRootCert \
  --root-cert-data "<certificate-data>"

Phase 4: Web App Deployment

Step 4.1: Create Web App
  az webapp create \
  --name BootCampWebApp-uniqueid \
  --resource-group BootCamp \
  --plan AppServicePlan \
  --runtime "DOTNET:6.0"

Step 4.2: Configure VNet Integration

az webapp vnet-integration add \
  --name BootCampWebApp-uniqueid \
  --resource-group BootCamp \
  --vnet VNet2 \
  --subnet GatewaySubnet

Testing & Validation
Test 1: S2S Connectivity

# From VM1
ping 172.18.0.4

# From VM2
ping 172.17.0.4

Test 2: Web App to VM Connectivity

# Access Kudu Console: https://yourapp.scm.azurewebsites.net
tcpping 172.18.0.4:3389
tcpping 172.17.0.4:3389


Troubleshooting Tips:

Gateway not connecting: Check shared key matches on both sides
Ping failing: Verify Windows Firewall allows ICMP
Web App can't reach VNet: Ensure VNet Integration is in "Succeeded" state
Certificate errors: Verify root cert is uploaded correctly to Azure

Cleanup Resources:

az group delete --name BootCamp --yes --no-wait