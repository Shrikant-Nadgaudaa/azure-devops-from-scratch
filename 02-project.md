# 📁 Azure DevOps Project — From Zero to Real-World Understanding

<p align="center">

<img src="https://img.shields.io/badge/Azure%20DevOps-Project-blue?logo=azuredevops" alt="Azure DevOps">

<img src="https://img.shields.io/badge/Level-Beginner-green" alt="Beginner">

<img src="https://img.shields.io/badge/Focus-Project%20Architecture-orange" alt="Project Architecture">

</p>

---

# 🎯 इस Chapter का Goal

पिछले chapter में हमने सीखा:

```text
🏢 Azure DevOps Organization
          │
          ▼
       📁 Project
```

अब हमारा पूरा focus **Project** पर है।

इस chapter के बाद हमें यह समझ आना चाहिए:

* Project क्या है?
* Organization के अंदर Project क्यों चाहिए?
* Project और Organization में difference क्या है?
* Project कब बनाना चाहिए?
* एक Organization में कितने Projects हो सकते हैं?
* एक Project में क्या-क्या होता है?
* एक Project में कितने Repositories हो सकते हैं?
* एक Project में कितनी Pipelines हो सकती हैं?
* Project और Azure Subscription का क्या relation है?
* Project और Azure Resource Group का क्या relation है?
* हमारे 4 clients — SBI, HDFC, ICICI और Shriram — के लिए Projects कैसे design कर सकते हैं?
* Client-wise और application-wise Project design में क्या difference है?
* Project-level security क्यों important है?
* Project को "application" समझना सही है या नहीं?
* Project और Environment में क्या difference है?

सबसे important:

> **Project को सिर्फ Azure DevOps Portal में एक folder की तरह नहीं समझना है।**

हम इसे एक **logical DevOps working boundary** के रूप में समझेंगे।

---

# 🧠 सबसे पहले — Project क्या है?

बहुत आसान भाषा में:

> **Azure DevOps Project एक logical working area है जहाँ किसी product, application, client, team या related DevOps work को organize किया जाता है।**

Organization हमारा बड़ा workspace था।

अब Organization के अंदर हम अलग-अलग कामों को अलग करना चाहते हैं।

यहीं Project आता है।

---

# 🏢 Organization → Project

सबसे पहले basic hierarchy:

```text
┌──────────────────────────────────────────┐
│        🏢 Azure DevOps Organization      │
│                                          │
│   ┌──────────────────────────────────┐   │
│   │        📁 Project                │   │
│   │                                  │   │
│   │  📦 Repository                   │   │
│   │  🔄 Pipeline                     │   │
│   │  📋 Boards                       │   │
│   │  🧪 Test Plans                   │   │
│   │  🌍 Environments                 │   │
│   │  📦 Artifacts                    │   │
│   │                                  │   │
│   └──────────────────────────────────┘   │
│                                          │
│   ┌──────────────────────────────────┐   │
│   │        📁 Project                │   │
│   │                                  │   │
│   │  📦 Repository                   │   │
│   │  🔄 Pipeline                     │   │
│   │                                  │   │
│   └──────────────────────────────────┘   │
│                                          │
└──────────────────────────────────────────┘
```

यानी:

```text
Organization
     │
     ├── Project-01
     ├── Project-02
     ├── Project-03
     └── Project-04
```

---

# 🧒 बच्चों वाला Example

मान लो हमारे पास एक बड़ा school है।

```text
🏫 SCHOOL
    │
    ├── Class 1
    ├── Class 2
    ├── Class 3
    └── Class 4
```

यहाँ:

```text
School = Organization
Class  = Project
```

अब Class के अंदर students और activities होंगी।

वैसे ही Project के अंदर DevOps work होगा:

```text
📁 Project
   │
   ├── 📦 Repository
   ├── 🔄 Pipeline
   ├── 📋 Boards
   ├── 🌍 Environment
   └── 📦 Artifacts
```

---

# 🤔 Project की जरूरत क्यों है?

मान लो हमारे पास एक Organization है:

```text
🏢 Audix-DevOps
```

और Audix चार clients के लिए काम करता है:

```text
SBI
HDFC
ICICI
Shriram
```

अगर Project concept न हो और सब कुछ एक जगह डाल दिया जाए:

