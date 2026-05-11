# Step-by-Step Guide - Traffic Distribution & Monitoring Lab
This guide walks you through implementing traffic distribution using **Azure Load Balancer** (Layer 4), **Application Gateway** (Layer 7), **User Defined Routes (UDR)**, and network monitoring with **Network Watcher**.

## 🎯 Scenario
Contoso Corporation is facing high traffic loads on their e-commerce web application. They need a robust solution to distribute traffic, implement Layer 7 routing, control outbound traffic with UDR, and monitor the network.

## 🏗️ Architecture
![Architecture](./diagrams/traffic-monitoring-architecture.png)

---
### STEP 1 — Create Resource Group
1. Search for **Resource groups** → Click **+ Create**
2. Settings:
   - **Name**: `rg-traffic-monitoring`
   - **Region**: **UAE North**
3. Click **Review + create** → **Create**

### STEP 2 — Create Virtual Network
1. Search for **Virtual networks** → Click **+ Create**
2. Basics:
   - Resource group: `rg-traffic-monitoring`
   - Name: `vnet-traffic`
   - Region: **UAE North**
3. IP Addresses:
   - Address space: `10.3.0.0/16`
4. Click **Review + create** → **Create**

### STEP 3 — Create Subnets
Inside `vnet-traffic`, create the following subnets:

| Subnet Name       | Address Range     | Purpose                            |
|-------------------|-------------------|------------------------------------|
| subnet-app        | 10.3.1.0/24       | Web Servers                        |
| subnet-gateway    | 10.3.2.0/27       | Application Gateway                |

### STEP 4 — Create Web Virtual Machines
Create two VMs with the following settings:

- **Names**: `vm-web-1` and `vm-web-2`
- **Image**: Windows Server 2022 Datacenter
- **Size**: Standard_D2s_v3
- **Virtual network**: `vnet-traffic`
- **Subnet**: `subnet-app`
- **Public IP**: **None**
- **NSG**: Allow ports **80 (HTTP)** and **3389 (RDP)**

### STEP 5 — Install IIS & Customize Pages
RDP into both VMs and run the following PowerShell commands (as Administrator):

```powershell
# Install IIS
Install-WindowsFeature -Name Web-Server -IncludeManagementTools

# On vm-web-1
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<h1 style='color:blue; text-align:center'>Welcome to Web Server 1 (vm-web-1)</h1>"

# On vm-web-2
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<h1 style='color:green; text-align:center'>Welcome to Web Server 2 (vm-web-2)</h1>"
STEP 6 — Create Load Balancer (Layer 4)

Search Load balancers → Click + Create
Basics:
Name: lb-production
SKU: Standard

Frontend IP: Create new Public IP
Backend Pool: Add both vm-web-1 and vm-web-2
Health Probe:
Protocol: HTTP
Port: 80
Path: /

Load Balancing Rule:
Frontend Port: 80
Backend Port: 80
Protocol: TCP

Click Review + create → Create

STEP 7 — Test Load Balancer

Copy the Public IP of the Load Balancer
Open browser and navigate to: http://<LB-Public-IP>
Refresh multiple times to see traffic distributed between both servers (blue & green pages)

STEP 8 — Create Application Gateway (Layer 7)

Search Application gateways → Click + Create
Basics:
Name: appgw-production
Tier: Standard V2
Region: UAE North

Virtual Network:
Virtual network: vnet-traffic
Subnet: subnet-gateway

Frontend IP: Create new Public IP
Backend Pool: Add both vm-web-1 and vm-web-2
Listener: HTTP on Port 80
Routing Rule: Basic routing to backend pool
Click Review + create → Create

STEP 9 — Test Application Gateway

Copy the Public IP of the Application Gateway
Open browser and navigate to: http://<AppGW-Public-IP>
Refresh multiple times to verify traffic distribution

STEP 10 — Create User Defined Route (UDR)

Search Route tables → Click + Create
Settings:
Name: rt-web-servers
Region: UAE North

Click Create

Add Route:

Click + Add
Settings:
Route name: to-internet
Address prefix: 0.0.0.0/0
Next hop type: Internet

Click Add
Go to the route table → Subnets → Associate
Associate to subnet-app

STEP 11 — Configure Network Watcher

Search for Network Watcher in the portal
Enable it for UAE North region
Use the following tools:
Topology – View complete network diagram
Next Hop – Verify routing behavior
Connection Troubleshoot – Test connectivity
Effective Routes – Check UDR on VMs



✅ Lab Completed Successfully!
text**Now everything is consistent.**  
Just copy the whole block above and paste it into your `.md` file. It should render perfectly.

Let me know if you still see any issue!
