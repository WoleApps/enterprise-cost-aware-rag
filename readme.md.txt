# Enterprise-Grade, Cost-Aware RAG Pipeline on AWS

[![AWS Certified Solutions Architect](https://img.shields.io/badge/AWS-Solutions%20Architect%20Professional-orange?style=flat-square&logo=amazon-aws)](https://aws.amazon.com)
[![Terraform](https://img.shields.io/badge/Terraform-v1.5+-purple?style=flat-square&logo=terraform)](https://www.terraform.io)
[![Python](https://img.shields.io/badge/Python-3.11+-blue?style=flat-square&logo=python)](https://www.python.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

An enterprise-ready, serverless Retrieval-Augmented Generation (RAG) architecture engineered with **real-time financial governance, token spend protection, and automated anomaly detection**. Designed to eliminate runaway LLM API costs and infinite-loop client retries before they impact operational budgets.

---

## Architecture & Data Flow

```mermaid
graph TD
    Client[Client Application / API Gateway] -->|POST /query| Lambda[AWS Lambda: RAG Orchestrator]
    
    subgraph "Vector Search Layer"
        Lambda -->|Query Vector| AOSS[Amazon OpenSearch Serverless]
        AOSS -->|Top-K Context Chunks| Lambda
    end
    
    subgraph "Inference & Governance Layer"
        Lambda -->|Generate Embedding| Titan[Amazon Bedrock: Titan Embed]
        Lambda -->|Converse API Request| Claude[Amazon Bedrock: Claude 3.5 Sonnet]
        Claude -->|Synthesized Response| Lambda
    end

    subgraph "Observability & Cost Guardrails"
        Bedrock -.->|Native Metrics| CW[Amazon CloudWatch]
        CW -->|Token Count Metric Math| Alarm1[Token Spend Spike Alarm]
        CW -->|ML Anomaly Detection Band| Alarm2[Error Rate Anomaly Alarm]
        Alarm1 -->|SNS Notification| SNS[AWS SNS Topic]
        Alarm2 -->|SNS Notification| SNS
        SNS -->|Alerts| Ops[Engineering / PagerDuty / Email]
    end