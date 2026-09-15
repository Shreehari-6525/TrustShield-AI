# 🛡️ TrustShield AI

### AI-Powered Multi-Layer Cyber Threat Detection Platform

TrustShield AI is an AI-powered cybersecurity platform designed to detect and analyze digital threats across **URLs, emails, SMS messages, and files**.

The platform combines **Machine Learning, rule-based analysis, NLP, threat intelligence, and explainable AI** to generate an understandable risk score and threat verdict.

Our goal is to provide users with a **fast, explainable, and user-friendly security solution** through a Chrome Extension.

---

## 🚨 Problem Statement

Cyber threats such as phishing links, fraudulent emails, malicious SMS messages, and malware files are becoming increasingly common.

Existing security solutions are often focused on individual attack vectors or provide limited explanations about **why** a particular input is considered dangerous.

TrustShield AI aims to address this by bringing multiple detection mechanisms together into a single platform with an explainable risk assessment.

---

## 💡 Proposed Solution

TrustShield AI uses a layered detection architecture:

```text
User Input
    │
    ├── URL
    ├── Email
    ├── SMS
    └── File
    │
    ▼
Chrome Extension
    │
    ▼
Spring Boot Backend
    │
    ▼
Python FastAPI AI Service
    │
    ├── Machine Learning
    ├── NLP
    ├── Rule-Based Analysis
    └── File Analysis
    │
    ▼
Explainable AI
    │
    ▼
Risk Engine
    │
    ├── ML Score
    ├── Rule Score
    ├── Reputation
    └── Threat Intelligence
    │
    ▼
Final Risk Score & Verdict
    │
    ▼
Chrome Extension
```

---

## 🎯 Current Detection Scope

TrustShield AI currently focuses on four major detection vectors:

| Detection Type | Purpose                                       |
| -------------- | --------------------------------------------- |
| 🔗 **URL**     | Detect suspicious and phishing URLs           |
| 📧 **Email**   | Detect phishing and suspicious email content  |
| 💬 **SMS**     | Detect spam, phishing and suspicious messages |
| 📁 **File**    | Analyze files for potential malware           |

> **Note:** QR codes, screenshots, and general image detection are not part of the current implementation scope.

---

## ✨ Key Features

* 🔗 AI-powered URL threat detection
* 📧 Email phishing detection
* 💬 SMS phishing and spam detection
* 📁 File malware analysis
* 🤖 Machine Learning-based detection
* 🧠 NLP-based email and SMS analysis
* 📊 Explainable risk scoring
* 🔍 "Why harmful?" threat explanations
* 🛡️ Rule-based security analysis
* 🧬 YARA and ClamAV-based file analysis
* 🌐 Supplementary threat intelligence
* ⚡ Redis caching for frequently requested results
* 🔐 Secure authentication using Spring Security and JWT
* 📜 Scan history and results
* 🧩 Chrome Extension-based user interface

---

## 🧠 Explainable AI

TrustShield AI is designed not only to identify a threat but also to explain the factors contributing to the prediction.

For example:

```text
Risk Score: 91 / 100
Verdict: MALICIOUS

Why was it flagged?

✓ Suspicious URL structure
✓ Phishing-related keywords detected
✓ Unusual number of subdomains
✓ Suspicious reputation indicators
✓ ML model detected phishing patterns
```

Explainable AI techniques such as **SHAP** can be used to identify important feature contributions from supported machine-learning models.

The objective is to make security results understandable to normal users instead of showing only a technical prediction.

---

## 🏗️ System Architecture

```text
                  TRUSTSHIELD AI
                        │
                        ▼
              ┌──────────────────┐
              │ Chrome Extension │
              │ React + JavaScript│
              │   Manifest V3    │
              └────────┬─────────┘
                       │
                       ▼
              ┌──────────────────┐
              │   Spring Boot    │
              │    Java 21       │
              │ Main Backend/API │
              └───────┬──────────┘
                      │
              ┌───────┴────────┐
              ▼                ▼
       ┌─────────────┐   ┌─────────────┐
       │    MySQL    │   │    Redis    │
       │  Database   │   │    Cache    │
       └─────────────┘   └─────────────┘
                      │
                      ▼
              ┌──────────────────┐
              │   Python FastAPI │
              │    AI Service    │
              └────────┬─────────┘
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
     Scikit-learn   PyTorch    Transformers
          │            │            │
          └────────────┼────────────┘
                       ▼
                 SHAP / XAI
                       │
                       ▼
                Risk Engine
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
     YARA + ClamAV          Threat Intelligence
                                  │
                              VirusTotal
```

---

## 🛠️ Technology Stack

| Layer               | Technology                | Purpose                            |
| ------------------- | ------------------------- | ---------------------------------- |
| Frontend            | React + JavaScript        | Extension user interface           |
| Chrome Extension    | Manifest V3               | Browser integration                |
| Main Backend        | Java 21 + Spring Boot     | APIs and system orchestration      |
| Security            | Spring Security + JWT     | Authentication and authorization   |
| AI Service          | Python + FastAPI          | AI/ML model serving                |
| Machine Learning    | Scikit-learn              | Traditional ML models              |
| Deep Learning       | PyTorch                   | Deep-learning models               |
| NLP                 | Hugging Face Transformers | Email and SMS analysis             |
| Explainable AI      | SHAP                      | Model explanations                 |
| Database            | MySQL                     | Persistent application data        |
| Cache               | Redis                     | Frequently accessed temporary data |
| File Analysis       | YARA + ClamAV             | Malware and pattern analysis       |
| Threat Intelligence | VirusTotal                | Supplementary threat intelligence  |
| Containerization    | Docker                    | Consistent deployment              |
| Version Control     | Git + GitHub              | Collaboration and version control  |

