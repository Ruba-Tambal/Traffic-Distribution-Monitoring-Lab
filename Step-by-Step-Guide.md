# Step-by-Step Guide — Traffic Distribution & Monitoring Lab

This guide walks you through implementing traffic distribution using:

- **Azure Load Balancer** (Layer 4)
- **Application Gateway** (Layer 7)
- **User Defined Routes (UDR)**
- **Network Watcher**

---

## 🎯 Scenario

Contoso Corporation is facing high traffic loads on their e-commerce web application. They need a robust solution to:

- Distribute traffic efficiently
- Implement Layer 7 routing
- Control outbound traffic using UDR
- Monitor and troubleshoot the network

---

## 🏗️ Architecture

![Architecture](./diagrams/traffic-monitoring-architecture.png)

---

# STEP 1 — Create Resource Group

1. Search for **Resource groups**
2. Click **+ Create**

### Settings

- **Name:** `rg-traffic-monitoring`
- **Region:** `UAE North`

3. Click **Review + create**
4. Click **Create**

---

# STEP 2 — Create Virtual Network

1. Search for **Virtual networks**
2. Click **+ Create**

### Basics

- **Resource group:** `rg-traffic-monitoring`
- **Name:** `vnet-traffic`
- **Region:** `UAE North`

### IP Addresses

- **Address space:** `10.3.0.0/16`

3. Click **Review + create**
4. Click **Create**

---

# STEP 3 — Create Subnets

Inside `vnet-traffic`, create the following subnets:

| Subnet Name    | Address Range | Purpose             |
|----------------|---------------|---------------------|
| `subnet-app`   | `10.3.1.0/24` | Web Servers         |
| `subnet-gateway` | `10.3.2.0/27` | Application Gateway |

---

# STEP 4 — Create Web Virtual Machines

Create two virtual machines with the following settings:

| Setting | Value |
|---|---|
| Names | `vm-web-1`, `vm-web-2` |
| Image | Windows Server 2022 Datacenter |
| Size | Standard_D2s_v3 |
| Virtual Network | `vnet-traffic` |
| Subnet | `subnet-app` |
| Public IP | None |
| NSG Rules | Allow `80 (HTTP)` and `3389 (RDP)` |

---

# STEP 5 — Install IIS & Customize Pages

RDP into both VMs and run the following PowerShell commands as Administrator.

## Install IIS

```powershell
Install-WindowsFeature -Name Web-Server -IncludeManagementTools
```

## Configure vm-web-1

```powershell
Set-Content -Path "C:\inetpub\wwwroot\index.html" `
-Value "<h1 style='color:blue; text-align:center'>Welcome to Web Server 1 (vm-web-1)</h1>"
```

## Configure vm-web-2

```powershell
Set-Content -Path "C:\inetpub\wwwroot\index.html" `
-Value "<h1 style='color:green; text-align:center'>Welcome to Web Server 2 (vm-web-2)</h1>"
```

---

# STEP 6 — Create Load Balancer (Layer 4)

1. Search for **Load balancers**
2. Click **+ Create**

## Basics

- **Name:** `lb-production`
- **SKU:** `Standard`

## Frontend IP

- Create a new **Public IP**

## Backend Pool

- Add:
  - `vm-web-1`
  - `vm-web-2`

## Health Probe

| Setting | Value |
|---|---|
| Protocol | HTTP |
| Port | 80 |
| Path | `/` |

## Load Balancing Rule

| Setting | Value |
|---|---|
| Frontend Port | 80 |
| Backend Port | 80 |
| Protocol | TCP |

3. Click **Review + create**
4. Click **Create**

---

# STEP 7 — Test Load Balancer

1. Copy the **Public IP** of the Load Balancer
2. Open a browser and navigate to:

```text
http://<LB-Public-IP>
```

3. Refresh multiple times

You should see traffic distributed between:

- Blue page → `vm-web-1`
- Green page → `vm-web-2`

---

# STEP 8 — Create Application Gateway (Layer 7)

1. Search for **Application gateways**
2. Click **+ Create**

## Basics

| Setting | Value |
|---|---|
| Name | `appgw-production` |
| Tier | Standard V2 |
| Region | UAE North |

## Virtual Network

| Setting | Value |
|---|---|
| Virtual Network | `vnet-traffic` |
| Subnet | `subnet-gateway` |

## Frontend IP

- Create a new **Public IP**

## Backend Pool

Add:

- `vm-web-1`
- `vm-web-2`

## Listener

| Setting | Value |
|---|---|
| Protocol | HTTP |
| Port | 80 |

## Routing Rule

- Basic routing to backend pool

3. Click **Review + create**
4. Click **Create**

---

# STEP 9 — Test Application Gateway

1. Copy the **Public IP** of the Application Gateway
2. Open a browser and navigate to:

```text
http://<AppGW-Public-IP>
```

3. Refresh multiple times to verify traffic distribution.

---

# STEP 10 — Create User Defined Route (UDR)

1. Search for **Route tables**
2. Click **+ Create**

## Settings

| Setting | Value |
|---|---|
| Name | `rt-web-servers` |
| Region | UAE North |

3. Click **Create**

## Add Route

| Setting | Value |
|---|---|
| Route Name | `to-internet` |
| Address Prefix | `0.0.0.0/0` |
| Next Hop Type | Internet |

## Associate Route Table

Associate the route table with:

```text
subnet-app
```

---

# STEP 11 — Configure Network Watcher

1. Search for **Network Watcher**
2. Enable it for the **UAE North** region

## Use the Following Tools

| Tool | Purpose |
|---|---|
| Topology | View complete network diagram |
| Next Hop | Verify routing behavior |
| Connection Troubleshoot | Test connectivity |
| Effective Routes | Check UDR on VMs |

---

# ✅ Lab Completed Successfully!

You have successfully implemented:

- Azure Load Balancer (Layer 4)
- Azure Application Gateway (Layer 7)
- User Defined Routes (UDR)
- Azure Network Watcher Monitoring
