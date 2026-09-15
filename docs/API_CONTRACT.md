# TrustShield AI — API Contract

## 1. Purpose

This document defines the communication contract between the TrustShield AI Spring Boot backend and the Python FastAPI AI/ML service.

The purpose of this contract is to ensure that both backend developers follow the same request and response structure during integration.

---

## 2. Architecture

```text
Chrome Extension
       |
       | REST API
       v
Spring Boot
       |
       | Internal AI API
       v
Python FastAPI
       |
       v
ML / NLP Models
       |
       v
Prediction + Confidence + Reasons
       |
       v
Spring Boot Risk Engine
       |
       v
Final Risk Score + Verdict
       |
       v
MySQL
       |
       v
Chrome Extension
```

### Main Responsibilities

**Chrome Extension**
- Provides the user interface.
- Accepts URLs, emails, SMS and files.
- Sends scan requests to Spring Boot.
- Displays the final result.

**Spring Boot**
- Acts as the central backend.
- Handles authentication and authorization.
- Validates requests.
- Communicates with FastAPI.
- Performs rule-based analysis.
- Integrates reputation and threat-intelligence information.
- Calculates the final risk score.
- Stores scan information in MySQL.

**FastAPI**
- Acts as the AI/ML service.
- Extracts relevant features.
- Runs machine-learning/NLP models.
- Generates prediction and confidence.
- Provides model-related reasons and features.

---

## 3. URL Detection API

### Endpoint

```
POST /predict/url
```

### Purpose

This endpoint is used by Spring Boot to send a URL to the Python AI/ML service for phishing detection.

### Request

```json
{
  "url": "https://example.com"
}
```

### Request Fields

| Field | Type   | Required | Description                     |
|-------|--------|----------|----------------------------------|
| url   | String | Yes      | URL that needs to be analyzed   |

### Example Request

```json
{
  "url": "https://example.com/login"
}
```

### Response

```json
{
  "prediction": "SAFE",
  "confidence": 0.90,
  "reasons": [
    "No major suspicious URL characteristics detected"
  ],
  "features": {
    "url_length": 24,
    "has_https": true,
    "subdomain_count": 0,
    "has_ip_address": false
  }
}
```

### Response Fields

| Field      | Type    | Description                                         |
|------------|---------|------------------------------------------------------|
| prediction | String  | AI prediction such as SAFE or PHISHING              |
| confidence | Decimal | Model confidence between 0.0 and 1.0                |
| reasons    | Array   | Human-readable reasons generated from model analysis |
| features   | Object  | Extracted URL features used by the model            |

---

## 4. Prediction Values

The AI service may initially return:

- `SAFE`
- `PHISHING`

Additional prediction classes may be introduced if required by the final ML model.

> The prediction returned by FastAPI is an AI/ML signal. It is **not** the final TrustShield verdict.

---

## 5. Confidence

The confidence value must be returned as a decimal between:

```
0.0 and 1.0
```

**Examples:**

| Value | Meaning         |
|-------|-----------------|
| 0.90  | 90% confidence  |
| 0.75  | 75% confidence  |
| 0.94  | 94% confidence  |

The exact interpretation of confidence depends on the selected ML model.

---

## 6. Reasons

The AI service should provide human-readable reasons explaining important detected patterns.

**Example:**

```json
[
  "Suspicious URL structure",
  "Phishing-related keywords detected",
  "Unusual number of subdomains"
]
```

These reasons are intended to help the Spring Boot backend and Chrome Extension provide an explainable result to the user.

The final explanation may combine AI-generated reasons with rule-based, reputation and threat-intelligence reasons.

---

## 7. Features

The AI service may return extracted URL features.

**Example:**

```json
{
  "url_length": 87,
  "has_https": true,
  "subdomain_count": 3,
  "has_ip_address": false
}
```

**Possible URL features include:**

- URL length
- HTTPS usage
- Number of subdomains
- IP address presence
- Special characters
- Suspicious keywords
- URL shortening
- Domain-related characteristics

The final feature set will depend on the selected ML model.

---

## 8. Explainability

TrustShield AI aims to provide an understandable explanation for the prediction.

**The general flow is:**

