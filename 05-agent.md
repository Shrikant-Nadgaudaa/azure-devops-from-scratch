# 🤖 Phase 05 — Azure DevOps Agent

<p align="center">

![Azure DevOps](https://img.shields.io/badge/Azure%20DevOps-Agent-blue?logo=azuredevops)
![CI/CD](https://img.shields.io/badge/CI%2FCD-Automation-green)
![Microsoft](https://img.shields.io/badge/Microsoft-Hosted-blue?logo=microsoft)
![Self Hosted](https://img.shields.io/badge/Self--Hosted-Agent-orange)

</p>

---

# 🎯 Objective

इस phase में हम समझेंगे:

* 🤖 Agent क्या है?
* ❓ Agent की जरूरत क्यों है?
* 🔄 Pipeline और Agent में क्या difference है?
* 🏊 Agent Pool क्या है?
* ☁️ Microsoft-hosted Agent क्या है?
* 🖥️ Self-hosted Agent क्या है?
* ⚙️ Pipeline Job Agent तक कैसे पहुँचती है?
* 🧑‍💻 Agent पर commands कहाँ execute होती हैं?
* 🔐 Agent और Service Connection का क्या relation है?
* 🏗️ Terraform pipeline में Agent का role क्या है?

सबसे पहले एक line याद रखो:

> **Pipeline बताती है कि क्या करना है, Agent उस काम को execute करता है।**

---

# 🧠 1. Agent क्या है?

Simple language में:

> **Azure DevOps Agent एक execution machine है जिस पर Pipeline के Jobs और Steps execute होते हैं।**

मान लो Pipeline में हमने लिखा:

```yaml
steps:
- script: echo "Hello Azure DevOps"
```

अब सवाल:

> यह `echo` command चलेगी कहाँ?

Answer:

```text id="h8r6e7"
Pipeline
    │
    ▼
   Job
    │
    ▼
  Agent
    │
    ▼
Command Execute
```

Agent ही वह execution environment है जहाँ command वास्तव में run होती है।

---

# 🏭 2. Real-Life Example

मान लो एक factory है।

```text id="b8ccqa"
🏭 Factory
│
├── 📋 Work Order
│
├── 👷 Worker
│
└── 🛠️ Tools
```

यहाँ:

```text
Work Order = Pipeline
Worker     = Agent
Task       = Job/Step
```

Pipeline कहती है:

> "यह काम करो।"

Agent कहता है:

> "ठीक है, मैं इसे execute करता हूँ।"

---

# 🔄 3. Pipeline vs Agent

सबसे important difference:

| Pipeline                          | Agent                               |
| --------------------------------- | ----------------------------------- |
| Automation workflow               | Execution machine                   |
| क्या करना है define करती है       | काम execute करता है                 |
| YAML में define हो सकती है        | Machine/environment provide करता है |
| Stages/Jobs/Steps contain करती है | Jobs/Steps execute करता है          |
| Instructions                      | Executor                            |

Memory trick:

```text id="o0g1ru"
Pipeline = WHAT
Agent    = WHERE IT RUNS
```

---

# 🔥 4. Complete Flow

```text id="1a9k0b"
👨‍💻 Developer
      │
      ▼
📦 Git Repository
      │
      ▼
🔄 Pipeline
      │
      ▼
⚙️ Job
      │
      ▼
🏊 Agent Pool
      │
      ▼
🤖 Agent
      │
      ▼
📝 Steps
      │
      ▼
💻 Commands Execute
```

---

# 🏊 5. Agent Pool क्या है?

अब एक नया concept:

> **Agent Pool = Agents का logical collection/group**

Example:

```text id="m3gh4w"
🏊 Agent Pool
│
├── 🤖 Agent-01
├── 🤖 Agent-02
└── 🤖 Agent-03
```

Pipeline कह सकती है:

```text id="7bq6t0"
मुझे इस Agent Pool से execution चाहिए।
```

फिर available agent job execute कर सकता है।

---

# 🧠 6. Agent Pool क्यों चाहिए?

मान लो Audix की 100 pipelines हैं।

हर pipeline के लिए अलग machine manage करना practical नहीं हो सकता।

इसलिए agents को pool में organize किया जा सकता है।

```text id="cr5x9s"
                 🏊 Agent Pool
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
      🤖 Agent-01 🤖 Agent-02 🤖 Agent-03
```

Pipeline jobs available agents पर execute हो सकते हैं।

---

# ☁️ 7. Microsoft-hosted Agent

Azure DevOps में Microsoft-hosted agents available होते हैं।

Conceptually:

```text id="x1gqci"
Azure DevOps
      │
      ▼
Microsoft-hosted Agent
      │
      ▼
Pipeline Job
```

आपको underlying machine को manually maintain नहीं करना पड़ता।

---

# 🧹 8. Microsoft-hosted Agent का Basic Idea

मान लो हमें Ubuntu environment चाहिए।

Pipeline में:

```yaml
pool:
  vmImage: ubuntu-latest
```

अब pipeline Microsoft-hosted environment पर job execute कर सकती है।

Conceptually:

```text id="11ck1r"
Pipeline
   │
   ▼
Microsoft-hosted Agent
   │
   ├── OS
   ├── Tools
   ├── Runtime
   └── Build Environment
```

इसमें उपलब्ध tools/image configuration platform द्वारा managed होती है और समय के साथ बदल सकती है।

---

# 🖥️ 9. Self-hosted Agent

अब दूसरा option:

> **Self-hosted Agent = जिस machine को organization खुद manage करती है और जिस पर Azure DevOps Agent software install किया जाता है।**

Example:

```text id="2u8w3a"
🏢 Audix
    │
    ▼
🖥️ Audix VM
    │
    ▼
🤖 Azure DevOps Agent
    │
    ▼
Pipeline Jobs
```

यह machine हो सकती है:

* Azure VM
* On-premises Server
* Physical Machine
* Cloud VM
* अन्य supported compute environment

---

# 🆚 10. Microsoft-hosted vs Self-hosted

| Feature                | Microsoft-hosted                  | Self-hosted                          |
| ---------------------- | --------------------------------- | ------------------------------------ |
| Machine management     | Microsoft side                    | Organization                         |
| OS maintenance         | Microsoft side                    | Organization                         |
| Agent setup            | Less infrastructure management    | Organization installs/manages agent  |
| Custom software        | Image/tool availability पर depend | High control                         |
| Private network access | Architecture dependent            | Useful when agent has network access |
| Maintenance            | Lower                             | Higher                               |
| Customization          | More limited                      | More control                         |

---

# 🧠 11. कब Microsoft-hosted Agent?

अगर हमें simple CI/CD चाहिए:

```text id="0k0hlj"
Code
 ↓
Build
 ↓
Test
 ↓
Deploy
```

और special network/tool requirements नहीं हैं, तो Microsoft-hosted agent convenient हो सकता है।

---

# 🏢 12. कब Self-hosted Agent?

मान लो Audix के पास internal application है:

```text id="8yq6yo"
Corporate Network
       │
       ├── Internal Git/Service
       ├── Internal Database
       └── Internal Application
```

Pipeline को internal network resource तक पहुँचना है।

ऐसे architecture में self-hosted agent उपयोगी हो सकता है क्योंकि agent को required network connectivity दी जा सकती है।

लेकिन इसका मतलब यह नहीं कि self-hosted agent automatically secure है।

Security, patching, access control और network design organization की responsibility होती है।

---

# 🔐 13. Agent और Service Connection अलग हैं

यह बहुत important है।

```text id="x8x8n3"
🤖 Agent
   │
   │ Executes Commands
   ▼
Terraform / Azure CLI / Scripts
```

और:

```text id="m6j2u7"
🔐 Service Connection
   │
   │ Authentication / Authorization Path
   ▼
☁️ Azure
```

दोनों का role अलग है।

Complete flow:

```text id="8l2k3a"
Pipeline
   │
   ▼
Agent
   │
   ▼
Terraform
   │
   ▼
Service Connection / Identity
   │
   ▼
Azure
```

---

# 🏗️ 14. Terraform में Agent का Role

मान लो हमारा repository:

```text id="2zq7e3"
Terraform-Repo
│
├── main.tf
├── variables.tf
├── outputs.tf
└── azure-pipelines.yml
```

Pipeline में:

```yaml
steps:
- script: terraform init

- script: terraform validate

- script: terraform plan
```

अब ये commands कहाँ चलेंगी?

```text id="q5r3m0"
Azure DevOps Pipeline
        │
        ▼
       Job
        │
        ▼
      🤖 Agent
        │
        ├── terraform init
        ├── terraform validate
        └── terraform plan
```

Agent के execution environment में Terraform उपलब्ध/installed होना चाहिए।

---

# 🚀 15. Terraform Apply Flow

अब:

```text id="v0i8qr"
terraform apply
```

का flow:

```text id="k9w3x4"
Pipeline
   │
   ▼
Agent
   │
   ▼
terraform apply
   │
   ▼
Azure Authentication
   │
   ▼
Azure API
   │
   ▼
Azure Resource
```

इसलिए:

> Terraform code repository में होना और Terraform command execute होना दो अलग चीजें हैं।

---

# 🔬 16. Agent के अंदर क्या होता है?

Conceptually:

```text id="2f8j17"
🤖 Agent
│
├── Workspace
│
├── Source Code
│
├── Tools
│   ├── Git
│   ├── Terraform
│   ├── Azure CLI
│   └── Runtime
│
├── Environment Variables
│
└── Pipeline Job
```

जब job शुरू होती है तो source code checkout हो सकता है और फिर defined steps execute होते हैं।

---

# 📥 17. Repository से Agent तक Code कैसे आता है?

Basic flow:

```text id="jv3r8u"
📦 Repository
      │
      │ Checkout
      ▼
🤖 Agent Workspace
      │
      ▼
📝 Pipeline Steps
```

Example:

```text id="b8f8k6"
main.tf
variables.tf
outputs.tf
```

Agent के workspace में उपलब्ध हो सकते हैं।

फिर:

```text id="6m0y4d"
Agent Workspace
      │
      ▼
terraform init
      │
      ▼
terraform validate
      │
      ▼
terraform plan
```

---

# 🧹 18. Microsoft-hosted Agent को Persistent Server मत समझना

यह important concept है।

Microsoft-hosted execution environment को generally **ephemeral** तरीके से समझना चाहिए।

मतलब:

```text id="gq4r4h"
Job Start
   ↓
Agent Environment
   ↓
Job Execute
   ↓
Job Finish
   ↓
Environment Released
```

इसलिए pipeline को जरूरी data को proper external storage/artifact/state systems में रखना चाहिए।

उदाहरण:

```text id="wdt4nz"
❌ Agent local disk = Long-term storage

✅ Artifact / Repository / Terraform Backend
   = Appropriate persistent location
```

---

# 🗂️ 19. Agent Workspace

Pipeline execution के दौरान agent पर workspace बनता है।

Conceptually:

```text id="r8b1c9"
🤖 Agent
   │
   ▼
📁 Workspace
   │
   ├── Source Code
   ├── Build Files
   ├── Temporary Files
   └── Output
```

Job के दौरान commands इसी working environment में execute हो सकती हैं।

---

# 🔄 20. Multiple Jobs

एक Pipeline में multiple jobs हो सकते हैं।

```text id="6e1y0r"
Pipeline
│
├── Job 1 → Build
│
├── Job 2 → Test
│
└── Job 3 → Deploy
```

हर job की execution requirements अलग हो सकती हैं।

Conceptually:

```text id="b3u1l5"
Pipeline
   │
   ├──── Job 1 ────► Agent
   │
   ├──── Job 2 ────► Agent
   │
   └──── Job 3 ────► Agent
```

Exact scheduling और execution behavior pipeline configuration और agent availability पर depend करता है।

---

# 🧪 21. Simple YAML Example

एक basic example:

```yaml
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:

- script: echo "Build started"
  displayName: "Build"

- script: echo "Running tests"
  displayName: "Test"

- script: echo "Deployment step"
  displayName: "Deploy"
```

Flow:

```text id="i9s7d2"
Push to main
     ↓
Pipeline Trigger
     ↓
Ubuntu Agent
     ↓
Build
     ↓
Test
     ↓
Deploy
```

---

# 🔥 22. Terraform YAML Example

Conceptually:

```yaml
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:

- script: terraform init
  displayName: "Terraform Init"

- script: terraform validate
  displayName: "Terraform Validate"

- script: terraform plan
  displayName: "Terraform Plan"
```

Flow:

```text id="4x9h3a"
Git Push
   ↓
Pipeline
   ↓
Ubuntu Agent
   ↓
Terraform Init
   ↓
Terraform Validate
   ↓
Terraform Plan
```

Production deployment में authentication, approvals, state backend, variables/secrets और environment controls को भी properly design करना पड़ता है।

---

# 🏦 23. Audix Industrial Scenario

मान लो Audix के पास:

```text id="t6c5l2"
🏢 Audix DevOps
       │
       ▼
📁 SBI Project
       │
       ▼
📦 Terraform Repository
```

अब SBI infrastructure pipeline चलती है।

```text id="f8s4q2"
Developer
   ↓
Git Push
   ↓
Pipeline
   ↓
Job
   ↓
Agent
   ↓
Terraform
   ↓
Service Connection
   ↓
Azure
```

अगर Microsoft-hosted agent use हो रहा है:

```text id="4p1m5u"
Azure DevOps
      │
      ▼
Microsoft-hosted Agent
      │
      ▼
Terraform
      │
      ▼
Azure
```

अगर self-hosted agent:

```text id="4s5k7r"
Azure DevOps
      │
      ▼
Audix Self-hosted Agent
      │
      ▼
Terraform
      │
      ▼
Azure
```

---

# 🚨 24. Common Mistakes

## ❌ Mistake 1

> Pipeline ही command execute करती है।

### ✅ Correct

```text id="t2g7k4"
Pipeline
   ↓
Job
   ↓
Agent
   ↓
Command Execution
```

---

## ❌ Mistake 2

> Agent और Service Connection एक ही चीज हैं।

### ✅ Correct

```text id="x4n2k8"
Agent
 ↓
Execution

Service Connection
 ↓
Authentication / Authorization configuration
```

---

## ❌ Mistake 3

> Agent हमेशा एक permanent server होता है।

### ✅ Correct

Microsoft-hosted agents को generally ephemeral execution environments की तरह समझें।

Self-hosted agents persistent machines हो सकते हैं, जिन्हें organization maintain करती है।

---

## ❌ Mistake 4

> Agent के local disk पर Terraform state permanently रख सकते हैं।

### ✅ Correct

Terraform state को appropriate remote backend में रखना चाहिए, जैसे Azure Storage backend, ताकि state centralized और persistent रहे।

---

## ❌ Mistake 5

> Self-hosted Agent मतलब automatically secure Agent।

### ✅ Correct

Self-hosted agent की:

* OS patching
* Network security
* Identity permissions
* Secrets handling
* Access control
* Monitoring

organization को manage करना पड़ता है।

---

# 🆚 25. Quick Comparison

| Concept               | Meaning                                 |
| --------------------- | --------------------------------------- |
| 🔄 Pipeline           | Automation workflow                     |
| ⚙️ Job                | Work unit                               |
| 📝 Step               | Individual action                       |
| 🤖 Agent              | Execution environment                   |
| 🏊 Agent Pool         | Agents का group                         |
| ☁️ Microsoft-hosted   | Microsoft-managed execution environment |
| 🖥️ Self-hosted       | Organization-managed agent              |
| 🔐 Service Connection | External/Azure access configuration     |
| 📦 Artifact           | Build/deployment output                 |
| ☁️ Azure              | Target cloud platform                   |

---

# 🧠 26. One-Line Memory Trick

```text id="r7v5y8"
Pipeline = क्या करना है
      ↓
Job = कौन सा काम
      ↓
Step = कौन सा action
      ↓
Agent = कहाँ execute करना है
      ↓
Service Connection = Azure से authenticate/access कैसे करना है
```

---

# 🏁 27. Final Architecture

```text id="2j9y4m"
                  👨‍💻 Developer
                       │
                       ▼
                  📦 Git Repo
                       │
                       ▼
                 🔄 Pipeline
                       │
                       ▼
                     ⚙️ Job
                       │
                       ▼
                 🏊 Agent Pool
                       │
                       ▼
                    🤖 Agent
                       │
                       ▼
                📝 Pipeline Steps
                       │
              ┌────────┼────────┐
              ▼        ▼        ▼
          Build       Test    Terraform
                                │
                                ▼
                       🔐 Service Connection
                                │
                                ▼
                             ☁️ Azure
                                │
                                ▼
                         🏗️ Azure Resources
```

---

# 🎯 Interview Questions

### Q1. What is an Azure DevOps Agent?

**Answer:**

Azure DevOps Agent is the execution environment that runs pipeline jobs and steps.

---

### Q2. What is the difference between Pipeline and Agent?

**Answer:**

Pipeline defines the automation workflow, while the Agent provides the environment where pipeline jobs and commands are executed.

---

### Q3. What is an Agent Pool?

**Answer:**

An Agent Pool is a logical collection of agents that can be used to execute pipeline jobs.

---

### Q4. What is a Microsoft-hosted Agent?

**Answer:**

It is an Azure DevOps-hosted execution environment where the underlying infrastructure is managed by Microsoft.

---

### Q5. What is a Self-hosted Agent?

**Answer:**

It is an agent installed and managed by the organization on its own supported machine or compute environment.

---

### Q6. Where does `terraform plan` execute?

**Answer:**

It executes on the Agent assigned to the pipeline job.

---

### Q7. Does the Agent itself provide Azure permissions?

**Answer:**

No. Execution and Azure authorization are separate concerns. The pipeline can use an appropriate identity/authentication mechanism such as a Service Connection to access Azure.

---

# 🚀 Next Phase

अब हमें पता है:

```text
Pipeline
   ↓
Agent
   ↓
Command Execution
```

लेकिन अभी एक बड़ा सवाल बाकी है:

> **Pipeline Azure को पहचानती कैसे है और Azure में resource create/update करने की permission कहाँ से आती है?**

उसके लिए अगला topic है:

```text
06-service-connection.md
```

और उसमें हम समझेंगे:

```text
🔐 Service Connection
       ↓
Authentication
       ↓
Authorization
       ↓
Microsoft Entra ID
       ↓
Workload Identity Federation
       ↓
Azure RBAC
       ↓
Resource Group / Resource
```

---