```text
Audix-DevOps
│
├── SBI Repo
├── HDFC Repo
├── ICICI Repo
├── Shriram Repo
├── SBI Pipeline
├── HDFC Pipeline
├── ICICI Pipeline
├── Shriram Pipeline
├── SBI Board
├── HDFC Board
├── ICICI Board
└── ...
```

बहुत जल्दी confusion हो जाएगा।

इसलिए:

```text
🏢 Audix-DevOps
       │
       ├── 📁 SBI Project
       ├── 📁 HDFC Project
       ├── 📁 ICICI Project
       └── 📁 Shriram Project
```

अब हर client का DevOps work logically अलग हो गया।

---

# 🧩 Project को एक "Working Area" समझो

Project को ऐसे visualize करो:

```text
┌─────────────────────────────────────────────┐
│              📁 SBI PROJECT                 │
│                                             │
│  👥 Teams                                   │
│  📦 Repositories                            │
│  🔄 Pipelines                               │
│  📋 Boards                                  │
│  🧪 Test Plans                              │
│  🌍 Environments                            │
│  📦 Artifacts                               │
│  🔐 Project Permissions                     │
│                                             │
└─────────────────────────────────────────────┘
```

इसीलिए Project को केवल:

> "एक folder"

समझना incomplete है।

यह एक **DevOps working boundary** है।

---

# 🔥 Organization और Project का सबसे आसान Difference

याद रखने के लिए:

```text
🏢 Organization
      │
      │  "पूरा DevOps Workspace"
      ▼
📁 Project
      │
      │  "Specific Work Area"
      ▼
📦 Repository
      │
      │  "Source Code"
      ▼
🔄 Pipeline
      │
      │  "Automation"
      ▼
🚀 Deployment
```

### Short memory:

> **Organization = पूरा घर**

> **Project = घर का एक कमरा**

> **Repository = उस कमरे की अलमारी जिसमें code है**

> **Pipeline = उस code पर काम करने वाली automation**

यह सिर्फ memory trick है; वास्तविक Azure DevOps model इससे अधिक capabilities रखता है।

---

# 🏗️ Project के अंदर क्या-क्या हो सकता है?

एक Azure DevOps Project में कई DevOps capabilities इस्तेमाल की जा सकती हैं।

High-level:

```text
📁 PROJECT
   │
   ├── 📦 Repos
   │
   ├── 🔄 Pipelines
   │
   ├── 📋 Boards
   │
   ├── 🧪 Test Plans
   │
   ├── 📦 Artifacts
   │
   ├── 🌍 Environments
   │
   └── 🔐 Permissions
```

अब एक-एक करके समझते हैं।

---

# 📦 1. Repositories

Repository में source code और Git history रहती है।

Example:

```text
📁 SBI Project
      │
      ├── 📦 frontend-repo
      ├── 📦 backend-repo
      └── 📦 infrastructure-repo
```

Repository का काम:

```text
Code
 ↓
Commit
 ↓
Branch
 ↓
Pull Request
 ↓
Merge
```

Repository को हम अगले chapter में बहुत deep समझेंगे।

---

# 🔄 2. Pipelines

Pipeline automation का workflow है।

उदाहरण:

```text
Code Push
    │
    ▼
Pipeline Trigger
    │
    ▼
Build
    │
    ▼
Test
    │
    ▼
Package
    │
    ▼
Deploy
```

एक Project में multiple pipelines हो सकती हैं।

Example:

```text
📁 SBI Project
      │
      ├── 🔄 Frontend-CI
      ├── 🔄 Backend-CI
      ├── 🔄 Terraform-CI
      └── 🚀 Production-Deployment
```

---

# 📋 3. Boards

Boards development work को track करने के लिए use किए जा सकते हैं।

Example:

```text
📋 SBI Project Board

To Do
   │
   ▼
In Progress
   │
   ▼
Testing
   │
   ▼
Done
```

Work Items जैसे:

```text
Epic
Feature
User Story
Task
Bug
```

आदि को manage किया जा सकता है।

---

# 🧪 4. Test Plans

Testing activities को organize/manage करने के लिए Test Plans capability इस्तेमाल की जा सकती है।

Example:

```text
Application
     │
     ▼
Test Cases
     │
     ▼
Test Execution
     │
     ▼
Test Result
```

हर Project में यह capability जरूरी हो ऐसा नहीं है।

---

