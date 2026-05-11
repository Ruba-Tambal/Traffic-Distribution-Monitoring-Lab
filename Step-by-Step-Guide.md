
# Step-by-Step Guide - Project 3: Traffic Distribution & Monitoring Lab

**Load Balancer + Application Gateway + UDR + Network Watcher**

## 🎯 Scenario
**Contoso Corporation**, a rapidly growing e-commerce company, is facing high traffic loads on their web application. They need a robust solution to distribute traffic, implement Layer 7 routing, control outbound traffic with UDR, and monitor the network.

## 🏗️ Architecture

![Architecture](./diagrams/architecture.png)

---

### STEP 1 — Create Resource Group
1. Search **Resource groups** → **+ Create**
2. Name: `rg-traffic-monitoring`
3. Region: **UAE North**
4. Click **Review + create** → **Create**

### STEP 2 — Create Virtual Network
1. Search **Virtual networks** → **+ Create**
2. Name: `vnet-traffic`
3. Address space: `10.3.0.0/16`
4. Create

### STEP 3 — Create Subnets
- `subnet-app`: `10.3.1.0/24`
- `subnet-gateway`: `10.3.2.0/27` (dedicated for Application Gateway)

### STEP 4 — Create Two Web VMs (`vm-web-1` & `vm-web-2`)
- Image: Windows Server 2022 Datacenter
- Size: Standard_D2s_v3
- Subnet: `subnet-app`
- Public IP: None
- Allow ports 80 and 3389 in NSG

### STEP 5 — Install IIS & Customize Pages
Run on both VMs:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
Customize index.html with different content on each server.
STEP 6 — Create Load Balancer (Layer 4)

Name: lb-production
SKU: Standard
Add both VMs to backend pool
Create HTTP health probe (port 80)
Create load balancing rule (port 80)

STEP 7 — Test Load Balancer
Open http://<LB-Public-IP> and refresh.
STEP 8 — Create Application Gateway (Layer 7)

Name: appgw-production
Tier: Standard V2
Subnet: subnet-gateway
Add both VMs to backend pool
Create HTTP listener (port 80)
Create routing rule

STEP 9 — Test Application Gateway
Open http://<APPGW-Public-IP>
STEP 10 — Create User Defined Route (UDR)

Create Route Table: rt-web-servers
Add route: 0.0.0.0/0 → Next hop: Internet
Associate to subnet-app

STEP 11 — Network Watcher

Enable Network Watcher
Use Topology, Next Hop, and Connection Troubleshoot
