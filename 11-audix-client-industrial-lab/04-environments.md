# 🌍 STEP 4 — Audix SBI Environment Setup

> 🚀 **Practical Industrial Lab — Azure DevOps Environments**

अब हमारा:

```text
🏢 Organization
      ↓
📁 Project
      ↓
📦 Repository
```

तैयार है।

अब हम **actual Azure DevOps Environments** बनाएँगे।

---

# 🎯 हमारा Target

SBI project के अंदर:

```text
AUDIX-SBI
│
├── DEV
├── UAT
├── PRE-PROD
└── PROD
```

ये चार अलग-अलग Azure DevOps Environments होंगे।

---

# 🧠 Important

Environment कोई Azure Resource Group नहीं है।

हमारे lab में:

```text
Azure DevOps
│
└── AUDIX-SBI
      │
      ├── DEV
      ├── UAT
      ├── PRE-PROD
      └── PROD
```

और Azure side पर बाद में:

```text
Azure Subscription
│
├── rg-audix-sbi-dev-...
├── rg-audix-sbi-uat-...
├── rg-audix-sbi-preprod-...
└── rg-audix-sbi-prod-...
```

दोनों अलग concepts हैं।

---

# 🖱️ STEP 1 — DEV Environment

Azure DevOps में जाएँ:

```text
AudixTechnology
   ↓
AUDIX-SBI
   ↓
Pipelines
   ↓
Environments
```

फिर:

```text
New environment
```

पर click करें।

---

# ✏️ Environment Details

### Name

```text
DEV
```

### Description

```text
SBI Development Environment
```

फिर:

```text
Create
```

click करें।

---

# 🔎 Expected Result

अब:

```text
AUDIX-SBI
   ↓
Pipelines
   ↓
Environments
```

में:

```text
DEV
```

दिखना चाहिए।

---

# 🖱️ STEP 2 — UAT

फिर:

```text
New environment
```

### Name

```text
UAT
```

### Description

```text
SBI User Acceptance Testing Environment
```

फिर:

```text
Create
```

---

# 🖱️ STEP 3 — PRE-PROD

फिर:

```text
New environment
```

### Name

```text
PRE-PROD
```

### Description

```text
SBI Pre-Production Environment
```

फिर:

```text
Create
```

---

# 🖱️ STEP 4 — PROD

फिर:

```text
New environment
```

### Name

```text
PROD
```

### Description

```text
SBI Production Environment
```

फिर:

```text
Create
```

---

# ✅ Expected Final Result

अब Azure DevOps में:

```text
AUDIX-SBI
│
└── Pipelines
      │
      └── Environments
            │
            ├── DEV
            ├── UAT
            ├── PRE-PROD
            └── PROD
```

दिखना चाहिए।

---

# 🔐 अभी Security Configuration नहीं

इस step में सिर्फ environments create करने हैं।

अभी:

```text
❌ Approval
❌ Service Connection
❌ Azure RBAC
❌ Pipeline
❌ Deployment
```

configure नहीं करना है।

ये अगले practical steps में करेंगे।

---

# 🏗️ आगे इनका काम क्या होगा?

हमारा future deployment flow:

```text
Git
 │
 ▼
Pipeline
 │
 ▼
DEV
 │
 ▼
UAT
 │
 ▼
PRE-PROD
 │
 ▼
PROD
```

लेकिन हर stage पर अलग governance/checks लगाए जा सकते हैं।

---

# 🔐 Production Governance

बाद में PROD पर हम practical controls configure करेंगे:

```text
Pipeline
   │
   ▼
PROD Environment
   │
   ├── Approval
   ├── Checks
   └── Controlled Deployment
```

इससे production deployment automatically unrestricted नहीं होगा।

---

# 📊 Environment vs Azure Resource Group

| Azure DevOps | Azure                    |
| ------------ | ------------------------ |
| DEV          | `rg-audix-sbi-dev-*`     |
| UAT          | `rg-audix-sbi-uat-*`     |
| PRE-PROD     | `rg-audix-sbi-preprod-*` |
| PROD         | `rg-audix-sbi-prod-*`    |

दोनों को pipeline बाद में connect करेगी।

Flow:

```text
Azure DevOps Environment
          │
          ▼
Pipeline
          │
          ▼
Service Connection
          │
          ▼
Azure Resource Group
```

---

# 🚫 अभी क्या नहीं करना है

अभी दूसरे clients के environments बनाने की जरूरत नहीं है।

पहले SBI complete करेंगे।

```text
❌ HDFC
❌ TCS
❌ CAPGEMINI
❌ BANDHAN
```

बाद में same pattern replicate करेंगे।

---

# 📁 GitHub Documentation

अब हमारी lab documentation:

```text
11-audix-client-industrial-lab/
│
├── 01-organization-setup.md
├── 02-project-setup.md
├── 03-repository-setup.md
└── 04-environments.md
```

---

# ✅ Verification Checklist

* [ ] `DEV` created
* [ ] `UAT` created
* [ ] `PRE-PROD` created
* [ ] `PROD` created
* [ ] सभी environments `AUDIX-SBI` project में हैं
* [ ] अभी कोई Azure deployment नहीं किया गया

---

# 🏁 Step Complete

अब हमारी SBI project foundation:

```text
🏢 AudixTechnology
       │
       └── 📁 AUDIX-SBI
              │
              ├── 📦 infra-sbi
              │
              └── 🌍 Environments
                    ├── DEV
                    ├── UAT
                    ├── PRE-PROD
                    └── PROD
```

अगला practical step:

```text
Azure RBAC
    ↓
Resource Group Scope
    ↓
WIF Service Connection
```

इसके बाद Terraform pipeline को actual Azure access मिलेगा।