---

## 🔄 URL Detection Flow

The initial development flow focuses on completing one complete detection pipeline before expanding to the other vectors.

```text
User enters URL
       ↓
Chrome Extension
       ↓
Spring Boot API
       ↓
Python FastAPI
       ↓
URL Feature Extraction
       ↓
ML Prediction
       ↓
SHAP Explanation
       ↓
Risk Engine
       ↓
MySQL
       ↓
Final Result
       ↓
Chrome Extension
```

---

## 📊 Risk Assessment

TrustShield AI combines multiple signals to calculate a final risk score.

Conceptually:

```text
ML Detection
      +
Rule Analysis
      +
Reputation
      +
Threat Intelligence
      ↓
Risk Engine
      ↓
Final Risk Score
      ↓
SAFE / SUSPICIOUS / MALICIOUS
```

The scoring weights and verdict thresholds will be validated and tuned during development and testing.

---

## 📁 Repository Structure

```text
TrustShield-AI/
│
├── backend/
│   └── README.md
│
├── ai-service/
│   └── README.md
│
├── frontend/
│   └── README.md
│
├── database/
│   └── README.md
│
├── docs/
│   └── API_CONTRACT.md
│
├── .gitignore
└── README.md
```

---

## 👥 Team

| Member              | Responsibility                                                    |
| ------------------- | ----------------------------------------------------------------- |
| **Shreehari Patil** | Backend Development, Spring Boot, APIs, Risk Engine & Integration |
| **Rajnandini**      | AI/ML, Python, FastAPI, Model Development & NLP                   |
| **Tushar Mante**    | Database Design, MySQL & Data Management                          |
| **Ashish**          | Frontend Development & UI/UX                                      |

---

## 📌 Development Strategy

TrustShield AI is being developed incrementally.

### Phase 1 — Foundation

* Repository setup
* Backend initialization
* AI service initialization
* Database schema
* API contract
* UI/UX research

### Phase 2 — URL Detection

* URL feature extraction
* ML model
* FastAPI endpoint
* Spring Boot integration
* Risk scoring
* Explainable result

### Phase 3 — Email Detection

* Email preprocessing
* NLP model
* Phishing classification
* Backend integration

### Phase 4 — SMS Detection

* SMS preprocessing
* NLP classification
* Risk assessment
* Backend integration

### Phase 5 — File Detection

* ClamAV integration
* YARA rules
* File feature extraction
* ML-based analysis
* Risk assessment

### Phase 6 — Integration & Testing

* Complete Chrome Extension integration
* Security testing
* API testing
* Model evaluation
* Performance testing
* Final deployment

---

## 🔐 Security Approach

TrustShield AI follows a layered detection approach instead of depending on a single security mechanism.

```text
              ┌─────────────────┐
              │ Machine Learning│
              └────────┬────────┘
                       │
              ┌────────▼────────┐
              │ Rule-Based      │
              │ Analysis        │
              └────────┬────────┘
                       │
              ┌────────▼────────┐
              │ Reputation /    │
              │ Threat Intel    │
              └────────┬────────┘
                       │
              ┌────────▼────────┐
              │   Risk Engine   │
              └────────┬────────┘
                       │
                       ▼
                 Final Verdict
```

External threat-intelligence services are treated as **supplementary verification sources**, while our own detection logic and machine-learning models remain central to the platform.

---

## 📚 Documentation

* [API Contract](docs/API_CONTRACT.md)
* Backend Documentation
* AI Service Documentation
* Database Documentation
* Frontend Documentation

Additional documentation will be added as development progresses.

---

## 🚧 Project Status

**Current Status: 🟡 Development**

The project foundation and team development structure have been established.

Current focus:

```text
GitHub Setup
     ↓
API Contract
     ↓
Backend Foundation
     ↓
AI Service Foundation
     ↓
Database Foundation
     ↓
URL Detection
     ↓
Email
     ↓
SMS
     ↓
File
     ↓
Complete Integration
```

---

## 🔮 Future Scope

Possible future improvements include:

* Advanced zero-day threat detection
* Improved model generalization
* Additional threat-intelligence sources
* Advanced behavioral malware analysis
* Improved explainability
* Browser-wide real-time protection
* Additional security platforms and integrations
* Mobile application support

---

## ⚠️ Disclaimer

TrustShield AI is an academic and research-oriented cybersecurity project.

Detection results are intended to assist users in identifying potentially harmful content and should not be considered a guaranteed replacement for professional security solutions.

---

## ⭐ Project Goal

> **Detect threats. Explain the risk. Protect the user.**

TrustShield AI aims to make cybersecurity **intelligent, explainable, and accessible** through a unified platform.
