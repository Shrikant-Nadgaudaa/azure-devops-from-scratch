# 🚀 STEP 1 — नया Organization

सबसे पहले सिर्फ organization बनाते हैं।

Microsoft का current GUI path:

```text
Azure DevOps
      ↓
Organization selector
      ↓
New organization
```

Microsoft के current documentation के अनुसार organization creation में organization name, hosting geography और billing Azure subscription select की जाती है।

### Organization Name

हम naming में company identity रखना चाहते हैं।

Suggested:

```text
AudixTechnology
```

अगर unavailable हो तो:

```text
AudixTechnologyDevOps
```

या:

```text
AudixTechnologyCloud
```

**पहला preferred नाम:** `AudixTechnology`

---

## Organization creation में

जब form आए:

```text
Organization name
Hosting geography
Azure subscription
```

तो:

### Organization

```text
AudixTechnology
```

### Geography

हमारे lab के लिए:

```text
India
```

अगर UI में India option उपलब्ध न हो तो Microsoft द्वारा उपलब्ध nearest appropriate geography चुनेंगे।

### Billing

अपनी active Azure subscription select करना।

Microsoft के अनुसार organization को Azure subscription से billing के लिए link किया जा सकता है।

---

# ⚠️ अभी बस इतना

**अभी Project मत बनाना।**

Organization create होने के बाद बस organization के अंदर चले जाना।

फिर हमारा next step होगा:

```text
AudixTechnology
       ↓
Project Architecture
       ↓
SBI
HDFC
TCS
BANDHAN
```

और उसके बाद हम पहला actual project बनाएँगे।

---

## 📌 हमारे practical rules

आगे से मैं हर step इसी format में दूँगा:

```text
🎯 Goal

🖱️ GUI Click Path

✏️ क्या भरना है

🔐 Security / Best Practice

🏗️ Azure DevOps में क्या बना

📁 GitHub में कौनसी .md file update होगी

✅ Expected Result

➡️ Next Step
```

और जहाँ Microsoft documentation useful होगी, मैं उसे भी साथ में reference करूँगा। Repo creation के लिए Microsoft का current path `Project → Repos → repository dropdown → New repository` है।

**अब हमारा पहला actual काम सिर्फ `AudixTechnology` Organization create करना है। उसके बाद सीधे Project पर चलते हैं।**



# 🚀 STEP 1 — नया Organization

सबसे पहले सिर्फ organization बनाते हैं।

Microsoft का current GUI path:

```text id="w50a2m"
Azure DevOps
      ↓
Organization selector
      ↓
New organization
```

Microsoft के current documentation के अनुसार organization creation में organization name, hosting geography और billing Azure subscription select की जाती है।

### Organization Name

हम naming में company identity रखना चाहते हैं।

Suggested:

```text id="je5ot3"
AudixTechnology
```

अगर unavailable हो तो:

```text id="fyhrb8"
AudixTechnologyDevOps
```

या:

```text id="44743r"
AudixTechnologyCloud
```

**पहला preferred नाम:** `AudixTechnology`

---

## Organization creation में

जब form आए:

```text id="hgeoyn"
Organization name
Hosting geography
Azure subscription
```

तो:

### Organization

```text id="acxiwj"
AudixTechnology
```

### Geography

हमारे lab के लिए:

```text id="qulad3"
India
```

अगर UI में India option उपलब्ध न हो तो Microsoft द्वारा उपलब्ध nearest appropriate geography चुनेंगे।

### Billing

अपनी active Azure subscription select करना।

Microsoft के अनुसार organization को Azure subscription से billing के लिए link किया जा सकता है।

---

# ⚠️ अभी बस इतना

**अभी Project मत बनाना।**

Organization create होने के बाद बस organization के अंदर चले जाना।

फिर हमारा next step होगा:

```text id="axxokb"
AudixTechnology
       ↓
Project Architecture
       ↓
SBI
HDFC
TCS
BANDHAN
```

और उसके बाद हम पहला actual project बनाएँगे।

---

# ✅ Expected Result

Organization creation successfully complete होने के बाद हमें यह स्थिति दिखाई देनी चाहिए:

```text
Azure DevOps
      ↓
AudixTechnology
```

Organization selector/open organization area में:

```text
Organization: AudixTechnology
```

दिखना चाहिए।

Organization के अंदर अभी **कोई client project बनाने की जरूरत नहीं है**।

Expected initial state:

```text
🏢 AudixTechnology
│
└── Projects
    └── अभी client projects create नहीं किए गए
```

### Verification Checklist

* [ ] `AudixTechnology` organization successfully created
* [ ] Organization open हो रहा है
* [ ] सही organization name दिखाई दे रहा है
* [ ] सही Azure DevOps account से organization accessible है
* [ ] Billing/subscription configuration complete है
* [ ] Hosting geography सही select हुई है
* [ ] अभी कोई SBI/HDFC/TCS/BANDHAN project नहीं बनाया गया है

### इसका मतलब क्या हुआ?

हमने सिर्फ **Audix Technology के लिए Azure DevOps की top-level boundary** बना दी है।

```text
AudixTechnology
       │
       └── अभी खाली organization
```

अगले step में इसी organization के अंदर client projects बनाए जाएँगे।

---

## 📌 हमारे practical rules

आगे से मैं हर step इसी format में दूँगा:

```text
🎯 Goal

🖱️ GUI Click Path

✏️ क्या भरना है

🔐 Security / Best Practice

🏗️ Azure DevOps में क्या बना

📁 GitHub में कौनसी .md file update होगी

✅ Expected Result

➡️ Next Step
```

और जहाँ Microsoft documentation useful होगी, मैं उसे भी साथ में reference करूँगा। Repo creation के लिए Microsoft का current path `Project → Repos → repository dropdown → New repository` है।

**अब हमारा पहला actual काम सिर्फ `AudixTechnology` Organization create करना है। उसके बाद सीधे Project पर चलते हैं।**