# 🌍 5. Environments

यह बहुत important concept है।

ADO Environment को Azure Environment समझना गलत हो सकता है।

हम Project में logical deployment environments represent कर सकते हैं:

```text
📁 Project
    │
    ├── 🌍 DEV
    ├── 🌍 UAT
    ├── 🌍 PRE-PROD
    └── 🌍 PROD
```

Pipeline deployment:

```text
Build
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

इन environments के साथ approvals और deployment controls जैसी capabilities जोड़ी जा सकती हैं।

---

# 📦 6. Artifacts

Build के बाद जो deployable output मिलता है, उसे broadly artifact कहा जा सकता है।

Example:

```text
Source Code
    │
    ▼
Build
    │
    ▼
Application Package
    │
    ▼
Artifact
    │
    ▼
Deployment
```

Example:

```text
.NET Application
      ↓
Build
      ↓
.zip / package
      ↓
Artifact
```

Artifact concept को Pipeline chapter में detail में समझेंगे।

---

# 🔐 7. Project Permissions

Project एक security boundary भी provide कर सकता है।

Conceptually:

```text
User / Group
      │
      ▼
Project
      │
      ▼
Permissions
      │
      ▼
Allowed Actions
```

Example:

```text
SBI Team
   │
   ▼
SBI Project
   │
   ├── Repo Access
   ├── Pipeline Access
   └── Board Access
```

यहाँ एक important बात:

> **Azure DevOps Project permissions और Azure Resource RBAC एक ही permission system नहीं हैं।**

Azure Resource access अलग Azure RBAC model से control होता है।

---

# 🏦 अब हमारे 4 Clients का Real Example

अब सबसे important part।

हमारे चार clients:

```text
🏦 SBI
🏦 HDFC
🏦 ICICI
🏦 Shriram
```

हमारे पास एक central DevOps Organization है:

```text
🏢 Audix-DevOps
```

अब Projects:

```text
🏢 Audix-DevOps
       │
       ├── 📁 SBI Project
       │
       ├── 📁 HDFC Project
       │
       ├── 📁 ICICI Project
       │
       └── 📁 Shriram Project
```

---

# 🏦 SBI Project

मान लो SBI के लिए हमारे पास:

```text
📁 SBI Project
     │
     ├── 📦 frontend
     ├── 📦 backend
     ├── 📦 infrastructure
     │
     ├── 🔄 frontend-CI
     ├── 🔄 backend-CI
     ├── 🔄 infrastructure-CI
     │
     ├── 🌍 DEV
     ├── 🌍 UAT
     └── 🌍 PROD
```

अब पूरा project एक logical working area बन गया।

---

# 🏦 HDFC Project

```text
📁 HDFC Project
     │
     ├── 📦 frontend
     ├── 📦 backend
     ├── 📦 infrastructure
     │
     ├── 🔄 CI
     ├── 🔄 CD
     │
     ├── 🌍 DEV
     ├── 🌍 UAT
     └── 🌍 PROD
```

---

# 🏦 ICICI Project

```text
📁 ICICI Project
     │
     ├── 📦 frontend
     ├── 📦 backend
     ├── 📦 terraform
     │
     ├── 🔄 Application-CI
     ├── 🔄 Infrastructure-CI
     └── 🚀 Deployment
```

---

# 🏦 Shriram Project

```text
📁 Shriram Project
     │
     ├── 📦 application
     ├── 📦 infrastructure
     │
     ├── 🔄 Build
     └── 🚀 Deployment
```

---

# 🔥 पूरा Client Architecture

अब चारों को एक साथ देख:

```text
┌─────────────────────────────────────────────────────┐
│            🏢 AUDIX AZURE DEVOPS                    │
│                 ORGANIZATION                        │
│                                                     │
│  ┌────────────────┐   ┌────────────────┐           │
│  │ 📁 SBI PROJECT │   │ 📁 HDFC PROJECT│           │
│  │                │   │                │           │
│  │ 📦 Repos       │   │ 📦 Repos       │           │
│  │ 🔄 Pipelines   │   │ 🔄 Pipelines   │           │
│  │ 🌍 Environments│   │ 🌍 Environments│           │
│  └────────────────┘   └────────────────┘           │
│                                                     │
│  ┌────────────────┐   ┌────────────────┐           │
│  │📁 ICICI PROJECT│   │📁 SHRIRAM      │           │
│  │                │   │   PROJECT      │           │
│  │ 📦 Repos       │   │ 📦 Repos       │           │
│  │ 🔄 Pipelines   │   │ 🔄 Pipelines   │           │
│  │ 🌍 Environments│   │ 🌍 Environments│           │
│  └────────────────┘   └────────────────┘           │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

