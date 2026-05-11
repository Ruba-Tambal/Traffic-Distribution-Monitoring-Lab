
---

### **3. `Verification.md`**

```markdown
# Verification & Testing - Project 3

## ✅ Test Results

### 1. Load Balancer Test
- Open: `http://<LB-Public-IP>`
- Expected: Traffic alternates between vm-web-1 and vm-web-2

### 2. Application Gateway Test
- Open: `http://<APPGW-Public-IP>`
- Expected: Successful Layer 7 traffic distribution

### 3. UDR Validation
- Check **Effective Routes** on the VMs
- Expected: `0.0.0.0/0` route from your route table is active

### 4. Network Watcher Validation
- Topology: Full network diagram visible
- Next Hop: Validates UDR routing correctly

## 📸 Recommended Screenshots
- Load Balancer Backend Pool + Health Status
- Application Gateway Backend Health
- Route Table & UDR Configuration
- Network Watcher Topology
- Browser showing both web servers

**Lab Completed Successfully!** 🎉
