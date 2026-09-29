# 🏷️ STEP 05 — Audix Industrial Naming Convention

> 🚀 **Practical Terraform Lab — Naming Standard**

अब से हमारे Azure infrastructure में सभी names एक standard naming convention से generate होंगे।

हम manually random names नहीं रखेंगे।

---

# 🎯 हमारा Naming Pattern

General pattern:

```text
<resource-type>-audix-<client>-<environment>-<region>
```

Example:

```text
rg-audix-sbi-dev-eus
```

इसका मतलब:

```text
rg       → Resource Group
audix    → Company
sbi      → Client
dev      → Environment
eus      → Region
```

---

# 🏢 Client Standard

हमारे clients:

| Client       | Code        |
| ------------ | ----------- |
| SBI          | `sbi`       |
| HDFC         | `hdfc`      |
| TCS          | `tcs`       |
| Capgemini    | `capgemini` |
| Bandhan Bank | `bandhan`   |

Terraform में इन्हीं standardized codes का उपयोग होगा।

---

# 🌍 Environment Standard

| Environment             | Code      |
| ----------------------- | --------- |
| Development             | `dev`     |
| User Acceptance Testing | `uat`     |
| Pre-Production          | `preprod` |
| Production              | `prod`    |

हम `pre-prod`, `pre_prod` जैसी अलग-अलग naming नहीं करेंगे।

Standard:

```text
preprod
```

---

# 🌎 Region Standard

इस lab के लिए:

```text
East US
```

का code:

```text
eus
```

रहेगा।

इसलिए:

```text
East US
    ↓
eus
```

---

# 🧱 Azure Resource Naming

## Resource Group

Pattern:

```text
rg-audix-<client>-<environment>-<region>
```

Example:

```text
rg-audix-sbi-dev-eus
```

---

## Virtual Network

Pattern:

```text
vnet-audix-<client>-<environment>-<region>
```

Example:

```text
vnet-audix-sbi-dev-eus
```

---

## Subnet

Subnet के लिए environment/client/region को नाम में repeat करना आवश्यक नहीं है क्योंकि subnet अपने VNet के अंदर scoped है।

Standard:

```text
snet-workload
```

Future में जरूरत के अनुसार:

```text
snet-app
snet-data
snet-mgmt
snet-aks
```

जैसे names use किए जा सकते हैं।

---

# 🏗️ SBI DEV Example

जब Terraform SBI DEV deploy करेगा:

```text
Azure Subscription
│
└── rg-audix-sbi-dev-eus
       │
       └── vnet-audix-sbi-dev-eus
              │
              └── snet-workload
```

---

# 🏗️ SBI UAT Example

```text
Azure Subscription
│
└── rg-audix-sbi-uat-eus
       │
       └── vnet-audix-sbi-uat-eus
              │
              └── snet-workload
```

---

# 🏗️ SBI PRE-PROD Example

```text
Azure Subscription
│
└── rg-audix-sbi-preprod-eus
       │
       └── vnet-audix-sbi-preprod-eus
              │
              └── snet-workload
```

---

# 🏗️ SBI PROD Example

```text
Azure Subscription
│
└── rg-audix-sbi-prod-eus
       │
       └── vnet-audix-sbi-prod-eus
              │
              └── snet-workload
```

---

# 🔁 Same Terraform Modules — Different Inputs

हम अलग-अलग clients के लिए अलग Terraform code copy नहीं करेंगे।

Instead:

```text
Terraform Modules
       │
       ├── Resource Group Module
       ├── VNet Module
       └── Subnet Module
```

इन modules को inputs देंगे:

```text
client
environment
region
```

और names automatically generate होंगे।

Example:

```text
client      = "sbi"
environment = "dev"
region      = "eus"
```

Terraform output naming:

```text
rg-audix-sbi-dev-eus
vnet-audix-sbi-dev-eus
```

---

# 🏷️ Standard Tags

