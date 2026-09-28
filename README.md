# 🚀 Azure DevOps From Scratch — Zero to Practical

<p align="center">

<img src="https://img.shields.io/badge/Azure%20DevOps-Learning-blue?logo=azuredevops" alt="Azure DevOps">

<img src="https://img.shields.io/badge/Azure-Cloud-blue?logo=microsoftazure" alt="Azure">

<img src="https://img.shields.io/badge/Git-GitHub-orange?logo=git" alt="Git">

<img src="https://img.shields.io/badge/Level-Beginner%20to%20Practical-green" alt="Level">

</p>

---

## 🎯 हमारा Goal

इस repository का मुख्य उद्देश्य **Azure DevOps को बिल्कुल Zero से समझना और धीरे-धीरे एक real-world CI/CD workflow तक पहुँचना** है।

यह repository केवल commands याद करने के लिए नहीं है।

हमारा focus रहेगा:

> **पहले Concept → फिर Why → फिर Architecture → फिर Practical → फिर Verification**

हम यह समझेंगे कि Azure DevOps में कोई component **क्या है, क्यों चाहिए, कहाँ use होता है और दूसरे components के साथ कैसे काम करता है।**

---

# 🧠 हम क्या सीखने वाले हैं?

हम Azure DevOps के पूरे flow को एक connected system की तरह समझेंगे:

```text
Developer
    │
    ▼
Git Repository
    │
    ▼
Azure DevOps Organization
    │
    ▼
Project
    │
    ▼
Repository
    │
    ▼
Pipeline
    │
    ▼
Agent
    │
    ▼
Build / Test
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
    │
    ▼
Application
```

इस पूरे flow को समझने के बाद हमारा लक्ष्य होगा कि हम सिर्फ Pipeline create करना नहीं, बल्कि **पूरे CI/CD architecture को reason कर सकें।**

---

# ☁️ Azure और Azure DevOps में Difference

शुरुआत में सबसे important concept यही है।

## Azure क्या करता है?

**Azure** मुख्य रूप से Cloud Infrastructure और Services provide करता है।

उदाहरण:

```text
Azure
 │
 ├── Virtual Machine
 ├── AKS
 ├── App Service
 ├── Storage Account
 ├── Azure Database
 ├── Virtual Network
 └── Resource Group
```

इन resources पर हमारी application वास्तव में **run** करती है।

---

## Azure DevOps क्या करता है?

**Azure DevOps** application के development और delivery process को manage और automate करने में मदद करता है।

उदाहरण:

```text
Azure DevOps
 │
 ├── Organization
 ├── Project
 ├── Repository
 ├── Branch
 ├── Pull Request
 ├── Pipeline
 ├── Agent
 ├── Agent Pool
 ├── Service Connection
 ├── Environment
 └── Artifacts
```

Simple language में:

> **Azure application को host/run करता है।**
> **Azure DevOps application को build, test और deploy करने की process automate करता है।**

---

# 🏢 Azure Foundation भी समझेंगे

Azure DevOps सीखने से पहले Azure की basic hierarchy समझना जरूरी है।

```text
Microsoft Entra Tenant
        │
        ▼
Management Group
        │
        ▼
Subscription
        │
        ▼
Resource Group
        │
        ▼
Azure Resources
```

उदाहरण:

```text
AUDIX TENANT
     │
     ▼
Audix-Cloud-Platform
     │
     ├── SBI Subscription
     │      ├── CBS RG
     │      ├── Payments RG
     │      └── Analytics RG
     │
     ├── HDFC Subscription
     │
     ├── ICICI Subscription
     │
     └── Shriram Subscription
```

इससे हमें यह समझ आएगा कि Azure में resources कहाँ exist करते हैं और permissions किस scope पर लागू हो सकती हैं।

---

# 🔗 Azure और Azure DevOps कैसे Connect होते है
