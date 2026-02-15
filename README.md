# 🛡️ Cyberinfiniti – Microsoft Azure Enterprise Cloud Security Assessment

![Azure](https://img.shields.io/badge/Platform-Microsoft%20Azure-0078D4?logo=microsoftazure&logoColor=white)
![Security](https://img.shields.io/badge/Domain-Cloud%20Security-blue)
![SIEM](https://img.shields.io/badge/SIEM-Microsoft%20Sentinel-purple)
![Defender](https://img.shields.io/badge/Defender-Plan%202-red)
![Status](https://img.shields.io/badge/Project-Completed-success)

---

## 📌 Project Overview


This repository documents a **comprehensive Enterprise Cloud Security Assessment** conducted within a Microsoft Azure production environment.

The assessment evaluates identity governance, access control, monitoring, detection engineering, threat protection, and incident response readiness using Azure-native security services.

The project aligns technical controls with:

- Zero Trust Architecture  
- Least Privilege Enforcement  
- Defense-in-Depth Strategy  
- SOC Modernization Principles  

---

## 📑 Table of Contents

1. [Executive Summary](#-executive-summary)
2. [Scenario & Objectives](#-scenario--objectives)
3. [Scope of Assessment](#-scope-of-assessment)
4. [Azure Environment Setup](#-azure-environment-setup)
5. [Methodology & Tooling](#-methodology--tooling)
6. [Lab 01 – RBAC Implementation](#-lab-01--role-based-access-control-rbac)
7. [Lab 02 – NSG & ASG Segmentation](#-lab-02--network-security-groups--application-security-groups)
8. [Lab 03 – Azure Firewall](#-lab-03--azure-firewall)
9. [Lab 04 – ACR & AKS Security](#-lab-04--azure-container-registry--aks-security)
10. [Lab 05 – Service Endpoints & Storage Security](#-lab-05--service-endpoints--storage-security)
11. [Lab 06 – Log Analytics & DCR](#-lab-06--log-analytics--data-collection-rules)
12. [Lab 07 – Microsoft Defender for Cloud](#-lab-07--microsoft-defender-for-cloud)
13. [Lab 08 – Just-In-Time VM Access](#-lab-08--just-in-time-jit-vm-access)
14. [Lab 09 – Microsoft Sentinel (SIEM & SOAR)](#-lab-09--microsoft-sentinel-siem--soar)
15. [Key Findings](#-key-findings)
16. [Strategic Recommendations](#-strategic-recommendations)
17. [Conclusion](#-conclusion)

---

# 📄 Executive Summary

This assessment demonstrates how Microsoft Azure security services can be configured and operationalized to defend enterprise cloud workloads.

The environment was evaluated across:

- Identity & Access Management  
- Network Segmentation  
- Cloud Workload Protection  
- Monitoring & Logging  
- Privileged Access Management  
- SIEM & Automated Response  

Results show significant reduction in attack surface and improved detection maturity through Azure-native integration.

---

# 🎯 Scenario & Objectives

Cyberinfiniti Ltd required validation of its cloud security posture.

### Primary Objectives

- Implement least-privilege RBAC
- Centralize logging & monitoring
- Detect misconfigurations and threats
- Reduce exposed management ports
- Deploy enterprise-grade SIEM
- Provide executive-level risk insights

---

# 🔎 Scope of Assessment

The following services were implemented and evaluated:

- Microsoft Entra ID
- Azure Role-Based Access Control (RBAC)
- Azure Monitor & Log Analytics
- Microsoft Defender for Cloud (Plan 2)
- Azure Firewall
- Azure Container Registry (ACR)
- Azure Kubernetes Service (AKS)
- Microsoft Sentinel
- Azure Logic Apps (SOAR)

All resources deployed in **East US** under segmented resource groups.

---

# ⚙️ Azure Environment Setup

### Subscription Model
- Azure Pay-As-You-Go
- Owner & Contributor role assignments
- Dedicated Entra ID tenant

### Design Principles
- Least Privilege
- Group-Based RBAC
- Resource Group Segmentation
- Azure-native Security Stack

---

# 🧪 Lab 01 – Role-Based Access Control (RBAC)

### 🎯 Objective
Implement least-privilege access using group-based role assignments.

### 🔹 Create Security Group (Portal)

1. Navigate to **Microsoft Entra ID**
2. Create User
3. Create Security Group
4. Add user to group

### 🔹 PowerShell Example

```powershell
New-AzADUser -DisplayName "Isabel Garcia" `
  -UserPrincipalName "isabel@domain.com" `
  -AccountEnabled $true

New-AzADGroup -DisplayName "Junior Admins" `
  -MailNickname "JuniorAdmins"


### 🔹 Assign VM Contributor Role (Azure CLI)

```bash
az role assignment create \
  --assignee <GroupObjectID> \
  --role "Virtual Machine Contributor" \
  --resource-group TEAM8
```

### ✅ Validation

- Confirm role assignment under **Access Control (IAM)**
- Validate least privilege enforcement
- Ensure users cannot escalate permissions

---

# 🌐 Lab 02 – Network Security Groups & Application Security Groups

### 🎯 Objective
Segment workloads using NSGs and ASGs to enforce granular traffic control.

### 🔹 Steps Implemented

1. Create Virtual Network and Subnet  
2. Create Application Security Groups:
   - `WebServers`
   - `ManagementServers`
3. Deploy Network Security Group (NSG)
4. Associate NSG with Subnet
5. Configure Security Rules:
   - Allow RDP (3389) → ManagementServers
   - Deny RDP → WebServers
   - Allow HTTP (80) → WebServers
6. Deploy two virtual machines
7. Associate NICs with respective ASGs

### ✅ Validation

- RDP accessible only to Management Server
- Web server accessible via HTTP
- Direct RDP to Web Server blocked

---

# 🔥 Lab 03 – Azure Firewall

### 🎯 Objective
Implement centralized outbound traffic inspection and filtering.

### 🔹 Deployment Steps

1. Deploy Azure Firewall
2. Create Public IP
3. Configure Firewall Subnet (`AzureFirewallSubnet`)
4. Create Route Table
5. Force default route (0.0.0.0/0) through Firewall

### 🔹 Application Rule Example

- Allow: `www.bing.com`
- Protocol: HTTPS
- Source: Internal subnet

### 🔹 Network Rule Example

- Allow DNS (Port 53)
- Protocol: UDP/TCP

### ✅ Validation

- Only approved FQDN accessible
- All other outbound traffic denied
- DNS resolution functioning

---

# 🐳 Lab 04 – Azure Container Registry & AKS Security

### 🎯 Objective
Secure container registry and Kubernetes workloads.

### 🔹 Create Azure Container Registry

```bash
az acr create \
  --resource-group TEAM8 \
  --name team8acr \
  --sku Standard
```

### 🔹 Create AKS Cluster with Managed Identity

```bash
az aks create \
  --resource-group TEAM8 \
  --name team8aks \
  --node-count 2 \
  --enable-managed-identity
```

### 🔹 Attach ACR to AKS

```bash
az aks update \
  --name team8aks \
  --resource-group TEAM8 \
  --attach-acr team8acr
```

### 🔹 Deploy Application

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### ✅ Security Enhancements

- Managed Identity enforced
- Registry access restricted
- Controlled external exposure
- Kubernetes service validation

---

# 🗄️ Lab 05 – Service Endpoints & Storage Security

### 🎯 Objective
Restrict storage account access to specific Azure subnets.

### 🔹 Implementation Steps

1. Create Virtual Network with two subnets
2. Enable `Microsoft.Storage` Service Endpoint
3. Deploy Storage Account
4. Restrict network access to selected subnet
5. Deploy test VMs in both subnets

### ✅ Validation

- Private subnet: Access Allowed
- Public subnet: Access Denied
- Public network access disabled

---

# 📊 Lab 06 – Log Analytics & Data Collection Rules

### 🎯 Objective
Centralize telemetry collection and enable log analysis.

### 🔹 Deployment Steps

1. Deploy Virtual Machine
2. Create Log Analytics Workspace
3. Install Azure Monitor Agent
4. Create Data Collection Rule (DCR)
5. Associate DCR with VM

### 🔹 Sample KQL Query

```kql
SecurityEvent
| where EventLevelName == "Information"
| limit 10
```

### ✅ Validation

- Logs visible in workspace
- Agent successfully connected
- Telemetry centralized

---

# 🛡️ Lab 07 – Microsoft Defender for Cloud

### 🎯 Objective
Enable advanced cloud workload protection.

### 🔹 Configuration

- Enable Defender for Servers (Plan 2)
- Review Secure Score
- Enable vulnerability assessments
- Validate endpoint protection coverage

### ✅ Outcomes

- Real-time threat detection enabled
- Configuration recommendations generated
- Behavioral analytics activated

---

# 🔐 Lab 08 – Just-In-Time (JIT) VM Access

### 🎯 Objective
Reduce attack surface by limiting exposure of management ports.

### 🔹 Configuration Steps

1. Navigate to Defender for Cloud
2. Select target VM
3. Enable JIT Access
4. Configure:
   - RDP (3389)
   - Time window (1–3 hours)
   - Allowed IP ranges

### ✅ Validation

- RDP closed by default
- Access granted only upon request
- Automatic port closure after expiration

---

# 📡 Lab 09 – Microsoft Sentinel (SIEM & SOAR)

### 🎯 Objective
Deploy centralized SIEM with automated response capabilities.

### 🔹 Deployment Steps

1. Create Log Analytics Workspace
2. Enable Microsoft Sentinel
3. Connect Azure Activity Logs
4. Create Analytics Rule
5. Deploy Logic App Playbook

### 🔹 Sample Detection Query

```kql
AzureActivity
| where OperationNameValue contains "Delete"
| summarize count() by Caller
```

### 🔹 Automation

- Incident triggered on suspicious activity
- Playbook executes automated response
- Alerts sent to SOC team

### ✅ Validation

- Incident generated successfully
- Playbook executed
- SOC visibility confirmed

---

# 📌 Key Findings

- Strong IAM governance using RBAC
- Effective network segmentation
- Centralized monitoring improved visibility
- Defender Plan 2 enhanced detection capability
- JIT significantly reduced exposed attack surface
- Sentinel enabled automated SOC workflows

---

# 🧠 Strategic Recommendations

- Conduct periodic RBAC access reviews
- Expand Sentinel automation playbooks
- Maintain centralized logging architecture
- Enforce policy-based governance
- Continue cloud security skill development

---

# ✅ Conclusion

This project demonstrates a scalable, enterprise-grade Azure cloud security architecture aligned with Zero Trust principles.

By integrating:

- Microsoft Entra ID  
- Azure RBAC  
- Defender for Cloud  
- Azure Monitor  
- Microsoft Sentinel  

Cyberinfiniti Ltd achieved a defensible, monitored, and resilient cloud security posture suitable for modern enterprise operations.

---