```text
Input
  |
  v
Feature Extraction
  |
  v
ML Model
  |
  v
Prediction
  |
  v
Explainability Layer
  |
  v
Important Features / Reasons
```

For suitable ML models, SHAP may be used to identify which features contributed to the model prediction.

**Example:**

```
Prediction: PHISHING
Confidence: 0.94

Important factors:
- Suspicious URL structure
- Phishing-related keywords
- Excessive subdomains
- Unusual URL length
```

The explainability layer does not independently determine whether the input is malicious. The ML model performs the prediction, while explainability helps communicate the contributing factors.

---

## 9. Responsibility — FastAPI

The Python AI service is responsible for:

- Receiving detection input.
- Validating the AI request.
- Extracting relevant features.
- Preprocessing the input.
- Running the machine-learning or NLP model.
- Generating the prediction.
- Generating confidence.
- Generating model-related reasons.
- Returning relevant extracted features.
- Returning a structured JSON response.

> FastAPI should focus on AI/ML processing. It should **not** calculate the final TrustShield risk score.

---

## 10. Responsibility — Spring Boot

Spring Boot is responsible for:

- Receiving requests from the Chrome Extension.
- Validating user requests.
- Authenticating and authorizing users.
- Creating scan records.
- Calling the FastAPI AI service.
- Receiving the AI response.
- Performing rule-based analysis.
- Integrating reputation information.
- Integrating supplementary threat intelligence.
- Combining detection signals.
- Calculating the final TrustShield risk score.
- Determining the final verdict.
- Storing scan information in MySQL.
- Returning the final structured result to the Chrome Extension.

---

## 11. Important Architecture Rule

The FastAPI AI service should **not** determine the final TrustShield risk score. FastAPI provides the machine-learning signal.

The Spring Boot Risk Engine combines multiple signals:

```text
ML Prediction
      +
Rule-Based Analysis
      +
Reputation
      +
Threat Intelligence
      |
      v
Final TrustShield Risk Score
```

**The initial conceptual scoring model may be:**

```text
Final Score =
ML × 0.40
+
Rules × 0.25
+
Reputation × 0.20
+
Threat Intelligence × 0.15
```

The weights and final thresholds will be validated and tuned during testing.

**Initial conceptual verdict ranges may be:**

| Score Range | Verdict     |
|-------------|-------------|
| 0–30        | SAFE        |
| 31–60       | SUSPICIOUS  |
| 61–100      | MALICIOUS   |

These values are subject to change based on testing and model evaluation.

---

## 12. Complete URL Detection Flow

```text
User enters URL
       |
       v
Chrome Extension
       |
       | POST request
       v
Spring Boot
       |
       | Validate request
       v
FastAPI /predict/url
       |
       v
Feature Extraction
       |
       v
ML Model
       |
       v
Prediction + Confidence
       |
       v
Explainability / Reasons
       |
       v
Spring Boot
       |
       +---- Rule-Based Analysis
       |
       +---- Reputation Check
       |
       +---- Threat Intelligence
       |
       v
Risk Engine
       |
       v
Final Risk Score + Verdict
       |
       v
MySQL
       |
       v
Chrome Extension
       |
       v
User sees result
```

---

## 13. Example AI Response

Example response from FastAPI:

```json
{
  "prediction": "PHISHING",
  "confidence": 0.94,
  "reasons": [
    "Suspicious URL structure",
    "Phishing-related keywords detected",
    "Unusual number of subdomains"
  ],
  "features": {
    "url_length": 87,
    "has_https": true,
    "subdomain_count": 3,
    "has_ip_address": false
  }
}
```

Spring Boot receives this response and uses it as one input to the Risk Engine.

---

## 14. Example Final Backend Response

After processing the AI result, rules, reputation and supplementary threat intelligence, Spring Boot may return:

```json
{
  "verdict": "MALICIOUS",
  "riskScore": 91,
  "confidence": 0.94,
  "reasons": [
    "Suspicious URL structure",
    "Phishing-related keywords detected",
    "Unusual number of subdomains",
    "Multiple threat indicators detected"
  ],
  "recommendation": "Do not open this URL or enter personal information."
}
```

### Final Response Fields

