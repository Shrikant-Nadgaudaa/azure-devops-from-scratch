# 🏗️ STEP 2 — Audix Client Project Setup

> 🚀 **Practical Industrial Lab — Azure DevOps Project Creation**

इस step में हम सिर्फ documentation नहीं बनाएँगे।

हम इसी structure को **actual Azure DevOps Organization** में create करेंगे।

---

# 🎯 हमारा Actual Scenario

हमारी company:

```text
AUDIX TECHNOLOGY
```

के लिए पाँच clients हैं:

```text
AUDIX TECHNOLOGY
│
├── SBI
├── HDFC
├── TCS
├── CAPGEMINI
└── BANDHAN BANK
```

हर client का अपना Azure DevOps Project होगा।

```text
AudixTechnology
│
├── AUDIX-SBI
├── AUDIX-HDFC
├── AUDIX-TCS
├── AUDIX-CAPGEMINI
└── AUDIX-BANDHAN
```

---

# 🧠 हम ऐसा क्यों कर रहे हैं?

हर client का infrastructure, repository, pipeline और deployment workflow अलग रखना है।

उदाहरण:

```text
AUDIX-SBI
│
├── Repositories
├── Pipelines
├── Environments
├── Service Connections
└── Project Permissions
```

और:

```text
AUDIX-HDFC
│
├── Repositories
├── Pipelines
├── Environments
├── Service Connections
└── Project Permissions
```

इससे client-level separation maintain करना आसान होता है।

---

# 🏷️ Project Naming Standard

हम पूरे lab में यह standard follow करेंगे:

| Client       | Azure DevOps Project |
| ------------ | -------------------- |
| SBI          | `AUDIX-SBI`          |
| HDFC         | `AUDIX-HDFC`         |
| TCS          | `AUDIX-TCS`          |
| Capgemini    | `AUDIX-CAPGEMINI`    |
| Bandhan Bank | `AUDIX-BANDHAN`      |

### Naming Rules

```text
AUDIX-<CLIENT>
```

Examples:

```text
AUDIX-SBI
AUDIX-HDFC
AUDIX-TCS
AUDIX-CAPGEMINI
AUDIX-BANDHAN
```

---

# 🏗️ Project के अंदर हमारा Standard Structure

हर client project के अंदर आगे चलकर:

```text
AUDIX-SBI
│
├── Repos
│
├── Pipelines
│
├── Environments
│   ├── DEV
│   ├── UAT
│   ├── PRE-PROD
│   └── PROD
│
└── Service Connections
```

उदाहरण:

```text
AUDIX-SBI
│
├── infra-sbi
│
├── sbi-infrastructure-pipeline
│
├── DEV
├── UAT
├── PRE-PROD
├── PROD
│
└── Azure Service Connections
```

---

# 🖱️ GUI — पहला Actual Project

Azure DevOps खोलें।

Organization:

```text
AudixTechnology
```

फिर:

```text
Projects
   ↓
New project
```

---

# ✏️ Project Details

सबसे पहले SBI project बनाएँगे।

### Project Name

```text
AUDIX-SBI
```

### Description

```text
Audix Technology - SBI Infrastructure Automation Project
```

### Visibility

```text
Private
```

### Version Control

```text
Git
```

### Work Item Process

```text
Agile
```

अगर organization में पहले से suitable default process configured है, तो उसे 그대로 रहने दें।

---

# 🔐 Security Decision

हमारे client projects:

```text
Private
```

रहेंगे।

इसका मतलब:

```text
Public Internet
      ❌
       │
       ▼
AUDIX-SBI
      │
      ▼
Authorized Users / Teams
```

Client infrastructure code और deployment configuration public नहीं होना चाहिए।

---

# ✅ Create Project

अब:

```text
Create
```

पर click करें।

---

# 🔎 Verification

Project create होने के बाद हमें दिखाई देना चाहिए:

```text
AudixTechnology
      │
      └── AUDIX-SBI
```

और project के अंदर:

```text
AUDIX-SBI
│
├── Overview
├── Boards
├── Repos
├── Pipelines
├── Test Plans
└── Artifacts
```

---

# 🧪 अब बाकी Clients

SBI successfully create होने के बाद इसी pattern से:

### HDFC

```text
AUDIX-HDFC
```

### TCS

```text
AUDIX-TCS
```

### Capgemini

```text
AUDIX-CAPGEMINI
```

### Bandhan Bank

```text
AUDIX-BANDHAN
```

---

# 📊 Expected Final Project Structure

```text
🏢 AudixTechnology
│
├── 📁 AUDIX-SBI
│
├── 📁 AUDIX-HDFC
│
├── 📁 AUDIX-TCS
│
├── 📁 AUDIX-CAPGEMINI
│
└── 📁 AUDIX-BANDHAN
```

---

# 🔐 Practical Security Rule

अभी हम:

```text
Project
   ↓
Repository
   ↓
Pipeline
```

बनाएँगे।

लेकिन Azure access अभी नहीं देंगे।

Azure access बाद में:

```text
Pipeline
    ↓
Service Connection
    ↓
Entra Identity
    ↓
Azure RBAC
    ↓
Specific Resource Group
```

से controlled तरीके से देंगे।

इससे pipeline को पूरे Azure subscription का unnecessary access नहीं मिलेगा।

---

# 🚫 अभी क्या नहीं करना है

इस step में अभी:

```text
❌ Azure Resource Group
❌ VNet
❌ Subnet
❌ Service Connection
❌ App Registration
❌ Service Principal
❌ Pipeline
❌ Terraform
❌ Production Environment
```

नहीं बनाना है।

अभी सिर्फ **Azure DevOps Projects**।

---

# 📁 GitHub Lab Structure

इस practical lab में धीरे-धीरे structure बनेगा:

```text
11-audix-client-industrial-lab/
│
├── 01-organization-setup.md
├── 02-project-setup.md
│
├── 03-repository-setup.md
├── 04-branch-policy.md
├── 05-environments.md
├── 06-azure-rbac.md
├── 07-service-connections.md
├── 08-terraform-structure.md
├── 09-sbi-infrastructure.md
├── 10-pipeline.md
├── 11-security.md
├── 12-governance.md
├── 13-monitoring.md
└── 14-troubleshooting.md
```

---

# ✅ Expected Result

इस step के बाद Azure DevOps में:

```text
🏢 AudixTechnology
│
├── AUDIX-SBI
├── AUDIX-HDFC
├── AUDIX-TCS
├── AUDIX-CAPGEMINI
└── AUDIX-BANDHAN
```

ये पाँच अलग-अलग **Private Projects** होने चाहिए।

और GitHub में:

```text
11-audix-client-industrial-lab/
└── 02-project-setup.md
```

होना चाहिए।

---

# 🏁 Step Complete

हमने अभी सिर्फ:

```text
AUDIX Organization
       ↓
Client Projects
```

की actual foundation बनाई।

अगला practical step:

```text
AUDIX-SBI
     ↓
Create Repository
     ↓
infra-sbi
     ↓
main branch
     ↓
Branch Policy
```

यानी अब हम **actual Terraform repository** बनाना शुरू करेंगे।
