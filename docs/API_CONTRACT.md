# TrustShield AI — API Contract

## 1. Purpose

This document defines the communication contract between the TrustShield Spring Boot backend and the Python FastAPI AI/ML service.

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
