# 🔐 Phase 08 — Azure DevOps Service Connection

<p align="center">

![Azure DevOps](https://img.shields.io/badge/Azure%20DevOps-Service%20Connection-0078D4?style=for-the-badge\&logo=azuredevops\&logoColor=white)
![Azure](https://img.shields.io/badge/Microsoft%20Azure-Integration-0078D4?style=for-the-badge\&logo=microsoftazure\&logoColor=white)
![Entra ID](https://img.shields.io/badge/Microsoft%20Entra-ID-0078D4?style=for-the-badge)
![Security](https://img.shields.io/badge/Security-Workload%20Identity%20Federation-107C10?style=for-the-badge)

</p>

---

## 🎯 Objective

इस phase में हम समझेंगे कि **Azure DevOps Pipeline Azure के साथ securely communicate कैसे करती है।**

हम सीखेंगे:

* Service Connection क्या है?
* इसकी जरूरत क्यों पड़ती है?
* Azure DevOps और Azure अलग systems क्यों हैं?
* Authentication और Authorization में difference
* Microsoft Entra ID क्या role play करता है?
* Service Principal क्या है?
* Managed Identity क्या है?
* Workload Identity Federation (WIF) क्या है?
* Service Connection और RBAC का relation
* Subscription / Resource Group / Resource scope
* Pipeline Service Connection को कैसे use करती है?
* `Contributor` role blindly क्यों नहीं देना चाहिए?
* Service Connection troubleshooting
* Industrial architecture

---

# 🧠 1. सबसे पहले — Service Connection क्या है?

Simple definition:

> **Azure DevOps Service Connection एक configured connection है जिसके through Azure DevOps Pipeline किसी external service — जैसे Azure — के साथ authenticated तरीके से काम कर सकती है।**

Azure Resource Manager Service Connection का उपयोग Pipeline से Azure resources पर deployment/management operations करने के लिए किया जा सकता है। Microsoft वर्तमान में नए Azure Resource Manager service connections के लिए **Workload Identity Federation** को recommended approach बताता है।

Simple:

```text
Azure DevOps
     │
     │ Service Connection
     ▼
Microsoft Entra ID
     │
     ▼
Azure Identity
     │
     ▼
RBAC
     │
     ▼
Azure Resource
```

---

# ❓ 2. Service Connection की जरूरत क्यों?

सबसे पहले एक real-life example।

मान लो:

```text
👨‍💻 Developer
       │
       ▼
Azure DevOps Pipeline
       │
       ▼
Azure Subscription
```

Pipeline को Azure में जाकर:

* Resource Group create करना है
* Terraform run करना है
* VM deploy करना है
* Storage Account create करना है
* App Service deploy करना है
* AKS update करना है

लेकिन problem है:

> Azure DevOps Pipeline को automatically तुम्हारे personal Azure login का access नहीं मिल जाता।

इसलिए Pipeline को एक **machine/application identity** चाहिए।

यहीं Service Connection useful होती है।

---

# 🌍 3. Azure DevOps और Azure अलग Systems हैं

यह concept बहुत important है।

```text
┌──────────────────────────┐
│      Azure DevOps        │
│                          │
│ Organization             │
│ Project                  │
│ Repository               │
│ Pipeline                 │
│ Agent                    │
└────────────┬─────────────┘
             │
             │ Service Connection
             ▼
┌──────────────────────────┐
│       Microsoft Azure    │
│                          │
│ Entra ID                 │
│ Subscription             │
│ Resource Group            │
│ VM / VNet / Storage      │
│ AKS / App Service        │
└──────────────────────────┘
```

इसलिए:

> **ADO Project permission ≠ Azure RBAC permission**

दोनों अलग systems हैं।

---

# 🔐 4. Authentication vs Authorization

Service Connection समझने के लिए यह difference याद रखना जरूरी है।

## Authentication

Question:

> **"तुम कौन हो?"**

Example:

```text
Pipeline
   ↓
Microsoft Entra ID
   ↓
Identity verified
```

---

## Authorization

Question:

> **"तुम्हें क्या करने की permission है?"**

Example:

```text
Identity
   ↓
Contributor
   ↓
Resource Group
```

मतलब identity को उस scope पर Contributor permission मिली है।

---

# 🧠 5. पूरा Concept एक लाइन में

```text
Authentication = WHO ARE YOU?
Authorization  = WHAT CAN YOU DO?
```

---

# 🪪 6. Pipeline के लिए Identity कौन होती है?

Azure DevOps Pipeline Azure से connect करने के लिए अलग-अलग identity/authentication approaches use कर सकती है।

Modern Azure Resource Manager service connections में important options हैं:

```text
App Registration
       +
Workload Identity Federation

या

User-Assigned Managed Identity
       +
Workload Identity Federation
```

Microsoft नई configurations के लिए Workload Identity Federation को preferred approach बताता है।

---

# 🏢 7. Service Principal क्या है?

Service Principal को simple language में समझो:

> **Service Principal एक application identity है जिसका उपयोग application/workload की तरफ से Azure resources access करने के लिए किया जा सकता है।**

यह human user नहीं है।

Example:

```text
Human User
   ↓
Shrikant

Application Identity
   ↓
terraform-deployment-sp
```

Microsoft Entra में App Registration और Service Principal related लेकिन अलग objects हैं। App registration application definition/object है, जबकि service principal उस application की tenant-local identity/representation है।

---

# 🆔 8. Managed Identity क्या है?

Managed Identity भी Azure में application identity का एक तरीका है।

इसका बड़ा फायदा:

> Credentials Azure द्वारा managed किए जा सकते हैं।

Microsoft के अनुसार Managed Identity एक special type of service principal है जिसके credentials Azure manage करता है। इसके दो common types हैं:

```text
System-assigned Managed Identity
User-assigned Managed Identity
```

---

# 🔥 9. Service Principal vs Managed Identity

| Feature                | Service Principal                   | Managed Identity               |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Identity               | Entra application/service principal | Entra service principal        |
| Secret management      | Depending on auth method            | Azure manages credentials      |
| Azure-hosted workload  | Possible                            | Very useful                    |
| Portable identity      | Yes                                 | User-assigned MI can be reused |
| WIF                    | Supported                           | Supported                      |
| Human account required | No                                  | No                             |

---

# 🚨 10. पुराना तरीका — Client Secret

पहले common architecture:

```text
Pipeline
   ↓
Service Connection
   ↓
Service Principal
   ↓
Client Secret
   ↓
Azure
```

Problem:

```text
Secret
  ↓
Store
  ↓
Protect
  ↓
Rotate
  ↓
Expire
  ↓
Replace
```

अगर secret expire हो गया:

```text
Pipeline
   ↓
❌ Authentication Failed
```

इसलिए modern setup में जहाँ supported हो, Workload Identity Federation को prefer किया जाता है। Microsoft इसे secret management के बिना authentication के लिए recommend करता है।

---

# 🚀 11. Workload Identity Federation क्या है?

अब सबसे important concept।

**Workload Identity Federation (WIF)** का उद्देश्य यह है कि workload को long-lived secret रखने की जरूरत न पड़े।

Simple mental model:

```text
Azure DevOps Pipeline
        │
        │ Short-lived identity token
        ▼
 Microsoft Entra ID
        │
        │ Trust relationship
        ▼
Azure Identity
        │
        ▼
Azure Resource
```

Microsoft Entra Workload Identity Federation external identity provider और Entra application या managed identity के बीच trust relationship establish करती है।

---

# 🔐 12. WIF में Secret कहाँ है?

Traditional:

```text
Pipeline
   │
   ▼
Client Secret
   │
   ▼
Azure
```

WIF:

```text
Pipeline
   │
   ▼
Federated Identity
   │
   ▼
Microsoft Entra
   │
   ▼
Azure
```

इसलिए long-lived client secret को pipeline में maintain करने की जरूरत कम/समाप्त हो जाती है, depending on the configuration.

---

# 🧩 13. Federated Credential क्या है?

यह बहुत important term है।

Federated Identity Credential Azure/Entra को बताती है:

> "इस specific external workload identity/token को इस identity के behalf पर trust किया जा सकता है।"

Concept:

```text
External Workload
       │
       │ Token
       ▼
Federated Credential
       │
       ▼
Microsoft Entra
       │
       ▼
Identity
```

अगर configured issuer/subject values match नहीं करतीं, authentication fail हो सकती है। Microsoft की troubleshooting guidance भी issuer और subject identifier matching को key check बताती है।

---

# 🏗️ 14. Complete Service Connection Architecture

अब पूरा architecture:

```text
                    Azure DevOps
                         │
                         ▼
                     Pipeline
                         │
                         ▼
                Service Connection
                         │
                         ▼
               Workload Identity
                  Federation
                         │
                         ▼
                Microsoft Entra ID
                         │
                         ▼
                  Service Principal
                    / Managed Identity
                         │
                         ▼
                       RBAC
                         │
                         ▼
                 Azure Scope
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
        Subscription   RG         Resource
```

---

# 🎯 15. RBAC कहाँ आता है?

Service Connection सिर्फ identity/authentication setup नहीं है।

Azure में उस identity को permission भी चाहिए।

Example:

```text
Service Principal
       │
       ▼
Contributor
       │
       ▼
Resource Group
       │
       ├── VM
       ├── VNet
       └── Storage
```

यानी:

```text
Authentication
      +
Authorization
      =
Successful Azure Operation
```

---

# 📍 16. RBAC Scope

Azure RBAC अलग-अलग scopes पर assign हो सकता है:

```text
Management Group
       ↓
Subscription
       ↓
Resource Group
       ↓
Resource
```

Example:

```text
Service Principal
       │
       ▼
Contributor
       │
       ▼
Axion-System-RG
```

तो identity उस Resource Group के scope में required operations कर सकती है।

---

# ⚠️ 17. Subscription Level Contributor देना जरूरी है?

**नहीं, हर scenario में नहीं।**

अगर Pipeline को सिर्फ एक Resource Group manage करना है:

```text
Service Principal
       │
       ▼
Contributor
       │
       ▼
Specific Resource Group
```

यह broad subscription-level access देने से narrower scope है।

Microsoft security guidance भी service connections को required resources तक scope करने और unnecessary broad Contributor permissions avoid करने की recommendation देती है।

---

# 🧠 18. Least Privilege

Golden rule:

> **Pipeline को जितनी permission चाहिए, उतनी ही दो।**

Example:

```text
❌ Contributor → Entire Subscription
```

अगर काम सिर्फ:

```text
Axion-System-RG
```

पर होना है, तो architecture को narrower scope पर consider करें:

```text
✅ Contributor → Axion-System-RG
```

या जरूरत के अनुसार अधिक specific role।

---

# 🏢 19. Industrial Example

मान लो AUDIX के पास:

```text
Azure Subscription
│
├── SBI-RG
├── HDFC-RG
├── ICICI-RG
└── Internal-RG
```

SBI deployment pipeline को केवल:

```text
SBI-RG
```

access चाहिए।

तो ideal concept:

```text
SBI Pipeline
    │
    ▼
SBI Service Connection
    │
    ▼
SBI Identity
    │
    ▼
RBAC
    │
    ▼
SBI-RG
```

इससे identity का access unnecessarily पूरे subscription तक नहीं फैलता।

---

# 🔄 20. Pipeline में Service Connection कैसे Use होती है?

Conceptual YAML:

```yaml
steps:
- task: AzureCLI@2
  inputs:
    azureSubscription: 'Axion-Azure-Connection'
    scriptType: 'ps'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az account show
```

यहाँ:

```text
azureSubscription:
        │
        ▼
Service Connection Name
```

यह actual Azure Subscription password नहीं है।

यह Pipeline को configured Azure authentication connection use करने के लिए कहता है।

---

# 🧱 21. Service Connection और Agent का Relation

बहुत important:

```text
Agent
=
काम execute करता है
```

और:

```text
Service Connection
=
Azure authentication/connectivity provide करती है
```

Combined:

```text
Pipeline
   │
   ├───────────────┐
   ▼               ▼
Agent          Service Connection
   │               │
   ▼               ▼
Execute        Authenticate
   │               │
   └───────┬───────┘
           ▼
         Azure
```

---

# 🔥 22. Agent और Service Connection को कभी confuse मत करना

| Component          | काम                             |
| ------------------ | ------------------------------- |
| Pipeline           | Workflow                        |
| Agent Pool         | Agent group                     |
| Agent              | Job execution                   |
| Service Connection | External service authentication |
| Entra ID           | Identity provider               |
| Service Principal  | Application identity            |
| Managed Identity   | Azure-managed identity          |
| RBAC               | Authorization                   |
| Azure Resource     | Target                          |

---

# 🖱️ 23. Service Connection कहाँ बनती है?

Azure DevOps Project में:

```text
Project
   ↓
Project Settings
   ↓
Service connections
   ↓
New service connection
```

फिर:

```text
Azure Resource Manager
```

select किया जाता है। Microsoft की current documentation में Azure Resource Manager service connection के लिए यही flow documented है।

---

# 🛠️ 24. Recommended Modern Setup

नई Azure Resource Manager Service Connection के लिए conceptual choice:

```text
Azure Resource Manager
        │
        ▼
Workload Identity Federation
        │
        ├── App Registration
        │
        └── Managed Identity
```

Microsoft वर्तमान guidance में WIF को recommend करता है।

---

# 🔐 25. Automatic WIF Setup

अगर environment/prerequisites allow करते हैं, Azure DevOps automatic setup में:

```text
Project Settings
      ↓
Service Connections
      ↓
New Service Connection
      ↓
Azure Resource Manager
      ↓
App registration (automatic)
      ↓
Workload identity federation
```

Azure DevOps आवश्यक identity/federation configuration को setup करने में मदद कर सकता है।

Automatic app-registration flow के लिए Microsoft documentation में subscription `Owner` permission जैसी prerequisites बताई गई हैं।

---

# 🧩 26. अगर Owner Permission नहीं है?

यह हमारे practical lab के लिए बहुत important हो सकता है।

अगर user के पास Azure subscription पर required permissions नहीं हैं, automatic service connection creation fail हो सकती है।

तब architecture:

```text
You
 │
 ▼
Azure DevOps
 │
 ▼
Create Service Connection / Draft
 │
 ▼
Azure Admin / Identity Owner
 │
 ├── Create/modify Identity
 ├── Add Federated Credential
 └── Assign RBAC
 │
 ▼
You
 │
 ▼
Verify Service Connection
```

Microsoft documentation explicitly बताती है कि Azure DevOps और Azure/Entra permissions अलग हो सकती हैं।

---

# 🧠 27. Azure DevOps Permission vs Azure Permission

यह table याद रखो:

| Permission                | कहाँ चाहिए?      |
| ------------------------- | ---------------- |
| Create Service Connection | Azure DevOps     |
| Use Service Connection    | Azure DevOps     |
| Create App Registration   | Entra/Azure      |
| Add Federated Credential  | Entra / Identity |
| Assign Azure RBAC         | Azure            |
| Deploy Resource           | Azure            |

इसलिए किसी user के पास ADO admin access हो सकता है लेकिन Azure RBAC change करने की permission न हो।

---

# 🔗 28. Complete Authentication Flow

अब पूरा flow:

```text
Developer
    │
    ▼
Git Push
    │
    ▼
Azure DevOps Pipeline
    │
    ▼
Agent
    │
    ▼
Service Connection
    │
    ▼
Workload Identity Federation
    │
    ▼
Microsoft Entra ID
    │
    ▼
Identity
    │
    ▼
Azure RBAC
    │
    ▼
Subscription / RG / Resource
```

---

# 🧪 29. Example — Terraform Pipeline

मान लो हमारा Axion Terraform code Git repository में है:

```text
azure-devops-from-scratch
        │
        ▼
Terraform Code
        │
        ▼
Pipeline
        │
        ▼
Self-Hosted Agent
        │
        ▼
Terraform Init
        │
        ▼
Terraform Plan
        │
        ▼
Terraform Apply
        │
        ▼
Azure
```

Terraform को Azure में resources create करने के लिए authentication चाहिए।

Service Connection / identity architecture उस authentication और authorization model का हिस्सा हो सकती है।

---

# 🏗️ 30. Terraform + Service Connection

Conceptually:

```text
ADO Pipeline
     │
     ▼
Self-Hosted Agent
     │
     ▼
Terraform
     │
     ▼
Azure Authentication
     │
     ▼
Service Connection / Identity
     │
     ▼
RBAC
     │
     ▼
Azure
```

---

# ⚠️ 31. Service Connection का नाम Secret नहीं है

Example:

```yaml
azureSubscription: 'Axion-Azure-Connection'
```

यह सिर्फ configured endpoint/service connection को reference करता है।

इसलिए:

```text
Axion-Azure-Connection
```

को password समझना गलत है।

---

# 🔒 32. Grant Access to All Pipelines?

Service Connection create करते समय Azure DevOps में pipelines को access देने का option मिल सकता है।

Conceptually:

```text
❌ सभी Pipelines को unrestricted access
```

की जगह जहाँ possible हो:

```text
Specific Pipeline
       ↓
Service Connection
```

authorize करना बेहतर security practice हो सकता है।

Microsoft documentation भी `Grant access permission to all pipelines` को generally recommend नहीं करती और individual pipeline authorization को prefer करती है।

---

# 🧯 33. Service Connection Failure Troubleshooting

अगर Pipeline में:

```text
Azure authentication failed
```

आ रहा है तो यह checklist follow करो:

```text
1. Service Connection exists?
        ↓
2. Pipeline authorized?
        ↓
3. Identity exists?
        ↓
4. Federated credential exists?
        ↓
5. Issuer correct?
        ↓
6. Subject correct?
        ↓
7. RBAC assigned?
        ↓
8. Correct subscription?
        ↓
9. Correct tenant?
        ↓
10. Task supports WIF?
```

Microsoft की troubleshooting guidance भी issuer, subject identifier और task WIF support verify करने को कहती है।

---

# ❌ 34. Common Error — Permission

Example:

```text
AuthorizationFailed
```

मतलब identity authenticate हो सकती है, लेकिन उसके पास required Azure permission नहीं है।

Concept:

```text
Authentication ✅
Authorization ❌
```

---

# ❌ 35. Common Error — Federated Credential

अगर:

```text
Issuer mismatch
```

या:

```text
Subject mismatch
```

तो:

```text
ADO Service Connection
        │
        ▼
Generated values
        │
        ❌
        │
Azure Federated Credential
```

values match नहीं कर रही हो सकती हैं।

Microsoft इस exact mismatch को WIF troubleshooting scenario के रूप में document करता है।

---

# ❌ 36. Common Error — Wrong Scope

Example:

```text
Identity
   ↓
Contributor
   ↓
RG-A
```

लेकिन Pipeline:

```text
RG-B
```

में deployment करने की कोशिश कर रही है।

Result:

```text
Authorization Failed
```

क्योंकि RBAC scope गलत है।

---

# 🧠 37. Service Connection vs Subscription

एक common misconception:

> "Service Connection ही Azure Subscription है।"

❌ गलत।

Correct:

```text
Azure Subscription
=
Azure resources का billing/management boundary
```

और:

```text
Service Connection
=
Azure DevOps से Azure authentication/configuration connection
```

---

# 🧠 38. Service Connection vs App Registration

ये भी अलग concepts हैं।

```text
App Registration
=
Entra में application identity definition
```

और:

```text
Service Connection
=
Azure DevOps में configured connection/endpoint
```

Architecture:

```text
App Registration
       │
       ▼
Service Principal
       │
       ▼
Federated Credential
       │
       ▼
Service Connection
       │
       ▼
ADO Pipeline
```

---

# 🧠 39. Service Connection vs RBAC

ये भी अलग हैं।

```text
Service Connection
=
"Azure से connect कैसे करना है?"
```

```text
RBAC
=
"Azure में क्या करने देना है?"
```

Example:

```text
Service Connection
        │
        ▼
Identity = Axion-Pipeline-Identity
        │
        ▼
RBAC = Contributor
        │
        ▼
Scope = Axion-System-RG
```

---

# 🏭 40. Industrial Architecture — AUDIX

एक production-style architecture:

```text
                         🏢 AUDIX
                            │
                            ▼
                    Azure DevOps Org
                            │
                            ▼
                       SBI Project
                            │
              ┌─────────────┴─────────────┐
              ▼                           ▼
        Git Repository                Pipeline
                                          │
                                          ▼
                                  Self-Hosted Agent
                                          │
                                          ▼
                                  Service Connection
                                          │
                                          ▼
                                Microsoft Entra ID
                                          │
                                          ▼
                                   Application Identity
                                          │
                                          ▼
                                         RBAC
                                          │
                                          ▼
                                      SBI-RG
                                          │
                              ┌───────────┼───────────┐
                              ▼           ▼           ▼
                             VM          VNet       Storage
```

---

# 🔥 41. Golden Security Model

Production में ideal mental model:

```text
Pipeline
   │
   ▼
Specific Service Connection
   │
   ▼
Specific Identity
   │
   ▼
Minimum Required RBAC
   │
   ▼
Specific Scope
```

मतलब:

```text
❌ One identity → Everything
```

के बजाय:

```text
✅ Pipeline → Specific Identity → Specific Scope
```

---

# 🧠 42. पूरा Topic एक Diagram में

```text
                         PIPELINE
                            │
                            ▼
                    SERVICE CONNECTION
                            │
                            ▼
                 WORKLOAD IDENTITY
                    FEDERATION
                            │
                            ▼
                   MICROSOFT ENTRA ID
                            │
                ┌───────────┴───────────┐
                ▼                       ▼
         Service Principal       Managed Identity
                │                       │
                └───────────┬───────────┘
                            ▼
                           RBAC
                            │
                            ▼
                     Azure Scope
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
        Subscription   Resource Group   Resource
```

---

# 🎤 43. Interview Questions

### Q1. Service Connection क्या है?

Azure DevOps Pipeline और external service जैसे Azure के बीच configured authenticated connection।

---

### Q2. Service Connection और Agent में difference?

```text
Agent
→ Pipeline Job execute करता है

Service Connection
→ External service authentication/connectivity provide करती है
```

---

### Q3. Authentication और Authorization में difference?

```text
Authentication → WHO ARE YOU?

Authorization → WHAT CAN YOU DO?
```

---

### Q4. Service Principal क्या है?

Microsoft Entra में application identity का tenant-local representation, जिसका उपयोग workloads द्वारा Azure resources access करने के लिए किया जा सकता है।

---

### Q5. Managed Identity क्या है?

Azure-managed service principal identity, जिसमें credential management Azure करता है।

---

### Q6. Workload Identity Federation क्यों?

Long-lived secrets manage किए बिना supported workload authentication model provide करने के लिए।

---

### Q7. क्या Service Connection खुद RBAC role है?

नहीं।

```text
Service Connection ≠ RBAC
```

Service Connection authentication/configuration का हिस्सा है और Azure RBAC identity को Azure resources पर authorization देता है।

---

### Q8. क्या Service Connection को पूरे Subscription पर Contributor देना चाहिए?

हर scenario में नहीं।

Required scope तक permission limit करना least-privilege approach है।

---

# 🚦 44. इस Phase के बाद हमारा Mental Model

अब तक:

```text
Organization
     ↓
Project
     ↓
Repository
     ↓
Pipeline
     ↓
Agent Pool
     ↓
Agent
     ↓
Service Connection
     ↓
Identity
     ↓
RBAC
     ↓
Azure
```

अब Azure DevOps और Azure के बीच की पूरी basic bridge समझ में आनी चाहिए।

---

# 🚀 45. Next Phase

अब अगला बहुत important topic होगा:

```text
09 — Microsoft Entra ID + Workload Identity Federation
```

जहाँ हम practically समझेंगे:

```text
App Registration
      ↓
Service Principal
      ↓
Federated Credential
      ↓
Issuer
      ↓
Subject
      ↓
Audience
      ↓
RBAC
      ↓
Service Connection
```

इसके बाद हम actual **Azure DevOps → Azure Pipeline deployment** करेंगे।

# 🧪 46. Hands-on Lab — Create Azure Service Connection

अब theory को actual Azure DevOps + Azure lab में convert करेंगे।

हमारा target:

```text
Azure DevOps Project
        │
        ▼
Service Connection
        │
        ▼
Workload Identity Federation
        │
        ▼
Microsoft Entra Identity
        │
        ▼
Azure RBAC
        │
        ▼
Azure Resource Group
```

---

# 🎯 Lab Objective

इस lab के end में हमारे पास:

```text
Service Connection
        │
        ├── Authentication → Workload Identity Federation
        │
        ├── Identity       → Entra Application Identity
        │
        ├── Authorization  → Azure RBAC
        │
        └── Scope          → Selected Azure Resource Group
```

और Pipeline इस Service Connection के through Azure से बात कर सकेगी।

---

# 🧱 Lab Scenario

हम अपने learning project के लिए यह naming use करेंगे:

```text
Azure DevOps Organization
    ↓
Project
    ↓
Service Connection
    ↓
Azure Subscription
    ↓
Resource Group
```

Recommended lab names:

```text
Project:
Azure-DevOps-Lab

Service Connection:
sc-axion-azure

Resource Group:
rg-axion-devops-lab
```

> अगर तुम्हारे existing Project/Resource Group का नाम अलग है, वही use कर सकते हो।

---

# ⚠️ Before Starting

इस lab के लिए ideally तुम्हारे पास:

```text
✅ Azure DevOps Organization
✅ Azure DevOps Project
✅ Azure Subscription
✅ Azure Portal access
✅ Required Azure permissions
✅ Service Connection creation permission
```

Automatic App Registration + WIF flow के लिए Microsoft documentation में Azure subscription पर **Owner** permission prerequisite बताई गई है। अगर तुम्हारे पास यह permission नहीं है, तो नीचे manual/managed-identity route use करना पड़ सकता है।

---

# 🧪 Lab Part 1 — Create a Dedicated Resource Group

पहले हम lab के लिए dedicated Resource Group बनाएँगे।

## क्यों?

क्योंकि हम Service Connection की permission पूरे subscription पर unnecessarily broad नहीं रखना चाहते।

Architecture:

```text
Azure Subscription
        │
        ├── Other Resources
        │
        └── rg-axion-devops-lab
                │
                └── Lab Resources
```

Microsoft भी Service Connections को required resource/group scope तक limit करने की recommendation देता है।

---

## 🖱️ Azure Portal

Azure Portal में:

```text
Portal
  ↓
Resource groups
  ↓
Create
```

Fill:

```text
Subscription:
<Your Subscription>

Resource group:
rg-axion-devops-lab

Region:
<Your preferred region>
```

फिर:

```text
Review + create
        ↓
Create
```

---

# ✅ Verification 1

Resource Group दिखाई देना चाहिए:

```text
rg-axion-devops-lab
```

Expected:

```text
Azure
└── Subscription
      └── rg-axion-devops-lab
```

---

# 🧪 Lab Part 2 — Open Azure DevOps Project

अब Azure DevOps खोलो।

Path:

```text
Azure DevOps
   ↓
Organization
   ↓
Your Project
   ↓
Project Settings
   ↓
Service connections
```

Microsoft की current documentation में Azure Resource Manager Service Connection इसी Project Settings → Service connections area से create की जाती है।

---

# 🧪 Lab Part 3 — Create New Service Connection

Click:

```text
New service connection
```

अब service type में select करो:

```text
Azure Resource Manager
```

फिर:

```text
Next
```

---

# 🧪 Lab Part 4 — Authentication Method

अब Azure DevOps authentication options दिखाएगा।

हमारा target:

```text
App registration
+
Workload Identity Federation
```

Automatic option available हो तो:

```text
App registration (automatic)
Credential:
Workload identity federation
```

select करो।

Microsoft नए Azure Resource Manager Service Connections के लिए WIF-based authentication को recommended approach बताता है।

---

# 🧪 Lab Part 5 — Select Scope

अब Azure DevOps को बताना है कि Service Connection किस Azure scope से connect करेगी।

हम lab में:

```text
Scope level:
Resource Group
```

select करेंगे, अगर UI में Resource Group scope available हो।

फिर:

```text
Subscription:
<Your Azure Subscription>

Resource Group:
rg-axion-devops-lab
```

Concept:

```text
Subscription
     │
     └── rg-axion-devops-lab
              ↑
              │
        Service Connection Scope
```

> अगर तुम्हारे current UI में केवल Subscription scope दिखता है, तो Subscription select करके आगे बढ़ सकते हो; बाद में Azure RBAC को Resource Group scope पर assign करके access को narrow किया जा सकता है।

---

# 🧪 Lab Part 6 — Service Connection Name

Service Connection का name:

```text
sc-axion-azure
```

Description:

```text
Azure connection for Axion learning pipeline
```

Naming pattern:

```text
sc-<project>-<environment>
```

Examples:

```text
sc-axion-dev
sc-axion-uat
sc-axion-prod
```

Production में descriptive naming बहुत useful होती है।

---

# ⚠️ Lab Part 7 — "Grant Access to All Pipelines"

यह checkbox दिख सकता है:

```text
☐ Grant access permission to all pipelines
```

हम learning lab में इसे **unchecked** रखेंगे।

क्यों?

क्योंकि:

```text
All Pipelines
     ↓
Service Connection
```

की बजाय:

```text
Specific Pipeline
     ↓
Service Connection
```

अधिक controlled approach है।

Microsoft भी all-pipelines access को generally recommend नहीं करता और individual pipeline authorization prefer करता है।

---

# 💾 Lab Part 8 — Save

अब:

```text
Save
```

करो।

अगर automatic WIF setup successful हुआ तो Azure DevOps required identity/federation configuration create करने में मदद करेगा।

---

# 🔍 Lab Part 9 — Verify Service Connection

अब वापस:

```text
Project Settings
   ↓
Service connections
```

तुम्हें दिखना चाहिए:

```text
sc-axion-azure
```

Status ideally:

```text
Verified
```

या successful connection state।

---

# 🧠 इस समय Background में क्या हुआ?

यह सबसे important हिस्सा है।

हमने सिर्फ UI में connection create नहीं की।

Conceptually:

```text
Azure DevOps
     │
     ▼
Service Connection
     │
     ▼
Entra Identity
     │
     ▼
Federated Credential
     │
     ▼
Azure Authentication
```

और Azure side पर identity को required Azure scope पर authorization चाहिए।

---

# 🧪 Lab Part 10 — Check Azure Identity

Azure Portal में जाओ:

```text
Microsoft Entra ID
   ↓
App registrations
```

Automatic flow में Azure DevOps संबंधित application identity create कर सकता है। Microsoft documentation automatic setup में app registration और WIF configuration creation describe करती है।

Search में service connection-related identity देखो।

---

# 🔎 क्या-क्या Note करना है?

Identity मिलने पर इन values को समझो:

```text
Application (client) ID
Directory (tenant) ID
Object / Principal ID
```

Concept:

```text
Client ID
   ↓
Application Identity

Tenant ID
   ↓
Microsoft Entra Tenant

Principal/Object ID
   ↓
Identity Object
```

> इन IDs को password/secret मत समझना। इन्हें Git repository में secret की तरह hard-code करने की जरूरत नहीं है।

---

# 🧪 Lab Part 11 — Check Federated Credential

App Registration में:

```text
App Registration
   ↓
Certificates & secrets
   ↓
Federated credentials
```

यहाँ federated credential दिखाई दे सकती है।

Concept:

```text
Azure DevOps
      │
      │ Issuer
      │ Subject
      ▼
Federated Credential
      │
      ▼
Entra Identity
```

Federated credential external workload के token और Entra identity के बीच trust relationship establish करती है।

---

# 🧪 Lab Part 12 — Understand Issuer + Subject

WIF में दो values बहुत important हैं:

```text
Issuer
Subject
```

Mental model:

```text
Issuer
=
Token कहाँ से आया?

Subject
=
कौन सा workload/service connection है?
```

अगर ये values expected configuration से match नहीं करतीं तो authentication fail हो सकती है। Microsoft की troubleshooting documentation issuer और subject identifier matching को explicitly check करने को कहती है।

---

# 🧪 Lab Part 13 — Check Azure RBAC

अब Azure Portal में:

```text
rg-axion-devops-lab
   ↓
Access control (IAM)
   ↓
Role assignments
```

यहाँ हमें Service Connection की identity के लिए required role देखना है।

Learning lab में example:

```text
Role:
Contributor

Scope:
rg-axion-devops-lab
```

Concept:

```text
Identity
   │
   ▼
Contributor
   │
   ▼
rg-axion-devops-lab
```

> Production में `Contributor` automatically best choice नहीं है। Actual workload के लिए minimum required role use करना चाहिए।

---

# 🧠 Authentication + Authorization Check

अब पूरा setup:

```text
                 PIPELINE
                    │
                    ▼
           SERVICE CONNECTION
                    │
                    ▼
          WORKLOAD IDENTITY
             FEDERATION
                    │
                    ▼
             ENTRA IDENTITY
                    │
                    ▼
                  RBAC
                    │
                    ▼
          rg-axion-devops-lab
```

अगर कोई एक layer missing है:

```text
Authentication ❌
        OR
Authorization ❌
```

Pipeline Azure operation fail कर सकती है।

---

# 🧪 Lab Part 14 — Create a Test Pipeline

अब actual proof करेंगे।

Repository में एक test pipeline YAML बनाओ:

```text
service-connection-test.yml
```

Content:

```yaml
trigger: none

pool:
  vmImage: ubuntu-latest

steps:
- task: AzureCLI@2
  displayName: "Test Azure Service Connection"
  inputs:
    azureSubscription: "sc-axion-azure"
    scriptType: "bash"
    scriptLocation: "inlineScript"
    inlineScript: |
      echo "Azure authentication successful"
      az account show
```

---

# 🧠 इस YAML को समझो

यह line:

```yaml
azureSubscription: "sc-axion-azure"
```

हमारी Service Connection को reference करती है।

यह:

```yaml
pool:
  vmImage: ubuntu-latest
```

बताता है कि इस test के लिए Microsoft-hosted Ubuntu agent use होगा।

इस lab में हमारा focus:

```text
Agent
   +
Service Connection
```

दोनों concepts को connect करना है।

---

# 🧪 Lab Part 15 — Run Pipeline

Azure DevOps में:

```text
Pipelines
   ↓
New Pipeline
```

अपना repository select करो।

YAML file:

```text
service-connection-test.yml
```

फिर:

```text
Run
```

---

# 🔍 Pipeline Flow

अब actual execution:

```text
Pipeline
   │
   ▼
Ubuntu Agent
   │
   ▼
AzureCLI@2
   │
   ▼
Service Connection
   │
   ▼
WIF
   │
   ▼
Entra ID
   │
   ▼
Azure
   │
   ▼
az account show
```

---

# ✅ Expected Result

Pipeline successful होनी चाहिए:

```text
Azure authentication successful
```

और `az account show` Azure account/subscription information return करेगा।

इसका मतलब:

```text
Pipeline
   ↓
Service Connection
   ↓
Identity
   ↓
Azure
```

authentication path working है।

---

# 🧪 Lab Part 16 — Test Resource Group Access

अब थोड़ा stronger test करते हैं।

YAML:

```yaml
trigger: none

pool:
  vmImage: ubuntu-latest

steps:
- task: AzureCLI@2
  displayName: "Verify Azure Access"
  inputs:
    azureSubscription: "sc-axion-azure"
    scriptType: "bash"
    scriptLocation: "inlineScript"
    inlineScript: |
      echo "Connected Azure subscription:"
      az account show --query "{name:name,id:id,tenantId:tenantId}" -o table

      echo ""
      echo "Checking Resource Group:"
      az group show \
        --name rg-axion-devops-lab \
        --query "{name:name,location:location}" \
        -o table
```

---

# 🎯 Expected Output

अगर Service Connection और RBAC सही है:

```text
Connected Azure subscription:
Name
----------------
<your-subscription>

Checking Resource Group:
Name
----------------------
rg-axion-devops-lab
```

---

# 🧪 Lab Part 17 — Intentional Failure Test

अब troubleshooting सीखने के लिए intentionally wrong Resource Group name डाल सकते हो:

```yaml
--name rg-does-not-exist
```

Pipeline fail हो सकती है क्योंकि requested resource मौजूद नहीं है।

यह difference समझो:

```text
Authentication
      ↓
      ✅

Authorization
      ↓
      ✅

Resource
      ↓
      ❌ Not Found
```

यह **authentication failure नहीं** है।

---

# 🧪 Lab Part 18 — Understand Permission Failure

अब imagine करो identity के पास:

```text
Reader
```

role है लेकिन Pipeline कोई write operation करने की कोशिश करती है।

Example:

```text
Pipeline
   ↓
Create Resource
   ↓
Azure
   ↓
❌ AuthorizationFailed
```

यहाँ:

```text
Authentication = ✅
Authorization  = ❌
```

यह distinction production troubleshooting में बहुत important है।

---

# 🔥 Lab Part 19 — Final Architecture

अब हमारा पूरा hands-on lab:

```text
┌──────────────────────────────┐
│       Azure DevOps           │
│                              │
│  Project                     │
│    │                         │
│    ├── Repository             │
│    │                         │
│    ├── Pipeline               │
│    │      │                   │
│    │      ▼                   │
│    │   AzureCLI Task          │
│    │      │                   │
│    │      ▼                   │
│    └── Service Connection     │
└────────────┬─────────────────┘
             │
             ▼
     Workload Identity
        Federation
             │
             ▼
      Microsoft Entra ID
             │
             ▼
        Application
         Identity
             │
             ▼
            RBAC
             │
             ▼
   rg-axion-devops-lab
             │
             └── Azure Resources
```

---

# 🧠 Lab Verification Checklist

Lab complete मानने से पहले:

```text
☐ Resource Group created

☐ Service Connection created

☐ WIF selected

☐ Service Connection verified

☐ Entra identity identified

☐ Federated credential verified

☐ RBAC assignment verified

☐ Pipeline created

☐ AzureCLI task executed

☐ az account show successful

☐ Resource Group query successful
```

---

# 🧯 Troubleshooting Matrix

| Error                              | सबसे पहले क्या check करें                 |
| ---------------------------------- | ----------------------------------------- |
| Service Connection creation failed | Azure DevOps + Azure permissions          |
| Subscription दिखाई नहीं दे रही     | Azure access / subscription permission    |
| Authentication failed              | WIF / identity / issuer / subject         |
| AuthorizationFailed                | Azure RBAC                                |
| ResourceGroupNotFound              | Resource Group name/scope                 |
| Pipeline unauthorized              | Service Connection pipeline authorization |
| Agent task failed                  | Agent/tool/task problem                   |
| `az account show` fails            | Authentication/service connection         |
| Resource operation denied          | RBAC role/scope                           |

---

# 🔐 Production Rules

### Rule 1 — WIF prefer करो

जहाँ supported हो:

```text
WIF
```

को long-lived client secrets के बजाय prefer करो। Microsoft current guidance में WIF recommended है।

---

### Rule 2 — Broad RBAC avoid करो

```text
❌ Subscription → Contributor
```

जब workload को केवल:

```text
Resource Group
```

की जरूरत हो।

Required scope तक access limit करना बेहतर है।

---

### Rule 3 — All Pipelines Access avoid करो

```text
❌ All pipelines
```

की जगह:

```text
✅ Required pipeline only
```

authorize करो।

---

### Rule 4 — Secrets Git में नहीं

कभी भी:

```text
client_secret
password
PAT
certificate private key
```

Git repository में hard-code मत करो।

---

# 🏆 What We Actually Built

इस lab में हमने सिर्फ एक button click नहीं किया।

हमने पूरा trust chain बनाया:

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
Microsoft Entra Identity
        │
        ▼
Azure RBAC
        │
        ▼
Azure Resource Group
```

और फिर Pipeline से actual Azure command execute करके verify किया।

---

# 🎓 Interview-Level Answer

अगर interview में पूछा जाए:

> "How does an Azure DevOps pipeline authenticate to Azure?"

तुम confidently explain कर सकते हो:

```text
Azure DevOps Pipeline
        ↓
Azure Resource Manager Service Connection
        ↓
Workload Identity Federation
        ↓
Microsoft Entra Identity
        ↓
Azure RBAC
        ↓
Target Azure Resource
```

और साथ में:

> **Authentication identity establish करती है, जबकि Azure RBAC उस identity को required authorization देता है।**

---

# 🚀 Next Hands-on Lab

अब अगला phase होगा:

```text
09 — Microsoft Entra ID + Workload Identity Federation Deep Dive
```

इसमें हम अलग-अलग objects को **Azure Portal में खोलकर** समझेंगे:

```text
App Registration
       ↓
Enterprise Application
       ↓
Service Principal
       ↓
Object ID
       ↓
Client ID
       ↓
Tenant ID
       ↓
Federated Credential
       ↓
Issuer
       ↓
Subject
       ↓
Audience
       ↓
RBAC
```

और फिर समझेंगे कि Service Connection के पीछे वास्तव में **Entra में क्या-क्या create हुआ**।