| Field          | Type    | Description                          |
|----------------|---------|----------------------------------------|
| verdict        | String  | Final TrustShield verdict            |
| riskScore      | Number  | Final risk score from 0–100          |
| confidence     | Decimal | Relevant model confidence            |
| reasons        | Array   | Human-readable explanation           |
| recommendation | String  | Recommended action for the user      |

---

## 15. Final Verdict Values

The initial TrustShield verdict categories are:

| Verdict     | Meaning                                                                                   |
|-------------|--------------------------------------------------------------------------------------------|
| SAFE        | The analyzed input does not show significant suspicious indicators.                       |
| SUSPICIOUS  | The input contains some potentially dangerous or unusual characteristics and requires caution. |
| MALICIOUS   | The input contains strong indicators of phishing, malware or other malicious activity.    |

The final thresholds may be tuned after testing.

---

## 16. Error Response

If the AI service cannot process the request:

```json
{
  "error": "Unable to analyze URL",
  "message": "The AI service could not process the supplied URL."
}
```

Appropriate HTTP status codes should be used.

**Example:**

| Status Code | Meaning                                              |
|-------------|--------------------------------------------------------|
| 400 Bad Request | For invalid input.                                 |
| 500 Internal Server Error | For unexpected server-side errors.       |
| 503 Service Unavailable | When the AI service is temporarily unavailable. |

---

## 17. Invalid Request Example

If the URL is missing:

```json
{
  "url": ""
}
```

The service should return an appropriate validation error.

**Example:**

```json
{
  "error": "Invalid URL",
  "message": "A valid URL is required."
}
```

---

## 18. Service Communication

Spring Boot communicates with FastAPI using HTTP REST APIs.

**Example:**

```text
Spring Boot
      |
      | HTTP POST
      | /predict/url
      v
FastAPI
      |
      | JSON Response
      v
Spring Boot
```

The AI service should return JSON responses so that the Spring Boot backend can easily process the prediction.

---

## 19. Internal API Boundary

The FastAPI API is considered an internal AI service.

```text
External/User Request
        |
        v
Chrome Extension
        |
        v
Spring Boot
        |
        v
Internal FastAPI Service
```

The Chrome Extension should not directly communicate with the FastAPI service. Spring Boot remains the central backend entry point.

---

## 20. Data Flow Between Services

### Request

```text
Chrome Extension
       |
       v
Spring Boot
       |
       v
FastAPI
```

### AI Response

```text
FastAPI
       |
       v
Prediction
Confidence
Reasons
Features
       |
       v
Spring Boot
```

### Final Response

```text
Spring Boot
       |
       v
Risk Score
Verdict
Reasons
Recommendation
       |
       v
Chrome Extension
```

---

## 21. Database Integration

Spring Boot is responsible for storing scan information in MySQL.

**Typical information includes:**

- User
- Scan
- Scan Type
- Input
- Status
- Risk Score
- Verdict
- Confidence
- Reasons
- Recommendation
- Timestamp

**Example database flow:**

```text
Scan Request
     |
     v
Spring Boot
     |
     v
Create Scan Record
     |
     v
AI Analysis
     |
     v
Risk Engine
     |
     v
Store Result
     |
     v
Store Reasons
```

---

## 22. Scan Types

TrustShield AI currently supports the following detection categories:

- URL
- EMAIL
- SMS
- FILE

The URL API is the first API to be implemented. The remaining APIs will follow the same overall architecture.

---

## 23. Future Detection APIs

The same architecture will later support:

```
POST /predict/email
POST /predict/sms
POST /predict/file
```

The exact request and response structures will be finalized when each detection module is implemented.

### Email

Possible processing:

```text
Email
  |
  v
Text / Header / Link Analysis
  |
  v
NLP / ML
  |
  v
Prediction + Reasons
```

### SMS

Possible processing:

```text
SMS
  |
  v
Text Preprocessing
  |
  v
NLP / ML
  |
  v
Prediction + Reasons
```

### File

Possible processing:

```text
Uploaded File
      |
      v
ClamAV + YARA
      |
      v
File Analysis / ML
      |
      v
Detection Signals
      |
      v
Spring Boot Risk Engine
```

---

## 24. Authentication

Authentication and authorization are handled by Spring Boot.

