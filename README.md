# Azure Hybrid Networking & Secure Web Application Lab

![Azure](https://img.shields.io/badge/Microsoft-Azure-0078D4?logo=microsoftazure&logoColor=white)
![Networking](https://img.shields.io/badge/Azure-Networking-blue)
![VPN](https://img.shields.io/badge/VPN-Site--to--Site%20%7C%20Point--to--Site-green)
![App Service](https://img.shields.io/badge/Azure-App%20Service-purple)
![Authentication](https://img.shields.io/badge/Easy%20Auth-App%20Authentication-orange)

---

## Project Overview

This project demonstrates the implementation of a **hybrid networking environment in Microsoft Azure**, simulating an enterprise architecture where Azure resources securely communicate with an on-premises network.

Since a physical on-premises environment was not available, a second Azure Virtual Network (VNet) was used to simulate the on-premises infrastructure.

The project includes:

- Azure Virtual Networks
- Azure Virtual Machines
- Site-to-Site VPN
- Point-to-Site VPN
- Azure Web App VNet Integration
- Azure Hybrid Connections
- SQL Server Connectivity
- Easy Authentication (Easy Auth)
- Network Troubleshooting
- Connectivity Testing

---

# Project Objectives

The objectives of this lab were to:

- Create two Azure Virtual Networks
- Deploy virtual machines into each network
- Configure Site-to-Site VPN connectivity
- Simulate an on-premises environment
- Integrate an Azure Web App with Azure networking
- Test connectivity using Azure Kudu (SCM)
- Configure Hybrid Connections
- Connect the Web App to SQL Server
- Secure the Azure Web App using Easy Authentication

---

# Architecture

```

Internet
│
▼
Azure Web App
│
Easy Authentication
│
Point-to-Site Connection
│
Azure VPN Gateway (VNet1)
│
Azure Virtual Machine 1
│
==============================
Site-to-Site VPN Tunnel
==============================
│
Azure VPN Gateway (VNet2)
│
Azure Virtual Machine 2
(Simulated On-Premises Network)
│
SQL Server

```

---

# Azure Services Used

- Azure Resource Groups
- Azure Virtual Network
- Azure Subnets
- Azure VPN Gateway
- Local Network Gateway
- Azure Virtual Machines
- Azure App Service
- Azure App Service Networking
- Hybrid Connections
- Azure Easy Authentication
- SQL Server
- Azure Network Security Groups (NSG)
- Azure Kudu (SCM)

---

#  Technologies Used

- Microsoft Azure
- Azure Portal
- Windows Server
- VPN Gateway
- Site-to-Site VPN
- Point-to-Site VPN
- Azure App Service
- Easy Authentication
- SQL Server
- Kudu Console
- Networking

---

#  Lab Implementation

Note: Full detail in steps.md file

## Lab 1 – Create Azure Virtual Network 1

Created:

- Resource Group
- Virtual Network
- Subnets
- Gateway Subnet
- Windows Virtual Machine

**Outcome**

Successfully deployed VNet1 with a virtual machine.

---

## Lab 2 – Create Azure Virtual Network 2

Created:

- Second Virtual Network
- Gateway Subnet
- Windows Virtual Machine

This network represents an **On-Premises Environment**.

**Outcome**

Successfully deployed the simulated on-premises network.

---

## Lab 3 – Configure Site-to-Site VPN

Configured:

- Virtual Network Gateway
- Local Network Gateway
- Shared Key
- VPN Connection

Validated that both gateways established a successful Site-to-Site VPN connection.

**Outcome**

Secure communication established between both virtual networks.

---

## Lab 4 – Test Connectivity

Performed connectivity tests between:

- VM1 → VM2
- VM2 → VM1

Validated communication using:

- Ping
- Remote Desktop (where applicable)

**Outcome**

Both virtual machines successfully communicated across the VPN tunnel.

---

## Lab 5 – Azure Web App Integration

Created:

- Azure App Service
- App Service Plan

Configured:

- VNet Integration
- Point-to-Site connectivity

**Outcome**

The Azure Web App successfully connected to the Azure Virtual Network.

---

## Lab 6 – Connectivity Testing Using Kudu (SCM)

Used the Azure Kudu (SCM) Console to verify network communication.

Performed:

- Ping VM1
- Ping VM2

**Outcome**

The Web App successfully reached both virtual machines through the configured network.

---

## Lab 7 – Hybrid Connection

Configured Azure Hybrid Connection between the Web App and the simulated on-premises environment.

**Outcome**

The Web App gained secure access to internal network resources.

---

## Lab 8 – SQL Server Connectivity

Configured SQL Server on the simulated on-premises network.

Validated that the Azure Web App successfully communicated with SQL Server.

**Outcome**

Successfully established secure database connectivity.

---

## Lab 9 – Azure Easy Authentication (Easy Auth)

Configured Azure App Service Authentication using **Easy Auth**.

Implementation included:

- Enabling App Service Authentication
- Configuring Microsoft Entra ID (Azure AD) as the Identity Provider
- Restricting unauthenticated access
- Validating successful user authentication before accessing the application

**Outcome**

The Azure Web App was secured using built-in authentication without modifying application code.

---

#  Validation Tests

The following validations were successfully completed:

 Site-to-Site VPN Connected

 Point-to-Site Connectivity

 VM1 can communicate with VM2

 VM2 can communicate with VM1

 Azure Web App connected to VNet

 Azure Web App reached both virtual machines

 SQL Server Connectivity Successful

 Easy Authentication Enabled

---

---

#  Screenshots

Include screenshots of:

- Resource Group
- Virtual Network 1
- Virtual Network 2
- VPN Gateways
- Site-to-Site Connection
- Virtual Machines
- Azure Web App
- Networking Configuration
- Kudu Console
- Ping Results
- SQL Connectivity
- Easy Authentication Configuration

---

#  Challenges Encountered

### Site-to-Site VPN Connection

**Issue**

VPN connection did not establish initially.

**Resolution**

Verified gateway configuration and corrected the shared key.

### Point-to-Site (P2S) VPN Configuration Challenge

**Resolution**

Check steps.md for details

---

### Web App Connectivity

**Issue**

The Azure Web App could not initially reach internal resources.

**Resolution**

Validated VNet Integration configuration and updated Network Security Group rules.

---

### SQL Connectivity

**Issue**

Database connection failed during initial testing.

**Resolution**

Verified SQL Server firewall configuration and network connectivity.

---

#  Key Learnings

Through this project I gained hands-on experience with:

- Azure Networking
- Hybrid Cloud Architecture
- Site-to-Site VPN
- Point-to-Site VPN
- Azure App Service Networking
- Azure Hybrid Connections
- SQL Connectivity
- Microsoft Entra ID Authentication
- Easy Authentication
- Azure Network Troubleshooting
- Enterprise Network Design

---

#  Skills Demonstrated

- Azure Administration
- Azure Networking
- VPN Configuration
- Hybrid Networking
- App Service
- Identity & Access Management
- Microsoft Entra ID
- SQL Connectivity
- Troubleshooting
- Cloud Infrastructure

---

#  References

- Microsoft Learn – Azure Virtual Network Documentation
- Microsoft Learn – Azure VPN Gateway Documentation
- Microsoft Learn – Azure App Service Networking
- Microsoft Learn – Azure Hybrid Connections
- Microsoft Learn – Quickstart: Add Authentication to an Azure Web App (Easy Auth)

---

##  Author

**Rofiat Ahmed Sholagberu**

Azure Support Engineer | Cloud & DevOps Engineer

Passionate about Azure Infrastructure, Networking, Identity Management, Automation, and Cloud Technologies.