# ☁️ Azure Side भी जोड़ते हैं

अब Audix के Azure architecture को साथ में रखो:

```text
                       🏢 AUDIX
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
        ☁️ AZURE                    🧑‍💻 ADO
             │                           │
             ▼                           ▼
   Management Group              Organization
             │                           │
      ┌──────┼──────┐              ┌─────┼─────┐
      │      │      │              │     │     │
      ▼      ▼      ▼              ▼     ▼     ▼
     SBI    HDFC   ICICI          SBI   HDFC  ICICI
     Sub    Sub     Sub         Project Project Project
      │      │      │              │     │     │
      ▼      ▼      ▼              ▼     ▼     ▼
     RGs    RGs     RGs           Repos Repos Repos
      │      │      │              │     │     │
      ▼      ▼      ▼              ▼     ▼     ▼
 Resources Resources Resources   Pipelines Pipelines
```

Shriram भी इसी model में जुड़ता है।

---

# ⚠️ एक बहुत important बात

Project और Azure Resource Group को एक जैसा मत समझना।

दोनों का नाम "group" जैसा लग सकता है, लेकिन purpose अलग है।

---

# ☁️ Azure Resource Group

Resource Group:

```text
Azure Subscription
      │
      ▼
Resource Group
      │
      ├── VM
      ├── VNet
      ├── NSG
      └── Storage
```

यह **Azure resources को organize/manage करने वाला container** है।

---

# 📁 Azure DevOps Project

Project:

```text
Azure DevOps Organization
          │
          ▼
Project
          │
          ├── Repository
          ├── Pipeline
          ├── Board
          └── Environment
```

यह **DevOps work को organize करने वाला logical boundary** है।

---

# 🔥 दोनों को Side-by-Side देखो

| Azure            | Azure DevOps                                          |
| ---------------- | ----------------------------------------------------- |
| Management Group | Organization                                          |
| Subscription     | Project boundary/context, but not a direct equivalent |
| Resource Group   | Project के बराबर नहीं                                 |
| Azure Resource   | DevOps resource के बराबर direct mapping नहीं          |
| VM               | Deployment target                                     |
| AKS              | Deployment target                                     |
| App Service      | Deployment target                                     |

सबसे important:

> **Azure DevOps Project का exact Azure-side equivalent नहीं है।**

हमें सिर्फ concept समझने के लिए उन्हें side-by-side देखना चाहिए।

---

# 🤔 क्या एक Project में Multiple Repositories हो सकती हैं?

हाँ।

Example:

```text
📁 SBI Project
     │
     ├── 📦 frontend-repo
     ├── 📦 backend-repo
     ├── 📦 terraform-repo
     ├── 📦 documentation-repo
     └── 📦 automation-repo
```

यह useful हो सकता है जब अलग components का lifecycle या ownership अलग हो।

---

# 🤔 क्या एक Project में Multiple Pipelines हो सकती हैं?

हाँ।

Example:

```text
📁 SBI Project
     │
     ├── 📦 frontend-repo
     ├── 📦 backend-repo
     └── 📦 terraform-repo

     Pipelines:
     │
     ├── 🔄 Frontend-CI
     ├── 🔄 Backend-CI
     ├── 🔄 Terraform-Plan
     └── 🚀 Terraform-Apply
```

एक Project को एक ही Pipeline तक सीमित नहीं करना है।

---

# 🤔 क्या एक Repository में Multiple Pipelines हो सकती हैं?

हाँ, architecture के हिसाब से एक repository से multiple pipelines बनाई जा सकती हैं।

उदाहरण:

```text
Repository
    │
    ├── CI Pipeline
    ├── Security Scan Pipeline
    └── Deployment Pipeline
```

इसलिए:

```text
Project
  │
  ├── Repository
  │      ├── Pipeline
  │      └── Pipeline
  │
  └── Repository
         └── Pipeline
```

---

# 🧠 Project = Application?

यह भी एक common misunderstanding है।