Terraform द्वारा resources पर standard tags लगाए जाएँगे।

```hcl
tags = {
  Client             = "SBI"
  Environment        = "DEV"
  ManagedBy          = "Terraform"
  Owner              = "Audix-DevOps"
  Application        = "Infrastructure"
  DataClassification = "Internal"
}
```

---

# 🔐 Tagging Rules

हर environment में कम से कम:

```text
Client
Environment
ManagedBy
Owner
Application
DataClassification
```

maintain करेंगे।

इससे बाद में:

```text
Cost Management
Governance
Inventory
Auditing
Resource Search
```

आसान होगा।

---

# 🌳 Complete Naming Structure

```text
AUDIX
│
├── SBI
│   │
│   ├── DEV
│   │   ├── rg-audix-sbi-dev-eus
│   │   ├── vnet-audix-sbi-dev-eus
│   │   └── snet-workload
│   │
│   ├── UAT
│   │   ├── rg-audix-sbi-uat-eus
│   │   ├── vnet-audix-sbi-uat-eus
│   │   └── snet-workload
│   │
│   ├── PREPROD
│   │   ├── rg-audix-sbi-preprod-eus
│   │   ├── vnet-audix-sbi-preprod-eus
│   │   └── snet-workload
│   │
│   └── PROD
│       ├── rg-audix-sbi-prod-eus
│       ├── vnet-audix-sbi-prod-eus
│       └── snet-workload
│
├── HDFC
├── TCS
├── CAPGEMINI
└── BANDHAN
```

---

# 🚫 Naming में क्या नहीं करेंगे

```text
❌ SBI-RG
❌ MyResourceGroup
❌ test-rg
❌ resourcegroup1
❌ SBI_DEV_RG
❌ rg_Audix_SBI_DEV
```

Avoid:

```text
Spaces
Underscores
Random abbreviations
Environment inconsistency
Random numbering
```

जहाँ Azure resource type hyphenated naming support करता है, वहाँ lowercase + hyphen convention follow करेंगे।

---

# 🧠 Terraform का मुख्य फायदा

हमारा code:

```text
client = sbi
environment = dev
region = eus
```

लेकर automatically:

```text
rg-audix-sbi-dev-eus
```

generate करेगा।

फिर:

```text
client = hdfc
environment = dev
region = eus
```

से:

```text
rg-audix-hdfc-dev-eus
```

generate होगा।

इसलिए हमें हर client के लिए पूरा Terraform code copy करने की जरूरत नहीं होगी।

---

# 🏁 Naming Standard Locked

हमारा primary pattern:

```text
<resource-type>-audix-<client>-<environment>-<region>
```

और subnet/purpose resources:

```text
snet-<purpose>
```

---

# ✅ Expected Result

इस step के बाद हमें इन standards पर agree करना है:

```text
Client
    ↓
Environment
    ↓
Region
    ↓
Resource Type
    ↓
Terraform Generated Name
```

Example:

```text
SBI
 ↓
DEV
 ↓
East US
 ↓
Resource Group
 ↓
rg-audix-sbi-dev-eus
```

अब आगे के सभी Terraform resources इसी naming convention को follow करेंगे।

---

# 📁 GitHub Structure

```text
11-audix-client-industrial-lab/
│
├── 01-organization-setup.md
├── 02-project-setup.md
├── 03-repository-setup.md
├── 04-environments.md
└── 05-naming-convention.md
```

---

# 🚀 Next Step

अब naming standard lock हो गया।

अगला practical step:

```text
05 Naming Convention
        ↓
06 Terraform Folder Structure
        ↓
07 AzureRM Provider
        ↓
08 Resource Group Child Module
        ↓
09 VNet Child Module
        ↓
10 Subnet Child Module
        ↓
11 Parent / Root Module
```

इसके बाद पहली बार:

```text
terraform init
terraform validate
terraform plan
```

चलाएँगे।
