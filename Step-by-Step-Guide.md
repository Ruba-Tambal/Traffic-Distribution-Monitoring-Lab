Traffic Distribution & Monitoring Lab
Load Balancer + Application Gateway + UDR + Network Watcher
🎯 Scenario
Contoso Corporation is facing high traffic loads on their e-commerce web application. They need a robust solution to:

Distribute traffic efficiently using Layer 4 and Layer 7 load balancing
Implement advanced routing with Application Gateway
Control outbound traffic using User Defined Routes (UDR)
Monitor and troubleshoot the network using Network Watcher


🏗️ Architecture


















































ComponentNameDetailsResource Grouprg-traffic-monitoringUAE NorthVirtual Networkvnet-traffic10.3.0.0/16Subnet (App)subnet-app10.3.1.0/24Subnet (App Gateway)subnet-gateway10.3.2.0/27Web VMsvm-web-1, vm-web-2Windows Server 2022Load Balancerlb-productionStandard SKU (Layer 4)Application Gatewayappgw-productionStandard V2 (Layer 7)Route Tablert-web-serversUDR for outbound traffic

STEP-BY-STEP IMPLEMENTATION
STEP 1: Create Resource Group

Search for Resource groups → Click + Create
Resource group name: rg-traffic-monitoring
Region: UAE North
Click Review + create → Create

STEP 2: Create Virtual Network

Search for Virtual networks → Click + Create
Name: vnet-traffic
Address space: 10.3.0.0/16
Region: UAE North
Click Review + create → Create

STEP 3: Create Subnets
Create the following subnets inside vnet-traffic:




















Subnet NameAddress RangePurposesubnet-app10.3.1.0/24Web Serverssubnet-gateway10.3.2.0/27Application Gateway (Dedicated)
STEP 4: Create Web Virtual Machines
Create two VMs with the following specifications:

Name: vm-web-1 and vm-web-2
Image: Windows Server 2022 Datacenter
Size: Standard_D2s_v3
Virtual Network: vnet-traffic
Subnet: subnet-app
Public IP: None
NSG: Allow ports 80 (HTTP) and 3389 (RDP)

STEP 5: Install IIS and Customize Web Pages
RDP into both VMs and run the following commands:
PowerShell# Install IIS
Install-WindowsFeature -Name Web-Server -IncludeManagementTools

# Customize pages
# On vm-web-1:
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<h1 style='color:blue; text-align:center'>Welcome to Web Server 1 (vm-web-1)</h1>"

# On vm-web-2:
Set-Content -Path "C:\inetpub\wwwroot\index.html" -Value "<h1 style='color:green; text-align:center'>Welcome to Web Server 2 (vm-web-2)</h1>"
STEP 6: Create Load Balancer (Layer 4)

Search Load balancers → + Create
Name: lb-production
SKU: Standard
Public IP: Create new
Add Backend Pool containing both vm-web-1 and vm-web-2
Create Health Probe: HTTP, Port 80, Path /
Create Load Balancing Rule: Frontend Port 80 → Backend Port 80 (TCP)

STEP 7: Test Load Balancer

Open browser and navigate to http://<LB-Public-IP>
Refresh multiple times to see traffic distributed between both servers.

STEP 8: Create Application Gateway (Layer 7)

Search Application gateways → + Create
Name: appgw-production
Tier: Standard V2
Subnet: subnet-gateway
Create Public IP
Add Backend Pool (vm-web-1 & vm-web-2)
Create HTTP Listener (Port 80)
Create Routing Rule (Basic)

STEP 9: Test Application Gateway

Open browser → http://<AppGateway-Public-IP>
Refresh to verify traffic distribution.

STEP 10: Create User Defined Route (UDR)

Search Route tables → + Create
Name: rt-web-servers
Add Route:
Address prefix: 0.0.0.0/0
Next hop type: Internet

Associate the route table to subnet-app

STEP 11: Network Watcher

Search for Network Watcher and enable it for UAE North region.
Use the following tools:
Topology – View full network diagram
Next Hop – Verify routing behavior (especially UDR)
Connection Troubleshoot – Test connectivity between resources
Effective Routes – Check applied routes on VMs



✅ Lab Completed Successfully!