**The planned security architecture uses:**

```text
Spring Security
       +
JWT
```

**General flow:**

```text
User Login
    |
    v
Spring Boot
    |
    v
Authentication
    |
    v
JWT Generated
    |
    v
Client Stores Token
    |
    v
Future API Request
    |
    v
JWT Validation
    |
    v
Allow / Reject Request
```

The FastAPI AI service does not manage the application's user authentication.

---

## 25. API Versioning

The initial internal API will use:

```
/predict/url
```

API versioning can be introduced later if required.

**Possible future structure:**

```
/api/v1/predict/url
```

The final versioning strategy will be decided before production deployment.

---

## 26. Request and Response Format

All APIs should use JSON for structured request and response data wherever applicable.

**Example:**

```
Content-Type: application/json
```

**Example request:**

```json
{
  "url": "https://example.com"
}
```

**Example response:**

```json
{
  "prediction": "SAFE",
  "confidence": 0.90
}
```

---

## 27. API Naming Convention

Detection endpoints should follow:

```
/predict/<detection-type>
```

**Examples:**

```
/predict/url
/predict/email
/predict/sms
/predict/file
```

This naming convention should remain consistent across the project.

---

## 28. Integration Contract

The following contract must be followed during Spring Boot ↔ FastAPI integration.

### Spring Boot sends

```json
{
  "url": "https://example.com"
}
```

### FastAPI returns

```json
{
  "prediction": "SAFE",
  "confidence": 0.90,
  "reasons": [
    "No major suspicious URL characteristics detected"
  ],
  "features": {
    "url_length": 19,
    "has_https": true,
    "subdomain_count": 0,
    "has_ip_address": false
  }
}
```

### Spring Boot then

```text
Receive AI Response
       |
       v
Apply Rules
       |
       v
Check Reputation
       |
       v
Check Threat Intelligence
       |
       v
Calculate Risk Score
       |
       v
Determine Verdict
       |
       v
Store Result
       |
       v
Return Final Response
```

---

## 29. Development Rule

The API contract should be finalized before implementing the complete integration.

If the request or response structure needs to change:

1. Discuss the change with both backend developers.
2. Update this document.
3. Update the FastAPI implementation.
4. Update the Spring Boot integration.
5. Test the complete request-response flow.
6. Commit the API contract change to GitHub.

---

## 30. Current Implementation Priority

**The development order is:**

```text
1. URL Detection
       |
       v
2. Email Detection
       |
       v
3. SMS Detection
       |
       v
4. File Detection
```

**The first complete integration target is:**

```text
Chrome Extension
       |
       v
Spring Boot
       |
       v
FastAPI
       |
       v
URL ML Model
       |
       v
Risk Engine
       |
       v
MySQL
       |
       v
Chrome Extension
```

---

## 31. Definition of API Contract Completion

The initial API contract is considered complete when:

- [ ] URL endpoint is defined.
- [ ] URL request format is defined.
- [ ] URL response format is defined.
- [ ] Prediction values are defined.
- [ ] Confidence format is defined.
- [ ] Reasons format is defined.
- [ ] Feature format is defined.
- [ ] FastAPI responsibilities are defined.
- [ ] Spring Boot responsibilities are defined.
- [ ] Risk Engine responsibility is defined.
- [ ] Error response is defined.
- [ ] Future detection endpoints are identified.
- [ ] Spring Boot ↔ FastAPI integration flow is documented.

Future changes will be documented in this file as the implementation evolves.

---

## 32. Summary

TrustShield AI follows a layered architecture where Spring Boot acts as the central backend and Python FastAPI acts as the independent AI/ML service.

**The main principle is:**

```text
FastAPI
    |
    | AI/ML Signal
    v
Spring Boot
    |
    +-- Rules
    +-- Reputation
    +-- Threat Intelligence
    |
    v
Risk Engine
    |
    v
Final Verdict
```

**The system is designed to provide:**

- Detection
- Risk Scoring
- Explainability
- Threat Intelligence
- User-Friendly Results

**The current detection scope is:**

- URL
- EMAIL
- SMS
- FILE

The goal of this API contract is to maintain a clear and consistent communication structure between all TrustShield AI components during development.
