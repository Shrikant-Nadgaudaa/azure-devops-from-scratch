# 🏊 Phase 07 — Azure DevOps Agent Pool

<p align="center">

![Azure DevOps](https://img.shields.io/badge/Azure%20DevOps-Agent%20Pool-blue?logo=azuredevops)
![Agents](https://img.shields.io/badge/Agents-Multiple-orange)
![CI/CD](https://img.shields.io/badge/CI%2FCD-Parallel-green)
![Enterprise](https://img.shields.io/badge/Enterprise-Architecture-purple)

</p>

---

# 🎯 Objective

इस phase में हम समझेंगे:

* Agent Pool क्या है?
* Agent Pool क्यों चाहिए?
* एक Pool में multiple agents कैसे काम करते हैं?
* Job किस Agent पर जाती है?
* Agent busy होने पर क्या होता है?
* क्या एक Pipeline के अलग Jobs अलग Agents पर चल सकते हैं?
* Parallel Jobs क्या हैं?
* Agent Pool और Parallel Jobs में difference क्या है?
* Capabilities और Demands क्या हैं?
* अगर सही Agent available नहीं है तो क्या होता है?
* Agent Pool troubleshooting कैसे करें?
* Industrial में pools कैसे design किए जा सकते हैं?

---

# 🧠 1. Agent Pool क्या है?

Simple definition:

> **Agent Pool multiple agents का logical collection है, जहाँ से Azure DevOps pipeline jobs के लिए compatible available agent चुन सकती है।**

Example:

```text
🏊 SBI-Production-Pool
│
├── 🤖 Agent-01
├── 🤖 Agent-02
└── 🤖 Agent-03
```

---

# 🔄 2. Pipeline → Pool → Agent

```text
Pipeline
   │
   ▼
Job
   │
   ▼
Agent Pool
   │
   ▼
Available Compatible Agent
   │
   ▼
Job Execute
```

Azure Pipelines job के लिए pool से agent request करती है। Self-hosted pool में compatible agent capabilities/demands के आधार पर चुना जाता है।

---

# 🏊 3. Swimming Pool Example

Imagine:

```text
🏊 Swimming Pool
│
├── Lane 1
├── Lane 2
└── Lane 3
```

यहाँ:

```text
Pool = Agent Pool
Lane = Agent
Swimmer = Job
```

अगर तीन lanes खाली हैं:

```text
Job-01 → Agent-01
Job-02 → Agent-02
Job-03 → Agent-03
```

लेकिन:

```text
Job-04
```

आ गया तो उसे available capacity का इंतजार करना पड़ेगा।

---

# 🤖 4. Multiple Agents

Example:

```text
🏊 Agent Pool
│
├── 🤖 Agent-01
├── 🤖 Agent-02
├── 🤖 Agent-03
└── 🤖 Agent-04
```

Status:

```text
Agent-01 → 🟢 Idle
Agent-02 → 🔴 Busy
Agent-03 → 🟢 Idle
Agent-04 → 🟢 Idle
```

Job आने पर compatible idle agent को चुना जा सकता है।

---

# 🔥 5. One Agent = One Job

Important:

```text
Agent-01
   │
   └── Job-01 🟢
```

जब तक Job-01 चल रही है:

```text
Agent-01 = Busy
```

दूसरी job उसी agent पर उसी समय नहीं चलती।

Azure Pipelines agent को एक समय में एक job execute करने वाली infrastructure unit के रूप में describe करता है।

---

# ⚡ 6. तीन Jobs और तीन Agents

मान लो:

```text
Pipeline
│
├── Job-01
├── Job-02
└── Job-03
```

और:

```text
Pool
│
├── Agent-01
├── Agent-02
└── Agent-03
```

अगर jobs independent हैं और parallel capacity available है:

```text
Job-01 ─────► Agent-01
Job-02 ─────► Agent-02
Job-03 ─────► Agent-03
```

तीनों concurrently execute हो सकते हैं।

---

# 🧩 7. Jobs अलग-अलग Agents पर जा सकती हैं?

### हाँ।

Example:

```yaml
jobs:

- job: Build
  pool:
    name: Audix-Pool
  steps:
  - script: echo "Build"

- job: Test
  pool:
    name: Audix-Pool
  steps:
  - script: echo "Test"
```

Possible execution:

```text
Build
 ↓
Agent-01

Test
 ↓
Agent-02
```

यह exact assignment guaranteed नहीं है; Azure DevOps available compatible agent select करता है।

---

# 🔗 8. Jobs Dependent हों तो?

Example:

```text
Build
  ↓
Test
  ↓
Deploy
```

अगर:

```yaml
dependsOn:
- Build
```

तो dependency के कारण Test Build के completion के बाद चलेगा।

यह जरूरी नहीं कि:

```text
Build → Agent-01
Test  → Agent-01
```

बल्कि हो सकता है:

```text
Build → Agent-01
Test  → Agent-02
```

अगर दोनों agents compatible हैं।

---

# 🧠 9. Important Concept — Workspace

अगर Job-01:

```text
Agent-01
   ↓
Build Output
```

और Job-02:

```text
Agent-02
```

पर चलता है, तो Agent-01 का local filesystem Agent-02 को automatically available नहीं होता।

इसलिए jobs के बीच data transfer के लिए:

```text
📦 Pipeline Artifact
```

या अन्य appropriate shared storage इस्तेमाल किया जाता है।

यह बहुत important है।

```text
Agent-01
   │
   ▼
Build
   │
   ▼
📦 Artifact
   │
   ▼
Agent-02
   │
   ▼
Test / Deploy
```

---

# ⚡ 10. Parallel Jobs क्या है?

Parallel Jobs organization-level execution capacity है।

Example:

```text
Parallel Jobs = 2
```

तो:

```text
Job-01 → 🟢 Running
Job-02 → 🟢 Running
Job-03 → 🟡 Waiting
```

जब Job-01 complete:

```text
Job-03 → 🟢 Running
```

Azure DevOps Services में parallel job capacity organization के projects/pipelines में shared होती है।

---

# 🆚 11. Agents vs Parallel Jobs

यह distinction बहुत important है।

### Agents

```text
Machine Capacity
```

### Parallel Jobs

```text
Concurrent Pipeline Job Capacity
```

Example:

```text
Agents = 5
Parallel Jobs = 2
```

तो:

```text
Agent-01 → Job 🟢
Agent-02 → Job 🟢
Agent-03 → Idle
Agent-04 → Idle
Agent-05 → Idle
```

इसलिए:

```text
5 Agents
≠
5 Concurrent Jobs
```

---

# 🏢 12. Organization-Level Sharing

Azure DevOps Services में parallel job capacity organization level पर shared होती है।

Example:

```text
🏢 Audix DevOps Organization
│
├── SBI Project
├── HDFC Project
├── ICICI Project
└── Shriram Project
```

अगर organization के पास limited parallel capacity है:

```text
SBI Pipeline → Job-01 🟢
SBI Pipeline → Job-02 🟢
HDFC Pipeline → Job-03 🟡 Waiting
```

इसलिए एक project की running jobs दूसरे project की available concurrency को consume कर सकती हैं।

---

# 🧩 13. Agent Pool vs Project

Agent Pool और Project अलग concepts हैं।

```text
🏢 Organization
│
├── 📁 SBI Project
├── 📁 HDFC Project
└── 📁 ICICI Project
```

और:

```text
🏊 Agent Pools
│
├── Default
├── Linux-Pool
├── Windows-Pool
└── Production-Pool
```

Pool permissions से तय किया जा सकता है कि कौन pipeline किस pool को use कर सकती है।

---

# 🐧 14. Different OS Agents

एक pool में अलग-अलग capabilities वाले agents हो सकते हैं।

Example:

```text
🏊 DevOps-Pool
│
├── Agent-01 → Linux
├── Agent-02 → Linux
├── Agent-03 → Windows
└── Agent-04 → Windows
```

Pipeline को specific capability चाहिए तो demands use की जा सकती हैं।

---

# 🧩 15. Capabilities

Agent capabilities यह बताती हैं कि agent क्या support करता है।

Example:

```text
Agent-01
│
├── OS = Linux
├── Terraform = installed
├── Docker = installed
└── AzureCLI = installed
```

दूसरा:

```text
Agent-02
│
├── OS = Windows
├── VisualStudio = installed
├── MSBuild = installed
└── Terraform = installed
```

---

# 🎯 16. Demands

Pipeline कह सकती है:

> मुझे ऐसा agent चाहिए जिसमें specific capability हो।

Example:

```yaml
pool:
  name: Audix-Pool
  demands:
  - Terraform
```

अब job ऐसे agent को target कर सकती है जिसमें required capability मौजूद हो।

Azure DevOps self-hosted agents में demands/capabilities का इस्तेमाल job matching के लिए करता है।

---

# 🔥 17. Real Industrial Pool

Audix के पास:

```text
🏊 Audix-Linux-Pool
│
├── Agent-01
│   ├── Terraform
│   ├── Docker
│   └── Azure CLI
│
├── Agent-02
│   ├── Terraform
│   ├── Docker
│   └── Azure CLI
│
└── Agent-03
    ├── Terraform
    └── Azure CLI
```

Pipeline:

```yaml
pool:
  name: Audix-Linux-Pool
  demands:
  - Terraform
```

तो Terraform requirement वाला compatible agent select हो सकता है।

---

# 🚨 18. कोई Compatible Agent नहीं मिला तो?

Example:

```text
Pipeline
   │
   ▼
Demand = Docker
   │
   ▼
Pool
│
├── Agent-01 → Terraform
├── Agent-02 → Terraform
└── Agent-03 → AzureCLI
```

किसी में Docker capability नहीं।

तो job को compatible agent नहीं मिलेगा।

ऐसी स्थिति में job wait/fail behavior pool availability और matching state पर depend करेगा; Azure docs के अनुसार यदि compatible free agent नहीं मिलता तो job wait करती है, और यदि pool में कोई matching agent ही नहीं है तो job fail हो सकती है।

---

# 🔴 19. Agent Offline

Example:

```text
Agent-01 → Offline
Agent-02 → Offline
Agent-03 → Offline
```

और Pipeline:

```text
Job
 ↓
Pool
 ↓
No Available Agent
```

Result:

```text
🟡 Queued / Waiting
```

जब तक suitable capacity/agent available नहीं होती।

---

# 🔴 20. Agent Busy

```text
Agent-01 → Busy
Agent-02 → Busy
Agent-03 → Idle
```

New Job:

```text
Job-04
   ↓
Agent-03
```

लेकिन अगर:

```text
Agent-01 → Busy
Agent-02 → Busy
Agent-03 → Busy
```

तो:

```text
Job-04
   ↓
Queue
```

---

# 💾 21. Pool Consumption

Agent Pool की utilization देखना useful है।

अगर बहुत सारे jobs:

```text
Queued
Queued
Queued
Queued
```

और available parallel capacity लगातार full है:

```text
Running = Maximum
```

तो organization को concurrency/capacity review करनी चाहिए।

Azure DevOps में Pool consumption report से running और queued jobs की history देखी जा सकती है।

---

# 🩺 22. Agent Pool Troubleshooting

अगर job start नहीं हो रही:

```text
Pipeline
   │
   ▼
Job Queued?
   │
   ├── Yes
   │
   ├── Parallel capacity?
   │
   ├── Agent available?
   │
   ├── Agent online?
   │
   ├── Pool permission?
   │
   ├── Capability match?
   │
   └── Demand match?
```

---

# 🔍 23. Troubleshooting Matrix

| Symptom            | Check                          |
| ------------------ | ------------------------------ |
| Job queued         | Parallel capacity              |
| No agent found     | Pool + agent status            |
| Agent offline      | Machine/service/network        |
| Demand mismatch    | Capabilities                   |
| Permission error   | Pool security                  |
| Tool missing       | Agent software                 |
| Disk full          | Agent workspace                |
| Job timeout        | Job/runtime/network            |
| Azure auth error   | Service Connection             |
| Different behavior | Agent configuration difference |

---

# 🏗️ 24. Recommended Industrial Design

Audix जैसे organization के लिए:

```text
🏢 Audix DevOps
│
├── 🏊 Linux-Build-Pool
│   ├── Agent-01
│   └── Agent-02
│
├── 🏊 Windows-Build-Pool
│   ├── Agent-01
│   └── Agent-02
│
└── 🏊 Production-Pool
    ├── Agent-01
    └── Agent-02
```

इससे workloads को अलग किया जा सकता है।

लेकिन pools की संख्या unnecessarily ज्यादा नहीं करनी चाहिए।

---

# 🛡️ 25. Production Pool

Production deployment के लिए:

```text
🏊 Production-Pool
│
├── Agent-01
└── Agent-02
```

और:

```text
Production Pipeline
       │
       ▼
Production Pool
       │
       ▼
Compatible Agent
       │
       ▼
Azure Production
```

इसके साथ:

```text
Approvals
Checks
RBAC
Service Connection
Network Controls
```

जैसी security controls भी design की जाती हैं।

---

# 🔥 26. सबसे Important Scenario

### Situation:

```text
Pipeline
│
├── Build
├── Security Scan
└── Test
```

Pool:

```text
Agent-01
Agent-02
Agent-03
```

Parallel capacity:

```text
3
```

Possible:

```text
Build
  ↓
Agent-01

Security Scan
  ↓
Agent-02

Test
  ↓
Agent-03
```

तीनों concurrently चल सकते हैं अगर jobs independent और compatible हैं।

---

# 🔗 27. अगर Build → Test Dependency है?

```text
Build
  ↓
Test
```

तो:

```text
Build → Agent-01
       ↓
    Complete
       ↓
Test → Agent-02
```

इसलिए:

> **Pipeline के सभी Jobs एक ही Agent पर चलना जरूरी नहीं है।**

यह बहुत important interview concept है।

---

# 🧠 28. Pool Selection vs Agent Selection

```text
Pipeline
   │
   ▼
Which Pool?
   │
   ▼
Agent Pool
   │
   ▼
Which Agent?
   │
   ├── Online?
   ├── Idle?
   ├── Compatible?
   ├── Capabilities?
   └── Demands?
```

---

# 🏁 29. Final Mental Model

```text
                    🔄 PIPELINE
                         │
                         ▼
                       ⚙️ JOB
                         │
                         ▼
                    🏊 AGENT POOL
                         │
            ┌────────────┼────────────┐
            ▼            ▼            ▼
        🤖 Agent-01  🤖 Agent-02  🤖 Agent-03
            │            │            │
         Busy?        Busy?        Available?
            │            │            │
            └────────────┼────────────┘
                         ▼
                  Compatible Agent
                         │
                         ▼
                   📝 Job Steps
                         │
                         ▼
                    ☁️ Target
```

---

# 🎯 Interview Questions

### Q1. What is an Agent Pool?

Agent Pool is a logical collection of agents from which Azure Pipelines can select an appropriate agent to execute a job.

### Q2. Can multiple agents exist in one pool?

हाँ।

```text
Pool
├── Agent-01
├── Agent-02
└── Agent-03
```

### Q3. Can one agent run multiple jobs simultaneously?

Normal Azure Pipelines agent execution model में एक agent एक समय में एक job run करता है।

### Q4. Can jobs of the same pipeline run on different agents?

हाँ। अलग jobs compatible available agents पर execute हो सकती हैं।

### Q5. What happens when all agents are busy?

Job queue में wait कर सकती है जब तक required capacity/compatible agent available न हो।

### Q6. What is the difference between Agent Pool and Parallel Jobs?

```text
Agent Pool
= Where agents belong

Parallel Jobs
= How many jobs can run concurrently
```

### Q7. What are capabilities?

Agent की available software/configuration characteristics।

### Q8. What are demands?

Pipeline की agent requirements जिनके आधार पर compatible agent चुना जाता है।

---

# 🚀 Next Phase

अब हमारे पास:

```text
04 Pipeline
      ↓
05 Agent
      ↓
06 Self-hosted Agent
      ↓
07 Agent Pool
```

अगला बहुत important security topic:

```text
08-service-connection.md
```

जहाँ हम खोलेंगे:

```text
Pipeline
   ↓
Service Connection
   ↓
Microsoft Entra ID
   ↓
Workload Identity Federation
   ↓
Azure Identity
   ↓
RBAC
   ↓
Subscription / Resource Group / Resource
```

---
