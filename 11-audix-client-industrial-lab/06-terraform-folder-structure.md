# 🏗️ STEP 06 — Terraform Folder Structure

> 🚀 **Practical Industrial Lab — Parent Module + Child Modules**

अब हमारा naming convention lock हो चुका है।

इस step में हम Terraform project का **actual industrial folder structure** define करेंगे।

अभी Azure में कोई infrastructure create नहीं करेंगे।

---

# 🎯 हमारा Target

SBI के लिए हमारा Terraform code इस structure में रहेगा:

```text
infra-sbi/
│
├── README.md
├── .gitignore
│
├── terraform/
│
│   ├── versions.tf
│   ├── providers.tf
│   ├── variables.tf
│   ├── locals.tf
│   ├── main.tf
│   ├── outputs.tf
│   ├── terraform.tfvars
│   │
│   └── modules/
│       │
│       ├── resource-group/
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       │
│       ├── vnet/
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   └── outputs.tf
│       │
│       └── subnet/
│           ├── main.tf
│           ├── variables.tf
│           └── outputs.tf
│
└── pipelines/
    └── sbi-infrastructure.yml
```

---

# 🧠 Root Module क्या है?

हमारा:

```text
terraform/
```

folder **Root Module** होगा।

इसमें:

```text
terraform/
├── versions.tf
├── providers.tf
├── variables.tf
├── locals.tf
├── main.tf
└── outputs.tf
```

रहेंगे।

Terraform command हम इसी directory से चलाएँगे:

```text
terraform/
```

Example:

```powershell
cd terraform
terraform init
terraform validate
terraform plan
```

---

# 🧩 Child Modules

हम reusable infrastructure components को अलग-अलग modules में रखेंगे:

```text
modules/
│
├── resource-group/
├── vnet/
└── subnet/
```

इनका काम:

| Module           | Responsibility        |
| ---------------- | --------------------- |
| `resource-group` | Azure Resource Group  |
| `vnet`           | Azure Virtual Network |
| `subnet`         | Azure Subnet          |

---

# 🔗 Parent → Child Relationship

हमारा Root Module:

```text
terraform/main.tf
```

Child Modules को call करेगा:

```text
Root Module
     │
     ├── Resource Group Module
     │
     ├── VNet Module
     │
     └── Subnet Module
```

Detailed flow:

```text
                    ROOT MODULE
                 terraform/main.tf
                         │
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
    resource-group     vnet          subnet
       module          module         module
          │              │              │
          ▼              ▼              ▼
       Azure RG        VNet          Subnet
```

---

# 🔄 Module Dependency

Infrastructure dependency:

```text
Resource Group
      ↓
     VNet
      ↓
    Subnet
```

Terraform में इसे module outputs और inputs के through connect करेंगे।

Example flow:

```text
Resource Group Module
        │
        │ resource_group_name
        ▼
VNet Module
        │
        │ vnet_id
        ▼
Subnet Module
```

इससे Terraform resource dependency automatically understand कर सकेगा।

---

# 📁 Root Module Files

## `versions.tf`

Terraform और provider requirements define करने के लिए।

```text
terraform/
└── versions.tf
```

---

## `providers.tf`

AzureRM Provider configuration के लिए।

```text
terraform/
└── providers.tf
```

हम बाद में यहाँ:

```text
azurerm
```

provider configure करेंगे।

---

## `variables.tf`

Reusable input variables:

```text
client
environment
region
location
tags
```

जैसी values के लिए।

---

## `locals.tf`

Naming और calculated values centralize करने के लिए।

Example:

```text
client + environment + region
```

से:

```text
rg-audix-sbi-dev-eus
```

जैसा name generate किया जा सकेगा।

---

## `main.tf`

यह हमारा primary composition file होगा।

यहीं से child modules call होंगे:

```text
main.tf
   │
   ├── module.resource_group
   ├── module.vnet
   └── module.subnet
```

---

## `outputs.tf`

Infrastructure के useful outputs expose करने के लिए।

उदाहरण:

```text
Resource Group Name
VNet ID
Subnet ID
```

---

## `terraform.tfvars`

Environment-specific input values रखने के लिए।

उदाहरण:

```text
client
environment
region
location
```

⚠️ इसमें secrets नहीं रखेंगे।

---

# 🧩 Child Module Structure

हर child module का basic structure:

```text
module-name/
│
├── main.tf
├── variables.tf
└── outputs.tf
```

---

# 📦 Resource Group Module

```text
modules/
└── resource-group/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

Responsibility:

```text
Input
 ↓
Resource Group Name
 ↓