जरूरी नहीं।

Project represent कर सकता है:

```text
Client
Product
Application
Team
Platform
Business Unit
```

लेकिन कौन सा model use करना है, यह organization की जरूरतों पर depend करता है।

---

# 🏗️ Project Design के अलग-अलग Models

अब architecture थोड़ा deep करते हैं।

## Model 1 — Client-wise Projects

```text
Organization
     │
     ├── SBI Project
     ├── HDFC Project
     ├── ICICI Project
     └── Shriram Project
```

Useful जब client-level separation महत्वपूर्ण हो।

---

# Model 2 — Application-wise Projects

मान लो SBI के multiple applications हैं:

```text
Organization
     │
     ├── SBI-CBS
     ├── SBI-Payments
     ├── SBI-Analytics
     └── SBI-Internet-Banking
```

यह product/application-centric organization हो सकती है।

---

# Model 3 — Platform-wise Project

Example:

```text
Organization
     │
     ├── Application-Platform
     ├── Infrastructure-Platform
     └── Security-Platform
```

यह अलग operating model में useful हो सकता है।

---

# ⚖️ तो Project कैसे decide करें?

कोई universal rule नहीं:

```text
1 Client = 1 Project
```

ऐसा mandatory नहीं है।

Project boundary decide करते समय देख सकते हैं:

```text
Business Boundary
       +
Team Boundary
       +
Access / Permission Boundary
       +
Application Lifecycle
       +
Governance
       +
Reporting
       +
Operational Ownership
```

इन factors के आधार पर Project structure design किया जा सकता है।

---

# 🔐 Project और Access Isolation

मान लो:

```text
SBI Team
   │
   ▼
SBI Project
```

और:

```text
HDFC Team
   │
   ▼
HDFC Project
```

अब Project boundaries हमें work और permissions को logically separate करने में मदद कर सकती हैं।

Concept:

```text
             Organization
                  │
       ┌──────────┴──────────┐
       │                     │
       ▼                     ▼
   SBI Project          HDFC Project
       │                     │
       ▼                     ▼
    SBI Team             HDFC Team
```

इससे एक central Organization के अंदर अलग client working areas बनाए जा सकते हैं।

Actual permissions को हमेशा organization's configured security model और required access के अनुसार verify करना चाहिए।

---

# 🔗 Project से Azure कैसे जुड़ता है?

यहाँ सबसे important bridge आता है।

```text
📁 Azure DevOps Project
          │
          ▼
      🔄 Pipeline
          │
          ▼
   🔐 Service Connection
          │
          ▼
    Azure Identity
          │
          ▼
        RBAC
          │
          ▼
Azure Subscription / RG / Resource
```

उदाहरण:

```text
SBI Project
    │
    ▼
SBI Deployment Pipeline
    │
    ▼
SBI Azure Service Connection
    │
    ▼
SBI Azure Subscription
    │
    ▼
SBI Resource Group
    │
    ▼
SBI Application
```

यह architecture बाद में Service Connection chapter में deep होगा।

---

# 🧠 एक Important Question

### क्या Azure DevOps Project के अंदर Azure Resources रखे जाते हैं?

**नहीं।**

Project में:

```text
Repos
Pipelines
Boards
Environments
Artifacts
```

जैसे DevOps objects होते हैं।

Azure resources अलग Azure side पर रहते हैं:

```text
Subscription
   │
   ▼
Resource Group
   │
   ├── VM
   ├── AKS
   ├── App Service
   └── Storage
```

Pipeline दोनों worlds के बीच automation connection बनाती है।

---

# 🌉 पूरा Bridge

अब पूरा flow:

```text
┌───────────────────────┐
│   Azure DevOps        │
│                       │
│   🏢 Organization     │
│          │            │
│          ▼            │
│      📁 Project       │
│          │            │
│          ▼            │
│      📦 Repository    │
│          │            │
│          ▼            │
│      🔄 Pipeline      │
└──────────┬────────────┘
           │
           ▼
    🔐 Service Connection
           │
           ▼
      ☁️ Azure Identity
           │
           ▼
          RBAC
           │
           ▼
┌───────────────────────┐
│       Azure           │
│                       │
│   Subscription        │
│       │               │
│       ▼               │
│   Resource Group      │
│       │               │
│       ▼               │
│     Resource          │
└───────────────────────┘
```

