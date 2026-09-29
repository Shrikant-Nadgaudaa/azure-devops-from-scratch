# 🔄 Azure DevOps Flow — From Organization to Azure

<p align="center">

![Azure DevOps](https://img.shields.io/badge/Azure%20DevOps-Flow-blue?logo=azuredevops)
![Git](https://img.shields.io/badge/Git-Version%20Control-orange?logo=git)
![Terraform](https://img.shields.io/badge/Terraform-Infrastructure-purple?logo=terraform)
![Azure](https://img.shields.io/badge/Azure-Cloud-blue?logo=microsoftazure)

</p>

---

## 🎯 Objective

इस document का उद्देश्य समझना है कि **Azure DevOps में code लिखने से लेकर Azure में application/infrastructure deploy होने तक पूरा flow कैसे काम करता है।**

हम इस flow को एक simple industrial example से समझेंगे:

> **Audix के पास SBI का project है और SBI की application को Azure पर deploy करना है।**

---

# 🧠 1. पहले पूरा Flow एक नजर में

```text
┌──────────────────────────────┐
│      🏢 AZURE DEVOPS         │
│        ORGANIZATION           │
│        Audix DevOps           │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        📁 PROJECT             │
│          SBI Project          │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        📦 REPOSITORY          │
│       Application Code        │
│       Terraform Code          │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       🔀 GIT WORKFLOW         │
│                              │
│ Branch → Commit → Push       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       🔄 PIPELINE             │
│                              │
│ Build → Test → Package       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        🤖 AGENT               │
│                              │
│ Pipeline Jobs Execute Here   │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       📦 ARTIFACT             │
│                              │
│ Build का Output              │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│    🔐 SERVICE CONNECTION      │
│                              │
│ Azure तक Secure Access       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│          ☁️ AZURE             │
│                              │
│ Subscription / RG            │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       🏗️ TARGET              │
│                              │
│ VM / AKS / App Service       │
│ Storage / Azure Resources    │
└──────────────────────────────┘
```

---

# 🏢 2. Organization से शुरुआत

Azure DevOps में सबसे ऊपर हमारा:

```text
🏢 Organization
```

होता है।

Example:

```text
Audix DevOps Organization
```

इसके अंदर multiple Projects हो सकते हैं।

```text
🏢 Audix DevOps Organization
│
├── 📁 SBI Project
├── 📁 HDFC Project
├── 📁 ICICI Project
└── 📁 Shriram Project
```

### Organization का काम

Organization एक **top-level Azure DevOps boundary** है।

यहाँ से हम Projects, users, permissions और DevOps resources को organize करते हैं।

---

# 📁 3. Project

अब हम SBI के लिए काम कर रहे हैं।

इसलिए:

```text
🏢 Audix DevOps Organization
        │
        ▼
📁 SBI Project
```

एक Project के अंदर कई DevOps resources हो सकते हैं।

```text
📁 SBI Project
│
├── 📦 Repositories
├── 🔄 Pipelines
├── 🌍 Environments
├── 👥 Teams
├── 🔐 Permissions
└── 📊 Boards
```

---

# 📦 4. Repository

अब developer application का code लिखता है।

यह code Repository में रखा जाता है।

Example:

```text
📁 SBI Project
      │
      ▼
📦 SBI-Application-Repo
```

Repository में हो सकता है:

```text
SBI-Application-Repo
│
├── src/
├── app/
├── Dockerfile
├── README.md
└── azure-pipelines.yml
```

अगर Infrastructure as Code है:

```text
Infrastructure-Repo
│
├── main.tf
├── variables.tf
├── outputs.tf
└── terraform.tfvars
```

---

# 🌿 5. Git Workflow

Developer सीधे `main` branch पर काम करने के बजाय feature branch बना सकता है।

```text
main
 │
 ├── feature/login
 │
 ├── feature/payment
 │
 └── feature/database
```

Example:

```text
feature/login
      │
      ▼
    Code
      │
      ▼
    Commit
      │
      ▼
     Push
      │
      ▼
Pull Request
      │
      ▼
Code Review
      │
      ▼
main
```

### Simple Example

Developer ने login feature बनाया:

```text
feature/login
      │
      ▼
git add
      │
      ▼
git commit
      │
      ▼
git push
      │
      ▼
Azure DevOps Repository
```

---

# 🔄 6. Pipeline कहाँ आता है?

अब सबसे important हिस्सा:

```text
Code Repository
       │
       ▼
    Pipeline
```

Pipeline का काम है:

> **Manual काम को Automation में बदलना।**

उदाहरण:

Developer ने code push किया।

Pipeline automatically:

```text
Code
 ↓
Build
 ↓
Test
 ↓
Package
 ↓
Deploy
```

कर सकती है।

---

# 🤖 7. Agent क्या करता है?

एक common confusion:

> Pipeline खुद commands execute नहीं करती।

Pipeline के jobs को execute करने के लिए **Agent** चाहिए।

```text
┌────────────────────┐
│     PIPELINE       │
│                    │
│  Build Job         │
│  Test Job          │
│  Deploy Job        │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│       🤖 AGENT     │
│                    │
│  Executes Commands │
└────────────────────┘
```

Agent पर commands चल सकती हैं:

```text
terraform init
terraform validate
terraform plan
terraform apply
```

या application के लिए:

```text
npm install
npm test
docker build
docker push
```

---

# 📦 8. Artifact क्या है?

Build के बाद जो deployable output मिलता है, उसे broadly **Artifact** कहा जा सकता है।

Example:

```text
Source Code
    │
    ▼
  Build
    │
    ▼
┌─────────────────┐
│    Artifact     │
│                 │
│ Application     │
│ Package         │
│ Config          │
└─────────────────┘
```

Application के हिसाब से artifact अलग हो सकता है।

Example:

```text
.NET Application
      ↓
Application Package
```

या:

```text
Java Application
      ↓
.jar
```

Container application में:

```text
Source Code
     ↓
Docker Build
     ↓
Docker Image
     ↓
Azure Container Registry
```

Infrastructure के लिए Terraform में अक्सर artifact concept application build से अलग होता है; वहाँ pipeline Terraform configuration को validate/plan/apply करती है और Azure resources provision करती है।

---

# 🔐 9. Service Connection

अब Pipeline को Azure में resource create/update करना है।

लेकिन pipeline को ऐसे ही Azure access नहीं देना चाहिए।

इसलिए हम configure करते हैं:

```text
🔐 Service Connection
```

Conceptually:

```text
ADO Pipeline
     │
     │ Secure Authentication
     ▼
Service Connection
     │
     ▼
Azure Identity
     │
     ▼
Azure
```

Modern Azure DevOps setups में Azure Resource Manager service connections के लिए **Workload Identity Federation (WIF)** जैसे password/secret-less authentication approaches इस्तेमाल किए जा सकते हैं।

---

# ☁️ 10. Azure तक पहुँच

अब pipeline Azure तक पहुँच गई।

Azure hierarchy:

```text
☁️ Azure
 │
 ▼
Subscription
 │
 ▼
Resource Group
 │
 ▼
Azure Resource
```

Example:

```text
Azure Subscription
       │
       ▼
SBI-Production-RG
       │
       ├── VM
       ├── Storage Account
       ├── VNet
       └── Key Vault
```

---

# 🏗️ 11. Actual Application कहाँ Run होती है?

यह बहुत important concept है।

**Azure DevOps application को खुद host नहीं करता।**

Azure DevOps मुख्यतः:

```text
Source
 ↓
Build
 ↓
Test
 ↓
Deploy Automation
```

करता है।

Application Azure के chosen compute/service target पर run होती है।

---

## 🖥️ Option 1 — Azure VM

```text
ADO Pipeline
      │
      ▼
Azure VM
      │
      ▼
Operating System
      │
      ▼
Application
```

VM में हमें OS और application runtime manage करना पड़ सकता है।

---

## ☸️ Option 2 — AKS

अगर application containerized है:

```text
ADO Pipeline
      │
      ▼
Docker Build
      │
      ▼
Container Image
      │
      ▼
ACR
      │
      ▼
AKS
      │
      ▼
Pod
      │
      ▼
Container
      │
      ▼
Application
```

---

## 🌐 Option 3 — App Service

Managed web application के लिए:

```text
ADO Pipeline
      │
      ▼
App Service
      │
      ▼
Application
```

यहाँ Azure कई underlying infrastructure responsibilities manage करता है।

---

# 🔥 12. Terraform Infrastructure Flow

अब हमारे learning project का important use-case:

> **ADO Repository में Terraform code है और Pipeline Azure infrastructure बनाती है।**

Flow:

```text
Developer
    │
    ▼
Terraform Code
    │
    ▼
Git Repository
    │
    ▼
Azure DevOps Pipeline
    │
    ▼
Agent
    │
    ├── terraform init
    │
    ├── terraform validate
    │
    ├── terraform plan
    │
    └── terraform apply
    │
    ▼
Service Connection
    │
    ▼
Azure
    │
    ▼
Resource Group
    │
    ▼
VNet / Subnet / VM / Storage
```

---

# 🧩 13. Complete Industrial Example

मान लो Audix को SBI के लिए Azure infrastructure बनाना है।

Repository:

```text
SBI-Infrastructure
│
├── main.tf
├── variables.tf
├── outputs.tf
└── azure-pipelines.yml
```

Developer code push करता है:

```text
Developer
    │
    ▼
git push
    │
    ▼
Azure DevOps Repo
```

Pipeline trigger होती है:

```text
Repo
 │
 ▼
Pipeline
 │
 ▼
Agent
 │
 ▼
Terraform
```

Terraform Azure से बात करता है:

```text
Terraform
    │
    ▼
Service Connection
    │
    ▼
Azure Authentication
    │
    ▼
Azure Subscription
```

फिर resources create होते हैं:

```text
Azure Subscription
       │
       ▼
SBI-Production-RG
       │
       ├── VNet
       ├── Subnet
       ├── NSG
       └── VM
```

---

# 🔄 14. पूरा CI/CD Flow

अब पूरे concept को एक साथ देखो:

```text
                 👨‍💻 Developer
                       │
                       ▼
                📝 Write Code
                       │
                       ▼
                 🌿 Git Branch
                       │
                       ▼
                    Commit
                       │
                       ▼
                     Push
                       │
                       ▼
              📦 Azure DevOps Repo
                       │
                       ▼
                 🔄 Pipeline
                       │
                       ▼
                    🤖 Agent
                       │
              ┌────────┴────────┐
              ▼                 ▼
           🔨 Build           🧪 Test
              │                 │
              └────────┬────────┘
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
                🏗️ Target Resource
                       │
              ┌────────┼────────┐
              ▼        ▼        ▼
             VM       AKS    App Service
                       │
                       ▼
                  Application
```

---

# 🧠 15. सबसे Important Difference

इन सभी components को mix मत करना।

| Component             | इसका काम                             |
| --------------------- | ------------------------------------ |
| 🏢 Organization       | Top-level DevOps boundary            |
| 📁 Project            | Work / team / product boundary       |
| 📦 Repository         | Code और Git history                  |
| 🌿 Branch             | Code changes isolate करना            |
| 🔄 Pipeline           | Automation workflow                  |
| 🤖 Agent              | Pipeline commands execute करना       |
| 📦 Artifact           | Build/deployment output              |
| 🔐 Service Connection | External/Azure authentication bridge |
| ☁️ Subscription       | Azure resource/billing boundary      |
| 📦 Resource Group     | Azure resources का logical container |
| 🖥️ VM                | Virtual server                       |
| ☸️ AKS                | Kubernetes platform                  |
| 🌐 App Service        | Managed application hosting          |

---

# 🚨 16. Common Beginner Confusion

### ❌ गलत सोच

```text
Repository → Application runs
```

### ✅ सही

```text
Repository
   ↓
Pipeline
   ↓
Deployment
   ↓
Azure Target
   ↓
Application Runs
```

---

### ❌ गलत सोच

```text
Pipeline = Agent
```

### ✅ सही

```text
Pipeline
   ↓
Job
   ↓
Agent executes job
```

---

### ❌ गलत सोच

```text
Project = Resource Group
```

### ✅ सही

```text
Azure DevOps
   │
   ▼
Project

Azure
   │
   ▼
Resource Group
```

दोनों अलग systems हैं।

---

### ❌ गलत सोच

```text
Service Connection = Azure Subscription
```

### ✅ सही

```text
Service Connection
        ↓
Authentication / Authorization
        ↓
Azure Resource Access
```

---

# 🎯 17. One-Line Memory Trick

इसे याद रखो:

```text
🏢 Organization
      ↓
📁 Project
      ↓
📦 Repo
      ↓
🌿 Branch
      ↓
💾 Commit
      ↓
🔄 Pipeline
      ↓
🤖 Agent
      ↓
🔨 Build / 🧪 Test
      ↓
📦 Artifact
      ↓
🔐 Service Connection
      ↓
☁️ Azure
      ↓
🏗️ Resource
      ↓
🚀 Application
```

---

# 🏦 18. Audix Four-Client Example

हमारे scenario में:

```text
                    🏢 AUDIX DEVOPS
                     ORGANIZATION
                          │
          ┌───────────────┼────────────────┐
          │               │                │
          ▼               ▼                ▼
     📁 SBI           📁 HDFC          📁 ICICI
     PROJECT          PROJECT          PROJECT
          │               │                │
          │               │                │
          ▼               ▼                ▼
       Repo            Repo             Repo
          │               │                │
          ▼               ▼                ▼
      Pipeline         Pipeline         Pipeline
          │               │                │
          ▼               ▼                ▼
        Agent           Agent            Agent
          │               │                │
          ▼               ▼                ▼
       Azure            Azure            Azure
```

और चौथा:

```text
📁 Shriram Project
       │
       ├── Repositories
       ├── Pipelines
       ├── Environments
       ├── Teams
       └── Permissions
```

हर Project की Azure deployment architecture अलग हो सकती है।

---

# 🔐 19. Security Flow

Production environment में access को least privilege के हिसाब से design किया जाता है।

```text
Developer
    │
    ▼
Azure DevOps Project
    │
    ▼
Pipeline
    │
    ▼
Service Connection
    │
    ▼
Azure Identity
    │
    ▼
RBAC Role
    │
    ▼
Scope
    │
    ▼
Azure Resource
```

RBAC का basic model:

```text
WHO
 │
 ▼
User / Group / Identity
 │
 ▼
WHAT
 │
 ▼
Role
 │
 ▼
WHERE
 │
 ▼
Scope
```

Example scope:

```text
Subscription
     OR
Resource Group
     OR
Specific Resource
```

इससे pipeline को जरूरत से ज्यादा Azure permissions देने से बचने में मदद मिलती है।

---

# 🧪 20. Terraform Pipeline Example

एक basic Terraform pipeline logically इस तरह हो सकती है:

```text
                 Git Push
                    │
                    ▼
             Azure DevOps Repo
                    │
                    ▼
              Pipeline Trigger
                    │
                    ▼
                  Agent
                    │
                    ▼
             terraform init
                    │
                    ▼
            terraform validate
                    │
                    ▼
             terraform plan
                    │
                    ▼
              Approval
                    │
                    ▼
             terraform apply
                    │
                    ▼
                  Azure
                    │
                    ▼
              Azure Resources
```

Production में `terraform apply` से पहले approval, environment controls, state management और permission boundaries जैसी controls रखी जा सकती हैं।

---

# 📌 21. Final Mental Model

अगर interviewer पूछे:

> **"Explain Azure DevOps flow."**

तो basic answer:

```text
Developer
   ↓
Git Repository
   ↓
Pipeline
   ↓
Agent
   ↓
Build / Test
   ↓
Artifact
   ↓
Service Connection
   ↓
Azure
   ↓
Target Resource
   ↓
Application
```

और अगर Terraform Infrastructure deployment है:

```text
Developer
   ↓
Terraform Code
   ↓
Git Repository
   ↓
Azure DevOps Pipeline
   ↓
Agent
   ↓
Terraform Init
   ↓
Terraform Validate
   ↓
Terraform Plan
   ↓
Terraform Apply
   ↓
Service Connection
   ↓
Azure
   ↓
Resource Group
   ↓
Azure Resources
```

---

# 🏁 What We Learned

इस phase में हमने समझा:

* 🏢 Organization क्या है
* 📁 Project क्या है
* 📦 Repository का role
* 🌿 Git Branch / Commit / Push flow
* 🔄 Pipeline क्या करती है
* 🤖 Agent क्या करता है
* 📦 Artifact क्या है
* 🔐 Service Connection क्यों चाहिए
* ☁️ Azure तक pipeline कैसे पहुँचती है
* 🏗️ VM, AKS और App Service कहाँ आते हैं
* 🏗️ Terraform pipeline कैसे Azure infrastructure create करती है
* 🔐 Azure RBAC कहाँ apply होता है
* 🔄 Complete CI/CD flow

---

# 🚀 Next Phase

अगले phase में हम **Azure DevOps Pipeline** को practically समझेंगे:

```text
📁 Project
   ↓
📦 Repository
   ↓
🔄 Pipeline
   ↓
⚙️ YAML
   ↓
🤖 Agent
   ↓
🔐 Service Connection
   ↓
☁️ Azure
```

और फिर एक वास्तविक example बनाएँगे:

```text
Git Push
   ↓
Azure DevOps Pipeline
   ↓
Terraform
   ↓
Azure Resource Group
```

यही हमारा practical **ADO → Terraform → Azure automation** journey होगा। 🚀
