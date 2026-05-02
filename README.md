# 🚀 Multi-Domain Support Triage Agent

An intelligent support triage system that classifies, prioritizes, and safely responds to customer support tickets using a hybrid rule-based and LLM-driven approach.

---

## 📌 Overview

This project implements a terminal-based support triage agent capable of handling support queries across multiple domains:

- HackerRank  
- Claude  
- Visa  

The system processes incoming support tickets, determines their type, evaluates risk and urgency, retrieves relevant support documentation, and decides whether to respond automatically or escalate to a human agent.

---

## 🧠 Approach

The system follows a hybrid pipeline combining rule-based filtering with AI-driven reasoning:

### 1. Risk Detection
- Identifies sensitive issues such as:
  - Fraud
  - Billing disputes
  - Account access problems
- High-risk cases are escalated immediately

### 2. Invalid Query Detection
- Filters out:
  - Malicious inputs
  - Irrelevant or nonsensical queries

### 3. Corpus Retrieval
- Matches user queries with relevant support documentation
- Uses keyword-based scoring to identify best-fit content

### 4. LLM-Based Response Generation
- Generates responses using a constrained prompt
- Ensures answers are:
  - Grounded in the provided support corpus
  - Safe and non-hallucinated

### 5. Decision Engine
- Final output decision:
  - ✅ Respond directly
  - ⚠️ Escalate to human support

---

## ⚙️ How It Works

1. Input CSV file containing support issues  
2. Each issue is analyzed and classified  
3. System determines:
   - Request type  
   - Product area  
   - Risk level  
4. Relevant documentation is retrieved  
5. Response is generated OR escalated  
6. Output is saved as structured CSV  

---

## 🛠️ Tech Stack

- Python  
- Rule-based NLP (keyword detection)  
- LLM Integration (Claude API)  
- CSV Processing  

---

## ▶️ How to Run

### 1. Clone the repository
```bash
git clone https://github.com/1bi24ri011-hash-dev/support-triage-agent.git
cd support-triage-agent