Azure Resource Group
 ↓
Output
```

---

# 🌐 VNet Module

```text
modules/
└── vnet/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

Responsibility:

```text
Input
 ↓
VNet Name
 ↓
Address Space
 ↓
Azure VNet
 ↓
Output VNet ID
```

---

# 🔌 Subnet Module

```text
modules/
└── subnet/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

Responsibility:

```text
Input
 ↓
VNet ID
 ↓
Subnet Name
 ↓
Subnet Address Prefix
 ↓
Azure Subnet
```

---

# ⚙️ Pipeline Folder

Pipeline YAML अलग folder में रहेगा:

```text
pipelines/
└── sbi-infrastructure.yml
```

आगे pipeline:

```text
Git
 ↓
Azure DevOps Pipeline
 ↓
Terraform
 ↓
Plan
 ↓
Approval
 ↓
Apply
```

कराएगी।

---

# 🏗️ Complete Architecture

```text
                         AUDIX-SBI
                            │
                     ┌──────┴──────┐
                     │   infra-sbi │
                     └──────┬──────┘
                            │
             ┌──────────────┴──────────────┐
             │                             │
             ▼                             ▼
        terraform/                     pipelines/
             │                             │
             │                             └── sbi-infrastructure.yml
             │
      ┌──────┴────────┐
      │               │
      ▼               ▼
Root Module       modules/
                      │
          ┌───────────┼───────────┐
          │           │           │
          ▼           ▼           ▼
       RG Module   VNet Module  Subnet Module
          │           │           │
          ▼           ▼           ▼
       Azure RG      VNet       Subnet
```

---

# 🔐 Security Rule

Terraform structure में:

```text
❌ Password
❌ Client Secret
❌ Subscription Secret
❌ Storage Account Key
❌ Terraform State
```

Git में commit नहीं करेंगे।

Authentication आगे:

```text
Azure DevOps
      ↓
Workload Identity Federation
      ↓
Microsoft Entra ID
      ↓
Azure RBAC
```

से करेंगे।

---

# 🌍 Environment Strategy

हम अभी अलग-अलग copied Terraform code नहीं बनाएँगे:

```text
❌ terraform-dev/
❌ terraform-uat/
❌ terraform-prod/
```

इसके बजाय reusable modules रखेंगे:

```text
modules/
├── resource-group/
├── vnet/
└── subnet/
```

और inputs के आधार पर environment-specific resources generate होंगे।

Example:

```text
DEV
 ↓
rg-audix-sbi-dev-eus

UAT
 ↓
rg-audix-sbi-uat-eus

PROD
 ↓
rg-audix-sbi-prod-eus
```

---

# 🔁 Future Client Reuse

आज:

```text
SBI
 ↓
infra-sbi
```

कल वही architecture:

```text
HDFC
 ↓
infra-hdfc
```

फिर:

```text
TCS
CAPGEMINI
BANDHAN
```

के लिए भी reusable Terraform module pattern रहेगा।

---

# 🚫 अभी क्या नहीं करना है

इस step में:

```text
❌ Azure Resource Group
❌ VNet
❌ Subnet
❌ Terraform Provider configuration
❌ Service Connection
❌ Pipeline
❌ terraform apply
```

नहीं करना है।

अभी सिर्फ architecture/documentation lock करना है।

---

# 📁 GitHub Documentation

अब हमारे lab docs:

```text
11-audix-client-industrial-lab/
│
├── 01-organization-setup.md
├── 02-project-setup.md
├── 03-repository-setup.md
├── 04-environments.md
├── 05-naming-convention.md
└── 06-terraform-folder-structure.md
```

---

# ✅ Expected Result

इस step के बाद हमें पता होना चाहिए:

```text
Root Module
    │
    └── terraform/
          │
          ├── providers.tf
          ├── variables.tf
          ├── locals.tf
          ├── main.tf
          └── modules/
                │
                ├── resource-group/
                ├── vnet/
                └── subnet/
```

और:

```text
Pipeline
    │
    └── pipelines/
          └── sbi-infrastructure.yml
```

---

# 🏁 Step Complete

हमारा Terraform architecture अब clear है:

```text
Parent / Root Module
        │
        ├── Resource Group Child Module
        │
        ├── VNet Child Module
        │
        └── Subnet Child Module
```

अगले step में पहली बार **actual Terraform code** लिखेंगे:

```text
07
 ↓
versions.tf
 ↓
providers.tf
 ↓
AzureRM Provider
 ↓
terraform init
```

इसके बाद सीधे पहला **Resource Group Child Module** बनाएँगे।