---

# 🧒 एक और Simple Real-Life Example

मान लो Audix एक बड़ी company है।

```text
🏢 AUDIX OFFICE
```

Office के अंदर अलग departments:

```text
Office
 │
 ├── SBI Team
 ├── HDFC Team
 ├── ICICI Team
 └── Shriram Team
```

Azure DevOps में:

```text
🏢 Organization
 │
 ├── SBI Project
 ├── HDFC Project
 ├── ICICI Project
 └── Shriram Project
```

अब SBI department के अंदर:

```text
SBI Project
 │
 ├── Developers
 ├── Repositories
 ├── Pipelines
 ├── Testing
 └── Deployment
```

बस यही core concept है।

---

# 🧠 Organization → Project → Repository

तीनों को कभी mix मत करना।

```text
🏢 ORGANIZATION
        │
        │
        │  "पूरा DevOps workspace"
        ▼
📁 PROJECT
        │
        │
        │  "Specific work area"
        ▼
📦 REPOSITORY
        │
        │
        │  "Source code + Git history"
        ▼
🌿 BRANCH
        │
        ▼
📝 COMMIT
```

---

# 🔥 हमारे 4 Client का Complete DevOps View

```text
                         🏢 AUDIX DEVOPS
                           ORGANIZATION
                                │
            ┌───────────────────┼───────────────────┐
            │                   │                   │
            ▼                   ▼                   ▼
      📁 SBI Project      📁 HDFC Project      📁 ICICI Project
            │                   │                   │
       ┌────┼────┐         ┌────┼────┐         ┌────┼────┐
       ▼    ▼    ▼         ▼    ▼    ▼         ▼    ▼    ▼
      Repo Repo Repo       Repo Repo Repo       Repo Repo Repo
       │                   │                   │
       ▼                   ▼                   ▼
    Pipelines           Pipelines           Pipelines
       │                   │                   │
       └───────────┬───────┴───────────┬───────┘
                   │                   │
                   ▼                   ▼
             Service Connections
                   │
                   ▼
                 AZURE
```

और:

```text
                         📁 Shriram Project
                                  │
                         ┌────────┴────────┐
                         ▼                 ▼
                       Repos            Pipelines
                                           │
                                           ▼
                                   Service Connection
                                           │
                                           ▼
                                         Azure
```

---

# 📊 Organization vs Project vs Repository

| Feature           | Organization                | Project                      | Repository                |
| ----------------- | --------------------------- | ---------------------------- | ------------------------- |
| Main purpose      | DevOps top-level boundary   | Specific working area        | Source code management    |
| Contains          | Projects                    | Repos, Pipelines, Boards आदि | Code + Git history        |
| Git code          | ❌                           | Through Repos                | ✅                         |
| Pipeline          | Indirectly                  | ✅                            | Pipeline can consume repo |
| Boards            | Through Projects            | ✅                            | ❌                         |
| Azure Resource    | ❌                           | ❌                            | ❌                         |
| Deployment target | ❌                           | ❌                            | ❌                         |
| Security boundary | Organization-level controls | Project-level controls       | Repo-level controls       |
| Example           | Audix-DevOps                | SBI Project                  | SBI-Backend Repo          |

---

# ⚠️ Common Mistakes

## ❌ Mistake 1

Organization को Subscription समझना।

```text
Organization ≠ Subscription
```

---

## ❌ Mistake 2

Project को Resource Group समझना।

```text
Project ≠ Resource Group
```

---

## ❌ Mistake 3

Project में VM रखा है ऐसा समझना।

गलत।

VM Azure में है:

```text
Subscription
   ↓
Resource Group
   ↓
VM
```

Project में deployment automation हो सकती है:

```text
Project
   ↓
Pipeline
   ↓
Service Connection
   ↓
Azure VM
```

---

## ❌ Mistake 4

एक Project = एक Repository

जरूरी नहीं।

```text
Project
 │
 ├── Repo-01
 ├── Repo-02
 ├── Repo-03
 └── Repo-04
```

---

## ❌ Mistake 5

एक Project = एक Pipeline

जरूरी नहीं।

```text
Project
 │
 ├── CI Pipeline
 ├── Security Pipeline
 ├── Infrastructure Pipeline
 └── Deployment Pipeline
```

---

# 🧠 Interview Question

