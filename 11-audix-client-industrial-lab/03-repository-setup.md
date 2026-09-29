# 📦 STEP 3 — Audix SBI Repository Setup

> 🚀 **Practical Industrial Lab — First Client Repository**

अब हमारा Azure DevOps Organization और Client Project तैयार है।

इस step में हम पहला **actual Infrastructure Repository** बनाएँगे।

---

# 🎯 हमारा Target

Azure DevOps में:

```text
🏢 AudixTechnology
       │
       └── 📁 AUDIX-SBI
                │
                └── 📦 infra-sbi
```

Repository का नाम:

```text
infra-sbi
```

---

# 🧠 Repository में क्या रहेगा?

यह repository आगे चलकर SBI infrastructure automation का complete source-code location होगा।

```text
infra-sbi
│
├── terraform/
│
├── pipelines/
│
├── environments/
│
└── README.md
```

अभी हम पूरा Terraform code नहीं डालेंगे।

पहले repository और Git workflow सही तरीके से establish करेंगे।

---

# 🖱️ GUI — Repository Create करना

Azure DevOps खोलें।

Organization:

```text
AudixTechnology
```

फिर:

```text
Projects
   ↓
AUDIX-SBI
   ↓
Repos
```

अब repository selector/dropdown पर click करें।

फिर:

```text
New repository
```

select करें।

---

# ✏️ Repository Details

### Repository type

```text
Git
```

### Repository name

```text
infra-sbi
```

### README

```text
Initialize with a README
```

इसे enable करें।

### .gitignore

अगर Azure DevOps Terraform template उपलब्ध हो:

```text
Terraform
```

select करें।

अगर Terraform option उपलब्ध नहीं है तो अभी:

```text
None
```

रहने दें।

`.gitignore` हम बाद में manually भी बना सकते हैं।

---

# 🔐 Repository Visibility

Repository उसी project की security boundary में रहेगा।

हमारा project:

```text
AUDIX-SBI
```

और repository:

```text
infra-sbi
```

internal/private enterprise code के रूप में maintain होगा।

---

# 🚀 Create

अब:

```text
Create
```

पर click करें।

---

# 🔎 Expected Repository

Creation के बाद:

```text
AUDIX-SBI
│
└── Repos
     │
     └── infra-sbi
```

और repository में कम से कम:

```text
infra-sbi
│
├── README.md
└── .gitignore
```

होना चाहिए।

---

# 🌿 Default Branch

Repository create होने के बाद default branch:

```text
main
```

होनी चाहिए।

Expected:

```text
infra-sbi
      │
      └── main
```

हम direct development को `main` पर नहीं करेंगे।

आगे feature branches use करेंगे:

```text
main
 │
 ├── feature/terraform-foundation
 ├── feature/networking
 └── feature/pipeline
```

---

# 🔐 Industrial Git Workflow

हमारा basic workflow:

```text
Developer
    │
    ▼
feature/*
    │
    ▼
Pull Request
    │
    ▼
Review
    │
    ▼
main
```

मतलब:

```text
❌ Direct push → main
```

नहीं।

बल्कि:

```text
✅ Feature Branch
       ↓
✅ Pull Request
       ↓
✅ Review
       ↓
✅ Merge
       ↓
main
```

---

# 🛡️ Main Branch Protection

Repository में:

```text
Repos
   ↓
Branches
```

जाएँ।

`main` branch के सामने:

```text
⋯
```

पर click करें।

फिर:

```text
Branch policies
```

open करें।

---

# ⚙️ Initial Branch Policy

पहले basic protection configure करेंगे।

Recommended initial settings:

```text
Require a minimum number of reviewers
        ↓
Enabled
```

Minimum reviewers:

```text
1
```

और:

```text
Allow requestors to approve their own changes
        ↓
Disabled
```

यदि आपकी organization policy अलग है तो उसके अनुसार configure करें।

---

# 🧪 Why 1 Reviewer?

हमारे lab में अभी छोटा team scenario है।

इसलिए:

```text
Developer
    ↓
Pull Request
    ↓
1 Reviewer
    ↓
main
```

enough है।

Production organization में reviewer count और approval rules business/security requirements के अनुसार रखे जा सकते हैं।

---

# 📁 Future Repository Structure

जब Terraform implementation शुरू होगी:

```text
infra-sbi/
│
├── README.md
│
├── .gitignore
│
├── terraform/
│   ├── providers.tf
│   ├── variables.tf
│   ├── terraform.tfvars
│   ├── main.tf
│   ├── outputs.tf
│   └── modules/
│
├── environments/
│   ├── dev/
│   ├── uat/
│   ├── preprod/
│   └── prod/
│
└── pipelines/
    └── sbi-infrastructure.yml
```

⚠️ **यह structure अभी पूरा create नहीं करना है।**

अभी सिर्फ repository बनाना है।

हम इसे implementation के दौरान step-by-step बनाएँगे।

---

# 🏗️ हमारा Infrastructure Flow

आगे चलकर:

```text
infra-sbi
    │
    ▼
Terraform Code
    │
    ▼
Azure DevOps Pipeline
    │
    ▼
Service Connection
    │
    ▼
Azure
    │
    ├── Resource Group
    ├── VNet
    └── Subnet
```

---

# 🔐 Security Rule

Terraform repository में कभी भी:

```text
❌ Client secrets
❌ Passwords
❌ Access keys
❌ Azure credentials
❌ Terraform state
❌ *.tfvars containing secrets
```

commit नहीं करेंगे।

Azure authentication के लिए आगे:

```text
Pipeline
   ↓
Workload Identity Federation
   ↓
Microsoft Entra ID
   ↓
Azure RBAC
```

use करेंगे।

---

# 🚫 अभी क्या नहीं करना है

इस step में:

```text
❌ Terraform code
❌ Azure Resource Group
❌ VNet
❌ Subnet
❌ Pipeline
❌ Service Connection
❌ Azure credentials
```

नहीं बनाना है।

अभी:

```text
Project
   ↓
Repository
   ↓
main protection
```

बस।

---

# 📊 Expected Final State

Azure DevOps:

```text
🏢 AudixTechnology
│
└── 📁 AUDIX-SBI
     │
     └── 📦 infra-sbi
          │
          ├── README.md
          └── .gitignore
```

Git:

```text
main
```

और:

```text
main
  🔐 Branch Policy
```

enabled होना चाहिए।

---

# 📁 GitHub Documentation

हमारे learning repository में:

```text
11-audix-client-industrial-lab/
│
├── 01-organization-setup.md
├── 02-project-setup.md
└── 03-repository-setup.md
```

अब तीन practical steps documented हैं।

---

# ✅ Verification Checklist

* [ ] `AUDIX-SBI` project exists
* [ ] `infra-sbi` repository created
* [ ] Repository initialized with README
* [ ] `.gitignore` configured/created
* [ ] `main` branch exists
* [ ] Main branch policy configured
* [ ] Direct unrestricted workflow to `main` avoided

---

# 🏁 Step Complete

हमारी पहली real client repository अब तैयार है:

```text
AUDIX-SBI
     │
     └── infra-sbi
```

अगला practical step:

```text
infra-sbi
    ↓
DEV / UAT / PRE-PROD / PROD
    ↓
Azure DevOps Environments
```

इसके बाद हम Azure RBAC और Service Connection की तरफ जाएँगे।

