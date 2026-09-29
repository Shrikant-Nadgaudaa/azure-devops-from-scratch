## 📁 04-pipeline.md

```text

इसमें हम Pipeline को zero से industrial level तक समझेंगे:

📦 Repository
      ↓
🔄 Pipeline
      ↓
📄 YAML
      ↓
🤖 Agent
      ↓
⚙️ Job
      ↓
📝 Steps / Tasks
      ↓
🔨 Build
      ↓
🧪 Test
      ↓
📦 Artifact
      ↓
🔐 Service Connection
      ↓
☁️ Azure
```

# 🔄 Phase 04 — Azure DevOps Pipeline

<p align="center">

![Azure DevOps](https://img.shields.io/badge/Azure%20DevOps-Pipeline-blue?logo=azuredevops)
![CI/CD](https://img.shields.io/badge/CI%2FCD-Automation-green)
![YAML](https://img.shields.io/badge/YAML-Pipeline-orange)
![Azure](https://img.shields.io/badge/Azure-Cloud-blue?logo=microsoftazure)

</p>

---

# 🎯 Objective

इस phase में हम समझेंगे कि **Azure DevOps Pipeline क्या है, इसकी जरूरत क्यों पड़ती है, Pipeline के अंदर क्या होता है और यह code से लेकर Azure deployment तक कैसे काम करती है।**

हमारा goal सिर्फ Pipeline की definition याद करना नहीं है।

हम यह समझेंगे:

```text
WHY → WHAT → HOW → INDUSTRIAL USE CASE
```

---

# 🧠 1. Pipeline क्या है?

Simple language में:

> **Pipeline एक automated workflow है जो software/infrastructure के repetitive tasks को automatically execute करती है।**

मान लो developer ने code repository में push किया।

पहले manual process कुछ ऐसा हो सकता है:

```text
Developer
   ↓
Code
   ↓
Build manually
   ↓
Test manually
   ↓
Package manually
   ↓
Azure login
   ↓
Deploy manually
```

यह process बार-बार करने पर:

* Time लगता है
* Human error हो सकता है
* Steps miss हो सकते हैं
* Production deployment inconsistent हो सकता है

Pipeline इस काम को automate करती है।

```text
Developer
   ↓
Git Push
   ↓
Pipeline
   ↓
Build
   ↓
Test
   ↓
Package
   ↓
Deploy
```

---

# 🏭 2. Real-Life Industrial Example

मान लो Audix के पास SBI की application है।

Developer ने application में नया feature बनाया।

Developer:

```text
feature/login
```

branch पर काम करता है।

फिर:

```text
git add .
git commit
git push
```

करता है।

अब Azure DevOps Pipeline automatically trigger हो सकती है।

```text
👨‍💻 Developer
      ↓
🌿 Feature Branch
      ↓
💾 Commit
      ↓
📤 Push
      ↓
📦 Azure DevOps Repository
      ↓
🔄 Pipeline Trigger
```

---

# 🔄 3. Complete Pipeline Flow

```text
┌───────────────────────────────┐
│        👨‍💻 Developer          │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│         📦 Repository          │
│                               │
│       Application Code        │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│        🔄 Pipeline             │
│                               │
│     Automation Workflow       │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│         🤖 Agent               │
│                               │
│    Commands Execute Here      │
└───────────────┬───────────────┘
                │
                ▼
       ┌────────┴────────┐
       │                 │
       ▼                 ▼
   🔨 Build            🧪 Test
       │                 │
       └────────┬────────┘
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

---

# 📄 4. Pipeline के दो Common Types

Azure DevOps में Pipeline को broadly दो तरीकों से configure किया जा सकता है:

### 1️⃣ YAML Pipeline

Configuration code के रूप में repository में रहती है।

Example:

```text
azure-pipelines.yml
```

### 2️⃣ Classic Pipeline

Pipeline configuration Azure DevOps UI के through बनाई जाती है।

```text
Azure DevOps Portal
        ↓
Pipeline
        ↓
GUI Configuration
```

Modern DevOps practices में YAML pipelines बहुत commonly use की जाती हैं क्योंकि pipeline configuration भी source control में रखी जा सकती है।

---

# 📄 5. YAML क्या है?

YAML का पूरा नाम:

> **YAML Ain't Markup Language**

यह एक human-readable configuration format है।

Example:

```yaml
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- script: echo "Hello Azure DevOps"
  displayName: "Run Hello World"
```

यह सिर्फ example है।

इस YAML में हम Pipeline को बताते हैं:

```text
कब चलना है?
        ↓
किस Agent पर चलना है?
        ↓
क्या commands execute करनी हैं?
```

---

# 🔥 6. YAML Pipeline का Basic Structure

एक Pipeline को इस तरह सोचो:

```text
YAML Pipeline
│
├── Trigger
│
├── Pool
│
├── Stages
│
├── Jobs
│
└── Steps
```

अब एक-एक करके समझते हैं।

---

# 🚦 7. Trigger

Trigger का मतलब:

> Pipeline कब automatically start होगी?

Example:

```yaml
trigger:
- main
```

इसका मतलब:

```text
Developer
    ↓
Push to main
    ↓
Pipeline Trigger
```

हम अलग-अलग branch strategies के अनुसार triggers configure कर सकते हैं।

---

# 🤖 8. Agent / Pool

Pipeline को commands execute करने के लिए execution environment चाहिए।

इसे Agent provide करता है।

Example:

```yaml
pool:
  vmImage: ubuntu-latest
```

Flow:

```text
Pipeline
   ↓
Agent Pool
   ↓
Agent
   ↓
Commands Execute
```

Agent का detailed topic आगे अलग phase में करेंगे।

---

# ⚙️ 9. Job

Job tasks का logical group है।

Example:

```text
Pipeline
   │
   ├── Job 1 → Build
   │
   ├── Job 2 → Test
   │
   └── Job 3 → Deploy
```

Simple memory:

> **Job = काम का एक logical group**

---

# 📝 10. Step

Job के अंदर individual steps होते हैं।

Example:

```text
Job
 │
 ├── Step 1 → Install dependencies
 │
 ├── Step 2 → Build
 │
 ├── Step 3 → Test
 │
 └── Step 4 → Package
```

Example YAML:

```yaml
steps:
- script: echo "Install dependencies"

- script: echo "Build application"

- script: echo "Run tests"
```

---

# 🧩 11. Task और Script

Pipeline में commands/tasks execute किए जा सकते हैं।

### Script

Direct command:

```yaml
- script: echo "Hello"
```

### Task

Azure DevOps का predefined task:

```yaml
- task: AzureCLI@2
```

Task specific functionality provide कर सकता है।

---

# 🏗️ 12. Stage

बड़ी Pipeline को अलग-अलग stages में divide किया जा सकता है।

Example:

```text
Pipeline
   │
   ├── 🔨 Build
   │
   ├── 🧪 Test
   │
   └── 🚀 Deploy
```

Industrial example:

```text
CI/CD Pipeline
│
├── Build Stage
│
├── Test Stage
│
├── UAT Stage
│
└── Production Stage
```

---

# 🌍 13. Environment

Environment deployment target/control boundary को represent कर सकता है।

Example:

```text
DEV
 ↓
UAT
 ↓
PRE-PROD
 ↓
PROD
```

Pipeline:

```text
Build
  ↓
Test
  ↓
DEV
  ↓
UAT
  ↓
PROD
```

Production deployment से पहले approval/checks लगाए जा सकते हैं।

---

# 📦 14. Artifact

Build के बाद deployment के लिए output produce हो सकता है।

उसे Artifact कहा जा सकता है।

Example:

```text
Source Code
     ↓
   Build
     ↓
📦 Artifact
     ↓
Deployment
```

Example:

```text
Application
     ↓
Build
     ↓
app.zip
```

या:

```text
Container Application
     ↓
Docker Build
     ↓
Docker Image
     ↓
Container Registry
```

Artifact का exact form application technology पर depend करता है।

---

# 🔐 15. Service Connection

अब Pipeline को Azure में deployment करना है।

Pipeline को Azure access चाहिए।

इसके लिए Azure DevOps में **Service Connection** configure की जाती है।

Conceptual flow:

```text
Pipeline
   ↓
Service Connection
   ↓
Azure Identity
   ↓
Azure Authorization
   ↓
Azure Resource
```

यहाँ दो concepts अलग हैं:

### Authentication

> आप कौन हैं?

### Authorization

> आपको क्या करने की permission है?

---

# 🔑 16. Service Connection + RBAC

मान लो SBI project की pipeline को सिर्फ SBI Production Resource Group पर Terraform deployment करना है।

Conceptually:

```text
Pipeline
   ↓
Service Connection
   ↓
Azure Identity
   ↓
RBAC
   ↓
SBI-Production-RG
```

अगर identity को required role/scope नहीं मिला:

```text
Pipeline
   ↓
Azure
   ↓
❌ Authorization Error
```

इसलिए Service Connection बन जाना alone पर्याप्त नहीं है।

Identity के पास target Azure resources पर आवश्यक permissions भी होनी चाहिए।

---

# 🏗️ 17. Terraform Pipeline

अब हमारे learning journey का सबसे important example।

मान लो Repository में:

```text
Terraform-Repo
│
├── main.tf
├── variables.tf
├── outputs.tf
└── azure-pipelines.yml
```

Pipeline Terraform commands execute कर सकती है:

```text
Git Push
   ↓
Pipeline
   ↓
Agent
   ↓
terraform init
   ↓
terraform validate
   ↓
terraform plan
   ↓
Approval
   ↓
terraform apply
   ↓
Azure
```

---

# 🔥 18. Terraform CI/CD Flow

```text
┌──────────────────────┐
│ 👨‍💻 Developer        │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ 📦 Azure DevOps Repo │
│                      │
│ main.tf              │
│ variables.tf         │
│ outputs.tf           │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ 🔄 Pipeline Trigger  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ 🤖 Agent             │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ terraform init       │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ terraform validate   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ terraform plan       │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ 👤 Approval / Check  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ terraform apply      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ 🔐 Service Connection│
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ ☁️ Azure             │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ 🏗️ Azure Resources   │
└──────────────────────┘
```

---

# 🏦 19. Audix Industrial Scenario

Audix के पास:

```text
🏢 Audix DevOps Organization
│
├── 📁 SBI Project
├── 📁 HDFC Project
├── 📁 ICICI Project
└── 📁 Shriram Project
```

मान लो SBI project में Terraform repository है।

```text
SBI Project
│
├── 📦 Terraform Repository
│
├── 🔄 CI Pipeline
│
└── 🚀 CD Pipeline
```

Developer Terraform code बदलता है:

```text
main.tf
```

फिर:

```text
git add .
git commit
git push
```

Pipeline शुरू:

```text
Git Push
   ↓
Pipeline
   ↓
Agent
   ↓
Terraform Init
   ↓
Terraform Validate
   ↓
Terraform Plan
```

अब plan review/approval process हो सकता है।

फिर:

```text
Approval
   ↓
Terraform Apply
   ↓
Azure
```

और Azure में:

```text
SBI-Production-RG
│
├── VNet
├── Subnet
├── NSG
├── Storage Account
└── VM
```

create/update हो सकते हैं।

---

# 🧠 20. CI और CD

Pipeline समझने के लिए CI/CD समझना जरूरी है।

## 🔨 CI — Continuous Integration

Focus:

```text
Code
 ↓
Build
 ↓
Test
```

Goal:

> Code changes को frequently integrate करके automatically validate करना।

---

## 🚀 CD — Continuous Delivery / Deployment

Focus:

```text
Validated Code
      ↓
Deployment
      ↓
Environment
```

Example:

```text
Build
 ↓
Test
 ↓
DEV
 ↓
UAT
 ↓
PROD
```

CD के exact implementation में approvals, checks और deployment strategy organization की process पर depend कर सकती है।

---

# 🆚 21. Pipeline vs Repository

| Repository               | Pipeline                        |
| ------------------------ | ------------------------------- |
| Code रखती है             | Automation चलाती है             |
| Git history रखती है      | Workflow define करती है         |
| Branches होती हैं        | Jobs/Stages होते हैं            |
| Commit होता है           | Build/Test/Deploy होता है       |
| Source of truth for code | Automation definition/execution |

Simple:

```text
Repository = Code
Pipeline   = Automation
```

---

# 🆚 22. Pipeline vs Agent

| Pipeline                    | Agent                    |
| --------------------------- | ------------------------ |
| क्या करना है define करती है | काम execute करता है      |
| Workflow                    | Execution machine        |
| YAML में define हो सकती है  | Commands execute करता है |
| Build/Test/Deploy logic     | Actual execution         |

Memory trick:

```text
Pipeline = WHAT
Agent    = WHERE/HOW IT EXECUTES
```

---

# 🆚 23. Pipeline vs Service Connection

```text
Pipeline
   │
   │ "मुझे Azure में deployment करना है"
   ▼
Service Connection
   │
   │ "यह identity/authentication configuration use करो"
   ▼
Azure
```

इसलिए:

```text
Pipeline ≠ Service Connection
```

---

# 🚨 24. Common Beginner Mistakes

### ❌ Mistake 1

Pipeline को application server समझना।

### ✅ Correct

```text
Pipeline
   ↓
Deployment Automation
   ↓
Azure Target
   ↓
Application
```

---

### ❌ Mistake 2

Agent को Pipeline समझना।

### ✅ Correct

```text
Pipeline
   ↓
Job
   ↓
Agent
   ↓
Command Execution
```

---

### ❌ Mistake 3

Service Connection को subscription समझना।

### ✅ Correct

```text
Service Connection
       ↓
Azure Authentication / Authorization Path
       ↓
Azure Resources
```

---

### ❌ Mistake 4

Terraform code repository में होने से automatically Azure में apply हो जाएगा।

### ✅ Correct

```text
Terraform Code
      ↓
Pipeline
      ↓
Terraform Commands
      ↓
Azure Authentication
      ↓
Azure
```

---

# 🎯 25. Interview Answer

अगर interviewer पूछे:

> **What is an Azure DevOps Pipeline?**

Simple answer:

> Azure DevOps Pipeline is an automation workflow used to build, test, package and deploy applications or infrastructure. It can execute jobs on agents and can interact with Azure or other services through configured authentication such as service connections.

और अगर Terraform context हो:

> Terraform code stored in an Azure DevOps repository can be validated, planned and applied through an Azure DevOps Pipeline using an execution agent and an appropriate Azure authentication mechanism.

---

# 🧠 26. One-Line Memory Map

इसे याद रखो:

```text
📦 Repo
   ↓
🔄 Pipeline
   ↓
🤖 Agent
   ↓
⚙️ Job
   ↓
📝 Steps
   ↓
🔨 Build / 🧪 Test
   ↓
📦 Artifact
   ↓
🔐 Service Connection
   ↓
☁️ Azure
   ↓
🚀 Deployment
```

---

# 🏁 What We Learned

इस phase में हमने सीखा:

* 🔄 Pipeline क्या है
* ❓ Pipeline की जरूरत क्यों है
* 📄 YAML Pipeline क्या है
* 🚦 Trigger क्या है
* 🤖 Agent क्या करता है
* ⚙️ Job क्या है
* 📝 Step क्या है
* 🏗️ Stage क्या है
* 🌍 Environment क्या है
* 📦 Artifact क्या है
* 🔐 Service Connection क्या है
* 🔑 Authentication vs Authorization
* 🔨 CI क्या है
* 🚀 CD क्या है
* 🏗️ Terraform Pipeline कैसे काम करती है
* 🏦 Industrial Audix → SBI example

---

# 🚀 Next Phase

अब Pipeline का basic concept clear है।

अगले phase में हम deep dive करेंगे:

```text
🤖 Agent
   ↓
Agent Pool
   ↓
Microsoft-hosted Agent
   ↓
Self-hosted Agent
   ↓
Agent कैसे काम करता है?
   ↓
Pipeline Job Agent तक कैसे जाता है?
   ↓
Agent पर commands कहाँ execute होती हैं?
```

**Next file:**

```text
05-agent.md
```

---

