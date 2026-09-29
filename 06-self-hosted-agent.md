# 🖥️ Phase 06 — Azure DevOps Self-Hosted Agent

<p align="center">

![Azure DevOps](https://img.shields.io/badge/Azure%20DevOps-Self--Hosted%20Agent-blue?logo=azuredevops)
![Windows](https://img.shields.io/badge/Windows-Agent-blue?logo=windows)
![Linux](https://img.shields.io/badge/Linux-Agent-orange?logo=linux)
![CI/CD](https://img.shields.io/badge/CI%2FCD-Automation-green)

</p>

---

# 🎯 Objective

इस phase में हम **Self-hosted Agent** को सिर्फ definition के रूप में नहीं, बल्कि एक real industrial machine की तरह समझेंगे।

हम जानेंगे:

* Self-hosted Agent क्या है?
* इसकी जरूरत क्यों पड़ती है?
* Agent कहाँ install होता है?
* Agent package कहाँ से download होता है?
* Agent को Azure DevOps से कैसे connect करते हैं?
* Agent के अंदर कौन-कौन से folders/files होते हैं?
* Agent पर source code कहाँ आता है?
* Pipeline commands कहाँ execute होती हैं?
* एक Agent कितने jobs चला सकता है?
* Agent busy हो जाए तो क्या होता है?
* Agent की disk full हो सकती है?
* Cache और workspace कैसे काम करते हैं?
* Agent troubleshooting कैसे करें?
* Agent को backup कैसे करें?
* Self-hosted Agent को secure कैसे रखें?

---

# 🧠 1. Self-hosted Agent क्या है?

Simple definition:

> **Self-hosted Agent वह machine है जिसे organization खुद manage करती है और उस machine पर Azure DevOps Agent software install किया जाता है ताकि Azure Pipelines के jobs वहाँ execute हो सकें।**

Example:

```text
🏢 Audix
   │
   ▼
🖥️ Azure VM / On-Prem Server
   │
   ▼
🤖 Azure DevOps Agent Software
   │
   ▼
🔄 Azure Pipeline Jobs
```

इस machine का OS हो सकता है:

```text
Windows
Linux
macOS
```

Self-hosted agents Azure VM, on-prem server, physical machine या दूसरे supported compute environments पर चलाए जा सकते हैं।

---

# 🆚 2. Microsoft-hosted vs Self-hosted

```text
Microsoft-hosted
      │
      ▼
Microsoft machine manage करता है

Self-hosted
      │
      ▼
Organization machine manage करती है
```

| Feature                | Microsoft-hosted       | Self-hosted                       |
| ---------------------- | ---------------------- | --------------------------------- |
| Machine                | Microsoft provides     | Organization provides             |
| OS maintenance         | Microsoft              | Organization                      |
| Agent maintenance      | Mostly managed         | Organization                      |
| Custom tools           | Image पर depend        | Full control                      |
| Local cache            | Job के बाद VM discard  | Persist कर सकता है                |
| Private network access | Architecture dependent | Easier when network is configured |
| Disk management        | Microsoft              | Organization                      |
| Patching               | Microsoft              | Organization                      |
| Troubleshooting        | Pipeline level         | Machine + Agent + Pipeline        |

Self-hosted का बड़ा फायदा है कि machine-level software और caches run-to-run persist कर सकते हैं।

---

# 🏗️ 3. Real Industrial Architecture

मान लो Audix के पास एक Azure VM है:

```text
Azure Subscription
       │
       ▼
Resource Group
       │
       ▼
🖥️ VM
       │
       ├── Windows / Linux
       │
       ├── Git
       ├── Terraform
       ├── Azure CLI
       ├── Docker
       └── Azure DevOps Agent
```

Pipeline:

```text
Azure DevOps
      │
      ▼
Pipeline
      │
      ▼
Agent Pool
      │
      ▼
Self-hosted Agent
      │
      ▼
VM
      │
      ▼
Commands
```

---

# 🧩 4. Agent के लिए Machine में क्या चाहिए?

Basic requirements:

```text
🖥️ Machine
   │
   ├── Supported OS
   ├── Network Connectivity
   ├── User/Service Account
   ├── Azure DevOps connectivity
   ├── Required tools
   ├── Sufficient CPU/RAM
   └── Sufficient Disk
```

अगर Terraform pipeline है:

```text
Machine
│
├── Git
├── Terraform
├── Azure CLI
└── Azure DevOps Agent
```

अगर Docker build है:

```text
Machine
│
├── Docker
├── Git
└── Azure DevOps Agent
```

---

# 📥 5. Agent Download कहाँ से करें?

Azure DevOps में:

```text
Organization
   ↓
Organization Settings
   ↓
Agent Pools
   ↓
Select Pool
   ↓
Agents
   ↓
New Agent
```

फिर OS select करके agent package download किया जा सकता है।

Microsoft official documentation agent package को download करके machine पर configure करने की प्रक्रिया बताती है।

---

# 🪟 6. Windows Self-hosted Agent

Concept:

```text
Windows Server / Windows VM
        │
        ▼
Download Agent
        │
        ▼
Extract ZIP
        │
        ▼
Configure Agent
        │
        ▼
Azure DevOps
        │
        ▼
Agent Pool
```

Typical folder:

```text
C:\azagent
```

हम agent को एक dedicated folder में रखना बेहतर मान सकते हैं।

Example:

```text
C:\azagent
```

---

# 🐧 7. Linux Self-hosted Agent

Example:

```text
Ubuntu VM
   │
   ▼
Download Agent
   │
   ▼
Extract package
   │
   ▼
Configure
   │
   ▼
Run Agent
```

Example directory:

```text
/home/azureagent
```

Production में dedicated service account और restricted permissions use करना बेहतर practice है।

---

# 🔐 8. Agent Registration क्या है?

Agent install करना और Agent register करना अलग concepts हैं।

### Install

Machine पर Agent software रखना।

### Register

Azure DevOps को बताना:

> "यह machine मेरी Agent Pool में available है।"

Flow:

```text
Machine
   │
   ▼
Agent Software
   │
   ▼
Registration
   │
   ▼
Azure DevOps Organization
   │
   ▼
Agent Pool
   │
   ▼
Agent दिखाई देता है
```

---

# 🔑 9. Authentication

Agent registration के दौरान Azure DevOps को authenticate करना पड़ता है।

Historically PAT commonly used रहा है।

Modern setups में supported authentication options environment/setup के हिसाब से अलग हो सकते हैं।

**PAT को code, YAML, README या public repository में कभी hard-code नहीं करना चाहिए।**

---

# 🧠 10. Agent Registration के बाद क्या होता है?

Azure DevOps में Agent दिखाई देगा:

```text
Agent Pool
   │
   └── 🟢 SBI-Agent-01
```

Status:

```text
Online
```

मतलब:

> Agent Azure DevOps से communicate कर रहा है और job लेने के लिए available है।

---

# 🔄 11. Agent काम कैसे करता है?

सबसे simple flow:

```text
Pipeline
   │
   ▼
Job Queue
   │
   ▼
Agent Pool
   │
   ▼
Compatible Agent
   │
   ▼
Job Assigned
   │
   ▼
Workspace
   │
   ▼
Steps Execute
```

---

# 📁 12. Agent File System

Self-hosted Agent की सबसे important चीजों में से एक है उसका filesystem।

Conceptually:

```text
C:\azagent
│
├── Agent Software
│
├── bin
│
├── externals
│
├── _diag
│
├── _work
│
└── Configuration
```

Exact files और directory contents agent version और OS के अनुसार बदल सकते हैं।

---

# 📁 13. `_work` Folder

सबसे important folder:

```text
_work
```

यह pipeline jobs के working data के लिए इस्तेमाल होता है।

Conceptually:

```text
_work
│
├── Source Code
├── Build Output
├── Artifacts
├── Test Results
└── Temporary Data
```

Azure Pipelines agent workspace में source, outputs और अन्य run data रखता है।

---

# 📦 14. Workspace के अंदर क्या होता है?

Conceptually:

```text
Pipeline.Workspace
│
├── Source
├── Artifacts
├── Binaries
└── Test Results
```

Important predefined directories:

```text
Build.SourcesDirectory
Build.ArtifactStagingDirectory
Build.BinariesDirectory
Common.TestResultsDirectory
```

Microsoft documentation के अनुसार agent job workspace source code download, steps execution और outputs के लिए इस्तेमाल होता है।

---

# 🧹 15. Self-hosted Agent में Cleanup क्यों जरूरी है?

Microsoft-hosted agent हर job के लिए fresh VM देता है।

Self-hosted agent reused होता है।

इसलिए:

```text
Run 1
 ↓
Files
 ↓
Run 2
 ↓
More Files
 ↓
Run 3
 ↓
More Files
```

अगर cleanup strategy नहीं है:

```text
Disk
 ↓
70%
 ↓
85%
 ↓
95%
 ↓
100%
 ↓
💥 Pipeline Failure
```

Self-hosted agents में workspace data runs के बीच persist हो सकता है; workspace cleaning को job configuration से control किया जा सकता है।

---

# 💾 16. क्या Agent की Disk Full हो सकती है?

### हाँ। बिल्कुल।

Common reasons:

```text
Disk Full
│
├── Old source checkouts
├── Docker images
├── Docker containers
├── Terraform cache
├── npm/Maven/NuGet cache
├── Build outputs
├── Logs
├── Artifacts
└── Temporary files
```

Example:

```text
C:\azagent\_work
```

या Linux:

```text
/home/azureagent/_work
```

बहुत ज्यादा data accumulate कर सकता है।

---

# 🔍 17. Disk Full Troubleshooting

पहले check:

### Windows

```powershell
Get-PSDrive C
```

### Linux

```bash
df -h
```

फिर देखें कौन सा folder बड़ा है।

Linux:

```bash
du -sh /home/azureagent/_work/*
```

Windows में:

```powershell
Get-ChildItem C:\azagent\_work -Directory |
Select-Object Name
```

फिर pipeline history और workspace cleanup policy देखें।

---

# 🧹 18. Workspace Clean

Self-hosted pipeline में job workspace clean किया जा सकता है।

Example:

```yaml
jobs:
- job: Build
  workspace:
    clean: all

  pool:
    name: Audix-SelfHosted

  steps:
  - script: echo "Build"
```

Options:

```text
clean: outputs
clean: resources
clean: all
```

`all` पूरे Pipeline workspace को job से पहले clean करता है।

---

# ⚠️ 19. Cleanup और Cache में Balance

हर चीज delete करना भी हमेशा सही नहीं है।

मान लो:

```text
Dependency Cache
      ↓
Build
      ↓
Next Build
```

Cache से build fast हो सकता है।

लेकिन:

```text
Huge Cache
     ↓
Disk Full
     ↓
Pipeline Failure
```

इसलिए:

> **Cleanup + Cache strategy दोनों चाहिए।**

---

# 🐳 20. Docker Agent Machine

अगर self-hosted agent Docker build करता है:

```text
Agent VM
│
├── Azure DevOps Agent
├── Docker Engine
│
├── Docker Images
├── Containers
├── Volumes
└── Build Cache
```

Docker images बहुत ज्यादा disk consume कर सकती हैं।

इसलिए Docker agents में disk monitoring बहुत important है।

---

# 🏃 21. एक Agent कितने Jobs चला सकता है?

यह बहुत important सवाल है।

Azure DevOps में सामान्य self-hosted agent:

> **एक समय में एक job execute करता है।**

Example:

```text
Agent-01
   │
   └── Job-01 🟢 Running
```

अगर Job-02 आता है:

```text
Agent-01
   │
   └── 🔴 Busy
```

तो Job-02 किसी दूसरे compatible available agent का इंतजार करेगा।

Azure documentation भी बताती है कि agent एक समय में एक job run करता है।

---

# 🔥 22. Agent Busy हो जाए तो?

मान लो:

```text
Agent-01 → Job-01 Running
```

अब:

```text
Job-02
Job-03
Job-04
```

आ गए।

अगर कोई दूसरा compatible agent available नहीं है:

```text
Job-02 → Queued
Job-03 → Queued
Job-04 → Queued
```

जैसे ही Agent-01 free:

```text
Job-01
  ↓
Finished

Agent-01
  ↓
Available

Job-02
  ↓
Running
```

लेकिन यहाँ **parallel job capacity** भी matter करती है।

---

# ⚡ 23. Parallel Job क्या है?

Parallel Job का मतलब:

> Organization कितने pipeline jobs को एक साथ execute कर सकती है।

Example:

```text
Parallel Jobs = 1

Job-01 → 🟢 Running
Job-02 → 🟡 Waiting
Job-03 → 🟡 Waiting
```

अगर:

```text
Parallel Jobs = 3
```

तो:

```text
Job-01 → 🟢
Job-02 → 🟢
Job-03 → 🟢
Job-04 → 🟡 Waiting
```

Azure DevOps Services में parallel job capacity organization level पर shared होती है।

---

# 🧠 24. Agent Count ≠ Parallel Job Count

यह बहुत important है।

मान लो:

```text
Agents = 5
Parallel Jobs = 2
```

तो पाँच agents registered होने के बावजूद एक समय में केवल available parallel capacity के हिसाब से jobs चलेंगी।

```text
Agent-01 → Job 🟢
Agent-02 → Job 🟢
Agent-03 → Idle
Agent-04 → Idle
Agent-05 → Idle
```

इसलिए:

```text
More Agents
      ≠
Automatically More Concurrent Jobs
```

Parallel capacity अलग concept है।

---

# 🏗️ 25. क्या एक Pipeline के Jobs अलग-अलग Agents पर चल सकते हैं?

### हाँ।

Example:

```text
Pipeline
│
├── Job-01 → Build
├── Job-02 → Test
└── Job-03 → Security Scan
```

अगर jobs independent हैं:

```text
Job-01 ──► Agent-01
Job-02 ──► Agent-02
Job-03 ──► Agent-03
```

लेकिन अगर:

```text
Job-02 dependsOn Job-01
```

तो Job-02 को Job-01 के completion के बाद चलाया जा सकता है।

इसलिए:

```text
Independent Jobs
      ↓
Potential Parallel Execution

Dependent Jobs
      ↓
Sequential Dependency
```

और अलग jobs अलग compatible agents पर route हो सकते हैं।

---

# 🔐 26. Agent पर Secrets रखना चाहिए?

### सामान्यतः नहीं।

Agent machine पर:

```text
❌ Password files
❌ PAT files
❌ Client secrets
❌ Production passwords
```

plain text में नहीं रखने चाहिए।

Prefer:

```text
Azure DevOps Secret Variables
        +
Variable Groups
        +
Azure Key Vault
        +
Federated Authentication
```

architecture के अनुसार।

---

# 🛡️ 27. Self-hosted Agent Security

Self-hosted agent को एक production server की तरह treat करो।

```text
🖥️ Agent
│
├── OS Patching
├── Firewall
├── Least Privilege
├── Antivirus/EDR
├── Network Restrictions
├── Secrets Protection
├── Monitoring
├── Disk Monitoring
└── Backup Strategy
```

Agent को unnecessarily:

```text
❌ Domain Admin
❌ Subscription Owner
❌ Global Admin
```

जैसी high privileges नहीं देनी चाहिए।

---

# 🧪 28. Agent Troubleshooting

जब pipeline fail हो:

```text
Pipeline Failed
      │
      ▼
पहले Error पढ़ो
      │
      ▼
Agent Issue?
      │
      ├── Yes → Agent troubleshoot
      │
      └── No → Pipeline/Tool/Application troubleshoot
```

---

# 🔍 29. Troubleshooting Checklist

## Step 1 — Agent Online है?

```text
Agent Pool
   ↓
Agent
   ↓
Status = Online?
```

अगर Offline:

```text
Agent Service
Network
Machine
Authentication
```

check करो।

---

## Step 2 — Agent Job ले रहा है?

अगर:

```text
Waiting for agent
```

तो check:

```text
Pool
↓
Agent Availability
↓
Demands
↓
Capabilities
↓
Parallel Jobs
```

---

## Step 3 — Tool Installed है?

अगर error:

```text
terraform: command not found
```

तो:

```text
Agent
 ↓
Terraform installed?
 ↓
PATH configured?
```

check करो।

---

## Step 4 — Disk Space

```text
df -h
```

या Windows:

```powershell
Get-PSDrive
```

---

## Step 5 — Network

Check:

```text
Agent
 ↓
Internet / Azure DevOps
 ↓
Required endpoints
```

अगर agent Azure DevOps से communicate नहीं कर पा रहा तो job start ही नहीं हो सकती।

---

# 🩺 30. Agent Diagnostics

Linux agent में diagnostics run किए जा सकते हैं:

```bash
./run.sh --diagnostics
```

Microsoft documentation self-hosted agent troubleshooting के लिए diagnostics option provide करती है।

Additional network diagnostics के लिए:

```text
Agent.Diagnostic = true
```

जैसी diagnostic configuration भी उपलब्ध है।

---

# 📋 31. Common Pipeline Failure Reasons

| Problem                     | Possible Cause                 |
| --------------------------- | ------------------------------ |
| Agent Offline               | Service stopped / Network      |
| No agent found              | Pool/capability mismatch       |
| Job queued                  | Agent busy / Parallel capacity |
| Terraform not found         | Tool missing/PATH              |
| Docker failed               | Docker service / permissions   |
| Disk full                   | Workspace/cache/images         |
| Git checkout failed         | Network/authentication         |
| Azure authentication failed | Service Connection / identity  |
| Permission denied           | OS permissions                 |
| Timeout                     | Long-running job / network     |
| Random build failure        | Dirty workspace/cache          |
| Artifact missing            | Publish/download configuration |
| Tool version mismatch       | Agent software/tool difference |

---

# 🧠 32. Capabilities और Demands

Self-hosted agents की capabilities होती हैं।

Example:

```text
Agent-01
│
├── OS = Linux
├── Terraform = installed
├── Docker = installed
└── AzureCLI = installed
```

Pipeline कह सकती है:

```text
मुझे ऐसा Agent चाहिए
जिसमें Terraform available हो।
```

इसे **demands** के through express किया जा सकता है।

```yaml
pool:
  name: Audix-SelfHosted
  demands:
  - Terraform
```

Azure Pipelines self-hosted agents में capabilities और demands का उपयोग compatible agent चुनने के लिए करती है।

---

# 💾 33. Agent Backup करना चाहिए?

यहाँ एक important distinction है।

### Agent software का backup

जरूरी नहीं कि पूरा agent folder daily backup करना ही best strategy हो।

क्यों?

क्योंकि agent को नए machine पर reinstall/re-register किया जा सकता है।

### ज्यादा important backup

```text
Terraform State
Application Artifacts
Configuration
Pipeline YAML
Infrastructure Code
Secrets/Key Vault Data
Important Build Data
```

इनका proper persistent backup strategy होना ज्यादा important है।

---

# 🏗️ 34. Agent VM Backup

अगर self-hosted agent Azure VM पर है:

```text
Azure VM
   │
   ├── OS Disk
   ├── Data Disk
   └── Agent
```

Azure VM backup/snapshot strategy बनाई जा सकती है।

लेकिन:

> **VM backup को Terraform state backup का replacement मत समझो।**

Terraform state को अलग persistent backend और backup strategy चाहिए।

---

# 🔁 35. Disaster Recovery

Industrial design:

```text
              🏢 Azure DevOps
                     │
                     ▼
               Agent Pool
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
      Agent-01              Agent-02
      Primary               Secondary
          │                     │
          └──────────┬──────────┘
                     ▼
                  Azure
```

अगर:

```text
Agent-01 💥
```

तो compatible Agent-02 available होने पर queued job वहाँ execute हो सकता है।

इसलिए production में single-agent dependency कम करना useful है।

---

# 🚨 36. Single Agent का Risk

Architecture:

```text
Pipeline
   ↓
Agent-01
   ↓
Azure
```

अगर Agent-01:

```text
💥 Disk failure
💥 OS failure
💥 Network failure
💥 Maintenance
```

तो pipeline execution प्रभावित हो सकती है।

Better:

```text
Pipeline
   ↓
Agent Pool
   │
   ├── Agent-01
   ├── Agent-02
   └── Agent-03
```

---

# 🏢 37. Industrial Audix Example

```text
🏢 Audix
   │
   ▼
Azure DevOps
   │
   ▼
SBI Project
   │
   ▼
SBI-PROD Agent Pool
   │
   ├── 🖥️ SBI-Agent-01
   ├── 🖥️ SBI-Agent-02
   └── 🖥️ SBI-Agent-03
```

अगर:

```text
Agent-01 → Busy
Agent-02 → Busy
Agent-03 → Available
```

तो compatible job Agent-03 पर जा सकती है।

अगर:

```text
Agent-01 → Offline
Agent-02 → Offline
Agent-03 → Offline
```

तो job execute नहीं होगी।

---

# 🧠 38. Golden Rules

### Rule 1

```text
One Agent
   ↓
One Job at a time
```

### Rule 2

```text
More Agents
   ↓
More execution capacity
```

लेकिन parallel job capacity भी required है।

### Rule 3

```text
Self-hosted
   ↓
You own the maintenance
```

### Rule 4

```text
Agent disk
   ↓
Monitor + Cleanup
```

### Rule 5

```text
Secrets
   ↓
Don't hard-code
```

### Rule 6

```text
Production
   ↓
Avoid single-agent dependency
```

### Rule 7

```text
Agent backup
   ↓
Useful for machine recovery

Persistent data backup
   ↓
Must be designed separately
```

---

# 🎯 39. Final Mental Model

```text
                 🔄 PIPELINE
                      │
                      ▼
                    ⚙️ JOB
                      │
                      ▼
                 🏊 AGENT POOL
                      │
          ┌───────────┼───────────┐
          ▼           ▼           ▼
      🤖 Agent-01 🤖 Agent-02 🤖 Agent-03
          │           │           │
          ▼           ▼           ▼
       Execute     Execute     Execute
          │           │           │
          └───────────┼───────────┘
                      ▼
                  ☁️ AZURE
```

---

# 🏁 What We Learned

इस phase में हमने deep dive किया:

* 🖥️ Self-hosted Agent
* 📥 Agent download/install concept
* 🔐 Agent registration
* 📁 Agent filesystem
* 📂 `_work`
* 📦 Workspace
* 🧹 Cleanup
* 💾 Disk full problem
* 🏃 One agent = one job at a time
* ⚡ Parallel execution
* 🔄 Multiple agents
* 🩺 Troubleshooting
* 🔍 Diagnostics
* 🧩 Capabilities
* 🎯 Demands
* 🔐 Security
* 💾 Backup
* ♻️ Disaster Recovery
* 🏢 Industrial Agent architecture

---

# 🚀 Next

अगले document में हम पूरा **Agent Pool** खोलेंगे:

```text
07-agent-pool.md
```

और समझेंगे:

```text
Agent Pool
   ↓
Multiple Agents
   ↓
Job Queue
   ↓
Parallel Jobs
   ↓
Capabilities
   ↓
Demands
   ↓
Job Scheduling
   ↓
Busy Agent
   ↓
Idle Agent
   ↓
Offline Agent
   ↓
Troubleshooting
```

---
