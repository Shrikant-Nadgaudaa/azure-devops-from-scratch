# 🌿 Phase 07 — Terraform AzureRM Provider Setup

<p align="center">

![Terraform](https://img.shields.io/badge/Terraform-1.x-844FBA?logo=terraform\&logoColor=white)
![AzureRM](https://img.shields.io/badge/AzureRM-4.x-0078D4?logo=microsoftazure\&logoColor=white)
![Azure](https://img.shields.io/badge/Microsoft-Azure-0078D4?logo=microsoftazure\&logoColor=white)
![IaC](https://img.shields.io/badge/IaC-Terraform-844FBA)

</p>

---

## 🎯 Objective

इस phase में हम Terraform को Microsoft Azure के साथ काम करने के लिए `AzureRM Provider` configure करेंगे।

हमारा target है:

```text
Terraform
   │
   ▼
AzureRM Provider
   │
   ▼
Microsoft Azure
```

इस step में अभी कोई Azure Resource Group, VNet या Subnet manually create नहीं किया जाएगा।

Infrastructure आगे Terraform code से create होगी।

---

# 🏗️ Repository Separation

इस project में दो अलग repositories हैं:

```text
GitHub
│
└── azure-devops-from-scratch
       │
       └── Documentation
```

और actual infrastructure repository:

```text
Azure DevOps
│
└── AUDIX-SBI
      │
      └── infra-sbi
             │
             └── terraform/
```

GitHub repository learning/documentation के लिए है।

Azure DevOps `infra-sbi` repository actual Terraform infrastructure code के लिए है।

---

# 📁 Terraform Root Structure

इस phase में Terraform root directory के अंदर:

```text
infra-sbi/
│
└── terraform/
    ├── versions.tf
    └── providers.tf
```

---

# 1️⃣ versions.tf

`versions.tf` Terraform और required providers की version requirement define करता है।

```hcl
terraform {
  required_version = ">= 1.9.0"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
  }
}
```

---

# 2️⃣ providers.tf

AzureRM provider configuration:

```hcl
provider "azurerm" {
  features {}

  subscription_id = var.subscription_id
}
```

`features {}` AzureRM provider configuration का required block है।

---

# 3️⃣ variables.tf

Provider को Azure subscription ID देने के लिए variable define करेंगे।

```hcl
variable "subscription_id" {
  description = "Azure Subscription ID"
  type        = string
}
```

हम Subscription ID को provider code में hardcode नहीं करेंगे।

---

# 🔐 Authentication Principle

इस phase में हम Azure credentials को Terraform code में hardcode नहीं करेंगे।

Future architecture:

```text
Local Development
      │
      ▼
Azure CLI Authentication
      │
      ▼
Terraform
      │
      ▼
AzureRM Provider
```

और Azure DevOps Pipeline में:

```text
Azure DevOps Pipeline
        │
        ▼
Service Connection
        │
        ▼
Workload Identity Federation
        │
        ▼
Azure
```

---

# 🧪 Terraform Initialization

Terraform directory में जाकर:

```powershell
terraform init
```

Terraform required provider download करेगा।

Expected:

```text
Initializing the backend...
Initializing provider plugins...

- Finding hashicorp/azurerm versions matching "~> 4.0"...
- Installing hashicorp/azurerm...

Terraform has been successfully initialized!
```

---

# 🔍 Terraform Validation

इसके बाद:

```powershell
terraform validate
```

Expected:

```text
Success! The configuration is valid.
```

---

# 📦 Generated Files

`terraform init` के बाद Terraform कुछ files/directories generate करेगा:

```text
terraform/
│
├── .terraform/
├── .terraform.lock.hcl
├── versions.tf
├── providers.tf
└── variables.tf
```

`.terraform/` को Git में commit नहीं करना है।

`.terraform.lock.hcl` को normally repository में commit करना चाहिए क्योंकि यह provider dependency versions lock करता है।

---

# 🚫 Important Security Rule

Terraform state और local credentials repository में commit नहीं करने हैं।

Never commit:

```text
terraform.tfstate
terraform.tfstate.backup
.terraform/
*.tfvars
```

अगर किसी `tfvars` file में secret मौजूद हो तो उसे repository में commit नहीं करना है।

---

# ✅ Validation Checklist

इस phase के बाद:

* [x] AzureRM provider defined
* [x] Terraform version constraint defined
* [x] Azure subscription variable defined
* [x] Provider initialized
* [x] Terraform configuration validated
* [x] No Azure infrastructure manually created
* [x] No Azure credentials hardcoded

---

# 🔜 Next Phase

अगले phase में हम पहला वास्तविक Terraform Child Module बनाएँगे:

```text
🌿 Phase 08
Resource Group Child Module
```

Architecture:

```text
Root Module
     │
     ▼
Resource Group Module
     │
     ▼
Azure Resource Group
```

Example:

```text
rg-audix-sbi-dev-eus
```

Resource Group भी Portal से manually नहीं बनेगा।

Terraform ही create करेगा।


# 🛠️ PART 2 — अब Actual Terraform Repository

अब सबसे important हिस्सा। ❤️

### Azure DevOps में जाओ:

```text
Azure DevOps
   ↓
AUDIX-SBI
   ↓
Repos
   ↓
infra-sbi
   ↓
Clone
```

अगर `infra-sbi` अभी तक local machine पर clone नहीं किया है:
**Clone → HTTPS → Copy URL**
फिर अपने desired local folder में clone करो।
हम actual code इसी repository में रखेंगे।

---

# 🛠️ PART 3 — Terraform Folder बनाओ

`infra-sbi` के local folder के अंदर:

```powershell
New-Item -ItemType Directory -Path "terraform" -Force
```

फिर तीन files:

```powershell
New-Item -ItemType File -Path "terraform\versions.tf" -Force
New-Item -ItemType File -Path "terraform\providers.tf" -Force
New-Item -ItemType File -Path "terraform\variables.tf" -Force
```

VS Code:

```powershell
code terraform
```

---

# ✍️ PART 4 — `versions.tf`

`terraform/versions.tf` में:

```hcl
terraform {
  required_version = ">= 1.9.0"

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
  }
}
```

---

# ✍️ PART 5 — `providers.tf`

`terraform/providers.tf`:

```hcl
provider "azurerm" {
  features {}

  subscription_id = var.subscription_id
}
```

---

# ✍️ PART 6 — `variables.tf`

`terraform/variables.tf`:

```hcl
variable "subscription_id" {
  description = "Azure Subscription ID"
  type        = string
}
```

---

# 🧪 PART 7 — Terraform Init

अब सिर्फ Terraform directory में जाओ:

```powershell
cd terraform
```

फिर:

```powershell
terraform init
```

इसके बाद:

```powershell
terraform validate
```

---

## 🔎 अभी हमें क्या देखना है?

### `terraform init`

Expected:

```text
Terraform has been successfully initialized!
```

### `terraform validate`

Expected:

```text
Success! The configuration is valid.
```

अगर यहाँ `subscription_id` या provider related error आता है तो **आगे मत जाना**।
वहीं error को solve करेंगे।

---

# 📌 अभी Git Commit मत करो

पहले यह verify कर लो:

```text
terraform init       ✅
terraform validate   ✅
```

फिर GitHub documentation को push करेंगे:

```powershell
cd "C:\Users\ADMIN\Desktop\azure-devops-from-scratch_28.09\azure-devops-from-scratch"

git status
git add 11-audix-client-industrial-lab\07-terraform-provider.md
git commit -m "Add Terraform AzureRM Provider documentation"
git push origin main
```

### फिर actual ADO Terraform repo में भी code commit होगा — लेकिन **validation successful होने के बाद**।

---

## 🧠 आज का पूरा flow

```text
GitHub
  │
  └── 07-terraform-provider.md
           │
           └── Documentation

Azure DevOps
  │
  └── AUDIX-SBI
        │
        └── infra-sbi
              │
              └── terraform
                    ├── versions.tf
                    ├── providers.tf
                    └── variables.tf
                           │
                           ▼
                    terraform init
                           │
                           ▼
                    terraform validate
```

### ✅ हमने क्या किया?

Terraform की **AzureRM foundation** बनाई।

### ✅ क्यों किया?

ताकि अगले steps में Terraform से:

```text
Resource Group
     ↓
VNet
     ↓
Subnet
```

create कर सकें।

### ✅ Azure में अभी क्या बना?

**कुछ भी नहीं।** यही सही है। अभी केवल Terraform configuration तैयार हुई है।

### ✅ अब क्या होना चाहिए?

```text
terraform init      → SUCCESS
terraform validate  → SUCCESS
```

**भाई, पहले `terraform init` और `terraform validate` चला।** उसके बाद हम सीधे **08 — Resource Group Child Module** में जाएंगे और पहली actual Azure resource Terraform से create करने की तरफ बढ़ेंगे।