### ❓ Azure DevOps Project क्या है?

**Answer:**

> Azure DevOps Project एक logical working boundary है जिसके अंदर किसी application, product, client, team या related DevOps work के लिए Repositories, Pipelines, Boards, Environments, Artifacts और related permissions जैसी capabilities organize की जा सकती हैं।

---

# 🧠 Interview Question

### ❓ Organization और Project में क्या difference है?

**Answer:**

```text
Organization
     ↓
Top-level DevOps workspace/boundary

Project
     ↓
Organization के अंदर specific working area
```

---

# 🧠 Interview Question

### ❓ क्या एक Organization में multiple Projects हो सकते हैं?

हाँ।

```text
Organization
 │
 ├── Project-01
 ├── Project-02
 ├── Project-03
 └── Project-04
```

---

# 🧠 Interview Question

### ❓ क्या एक Project में multiple Repositories हो सकती हैं?

हाँ।

```text
Project
 │
 ├── frontend
 ├── backend
 └── infrastructure
```

---

# 🧠 Interview Question

### ❓ क्या Project Azure Resource Group के अंदर होता है?

नहीं।

दोनों अलग platforms और purposes के concepts हैं।

```text
Azure:
Subscription
   ↓
Resource Group
   ↓
Azure Resources


Azure DevOps:
Organization
   ↓
Project
   ↓
DevOps Resources
```

---

# 🎯 Final Mental Model

अगर आज की पूरी chapter को सिर्फ एक diagram में याद रखना हो:

```text
                         🏢 AUDIX
                            │
             ┌──────────────┴──────────────┐
             │                             │
             ▼                             ▼
        ☁️ AZURE                    🧑‍💻 AZURE DEVOPS
             │                             │
             ▼                             ▼
   Management Group                 Organization
             │                             │
             ▼                             ▼
       Subscription                    Project
             │                             │
             ▼                             ▼
      Resource Group                  Repository
             │                             │
             ▼                             ▼
       Azure Resource                  Pipeline
                                           │
                                           ▼
                                      Agent / Build
                                           │
                                           ▼
                                         Artifact
                                           │
                                           ▼
                                    Service Connection
                                           │
                                           ▼
                                         Azure
                                           │
                                           ▼
                                      Deployment
```

---

# 🏁 What We Learned

आज हमने समझा:

* 📁 Project क्या है
* 🏢 Organization और Project का relationship
* 🎯 Project क्यों चाहिए
* 📦 Project में Repositories
* 🔄 Project में Pipelines
* 📋 Project में Boards
* 🧪 Test Plans
* 🌍 Environments
* 📦 Artifacts
* 🔐 Project-level permissions
* 🏦 हमारे 4 clients के लिए Project structure
* ☁️ Project और Azure Subscription का difference
* 📦 Project और Resource Group का difference
* 🔗 Project से Azure तक Service Connection का bridge
* 🧩 Client-wise, Application-wise और Platform-wise Project models
* ⚠️ Common Project design mistakes

---

# 🚀 Next Chapter

अब हमारी hierarchy:

```text
🏢 Organization
       │
       ▼
📁 Project
       │
       ▼
📦 Repository
```

अब अगला सवाल naturally आता है:

> **Repository क्या है? इसमें code कैसे आता है? Branch क्या है? Commit क्या है? Pipeline Repository से code कैसे लेती है? और Repository Agent पर कैसे पहुँचती है?**

यही हमारा अगला chapter होगा:

```text
📄 03-repository.md
```

और वहाँ हम Git को भी इस Azure DevOps architecture के साथ connect करेंगे।

---

# 🔥 Complete Journey

```text
🏢 Organization
      │
      ▼
📁 Project
      │
      ▼
📦 Repository
      │
      ▼
🌿 Branch
      │
      ▼
📝 Commit
      │
      ▼
🔄 Pipeline
      │
      ▼
🤖 Agent
      │
      ▼
🏗️ Build
      │
      ▼
🧪 Test
      │
      ▼
📦 Artifact
      │
      ▼
🔐 Service Connection
      │
      ▼
☁️ Azure
      │
      ▼
🚀 Deployment
```

> **Organization हमें workspace देता है।
> Project हमें working boundary देता है।
> Repository हमें source code देता है।
> Pipeline उस code को automate करती है।
> Azure application को run करने का target देता है।**

---
