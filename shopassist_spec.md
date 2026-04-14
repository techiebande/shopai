# ShopAssist AI — Product Specification Document
**The $15/month Shopify & WooCommerce AI Support Chatbot**

| | |
|---|---|
| **Version** | 1.0 — Initial Spec |
| **Date** | April 2026 |
| **Status** | Pre-build — Research validated |
| **Author** | Solo founder |
| **Target market** | US/UK Shopify & WooCommerce store owners |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Product Overview](#2-product-overview)
3. [Technical Specification](#3-technical-specification)
4. [Business Model](#4-business-model)
5. [Compliance Specification](#5-compliance-specification)
6. [Competitive Positioning](#6-competitive-positioning)
7. [Distribution & Go-to-Market](#7-distribution--go-to-market)
8. [Launch Artifacts Checklist](#8-launch-artifacts-checklist)
9. [Key Metrics & Success Criteria](#9-key-metrics--success-criteria)

---

## 1. Executive Summary

ShopAssist AI is a $15/month AI-powered support chatbot purpose-built for Shopify and WooCommerce stores. It connects directly to a store's product catalog and FAQ data via a RAG (Retrieval-Augmented Generation) pipeline, enabling accurate, real-time answers about products, orders, shipping, and returns — without requiring a human agent.

The market gap is clear: the leading competitor (Chatbase) scores 2.1/5 on Trustpilot due to a confusing credit model, no native Shopify integration, and no human handoff. ShopAssist AI is 53–62% cheaper, with transparent flat-fee pricing and features Chatbase deliberately omits.

This document consolidates all verified research findings into a buildable, compliance-ready, financially modelled specification.

| Metric | Value |
|--------|-------|
| Price | $15/mo flat — vs $40+ for Chatbase (53% cheaper) |
| Target containment rate | 86–89% (industry benchmark: 86%) |
| Ramen-profitable milestone | 100 stores (~$1,300/mo net) |

---

## 2. Product Overview

### 2.1 Problem Statement

50%+ of Shopify merchants plan to implement AI tools, but only 36% of top stores have actually adopted chatbots. The adoption gap exists because current solutions are either too expensive, too generic, or technically broken for e-commerce use cases.

> **Primary complaint (400+ Chatbase reviews):** Credit system is confusing and unpredictable. Chatbot goes completely silent when credits run out. No live order lookup. No human escalation.
> *— SiteGPT analysis of 400+ Chatbase reviews, Dec 2025*

### 2.2 Solution

ShopAssist AI is a store-native chatbot that:

- Syncs product catalog, FAQ, and policy data from Shopify/WooCommerce via API
- Answers product, order, shipping, and returns questions using RAG over the store's own data
- Escalates to a human when it cannot answer confidently
- Charges a transparent flat monthly fee with no hidden credits
- Is GDPR and EU AI Act compliant from day one

### 2.3 Target Users

**Primary:** US/UK Shopify and WooCommerce store owners with 100–10,000 SKUs and 50–500 support queries per day.

**Secondary:** Their end customers (shoppers) who interact with the widget.

### 2.4 Key Differentiators

No verified competitor offers flat-fee, unlimited-messaging, full Shopify/WooCommerce product catalog RAG sync at $15/month. The closest competitor is Chatty at $19.99 with interaction limits and no catalog sync.

---

## 3. Technical Specification

### 3.1 System Architecture

The system consists of four layers: data ingestion (Shopify/WooCommerce API sync), vector storage (Supabase pgvector), inference (OpenAI GPT-4o-mini + embeddings), and delivery (widget embed on merchant storefront).

### 3.2 Platform Integrations

#### Shopify

Use the Shopify Admin REST API. Pull: product titles, descriptions, variants, pricing, shipping policy, and order status. All reads are via OAuth app — read-only permissions only. App Store listing requires OAuth review by Shopify.

- Endpoints: `/admin/api/2024-01/products.json`, `/orders.json`
- Sync schedule: full catalog nightly, incremental delta every 15 minutes
- Auth: OAuth 2.0 — never store Admin API keys client-side

#### WooCommerce

Use WooCommerce REST API v3 via Consumer Key + Consumer Secret. Always use Read-Only permissions to prevent accidental data modification. API calls take 500ms–2s, so always use cached sync rather than live queries per message.

- Endpoints: `/wp-json/wc/v3/products`, `/products/attributes`, `/orders`
- Sync schedule: nightly full sync; webhook triggers for product updates
- Coverage: Shopify (29% US market) + WooCommerce (23% US market) = 52% of target market

### 3.3 RAG Pipeline

#### Chunking Strategy

- Product catalog: one embedding per product (title + description + variants + price)
- FAQ and policy documents: chunk by heading
- Average product entry: ~500 tokens — embedding 500 products costs $0.005 (essentially zero)

#### Vector Storage

Supabase pgvector is the chosen vector database. It rivals dedicated solutions (Pinecone) in cost-efficiency while keeping all data in a single Postgres instance. Production benchmarks: 1.6M+ embeddings with sub-50ms query times using HNSW indexing.

> **Infrastructure requirement:** Supabase Pro ($25/month) is mandatory before serving paying customers — the free tier has no uptime SLA.

#### Inference Model

Primary model: GPT-4o-mini ($0.15/M input, $0.60/M output tokens). At 500 conversations/day (800 input + 400 output tokens each), API cost is $4.05/month — making $15/month pricing extremely viable.

> **Model cost warning:** Switching to GPT-5.4 Standard for the same 500-conversation volume would cost $518/month. Model choice is the single most important financial decision in this product.
> *— CloudZero OpenAI cost guide, April 2026*

#### Escalation Flow

When confidence score falls below threshold (suggested: 0.72), the chatbot must offer human handoff. 87% of consumers prefer hybrid AI + human support. Pure-bot experiences without escalation fail trust tests.

### 3.4 Infrastructure Budget

| Component | Monthly cost | Notes |
|-----------|-------------|-------|
| Supabase Pro (pgvector + DB) | $25 | Mandatory — free tier has no SLA |
| Vercel Hobby (Next.js frontend) | $0 | Upgrade to Pro ($20/mo) if needed |
| OpenAI API (GPT-4o-mini) | ~$4 per 500 stores/day | $0.15/M input, $0.60/M output |
| OpenAI Embeddings (text-embedding-3-small) | ~$0.01 | $0.02/M tokens — near zero |
| Paddle MoR fees | 5% + $0.50/txn | Handles all VAT/tax globally |
| **Total at launch (50 stores)** | **~$60–75** | **Fully ramen-viable** |

### 3.5 SLA Commitments

- **Uptime:** 99.9% (guaranteed by Supabase Pro — ~8.7 hours downtime/year)
- **Response latency:** 1–3 seconds per message (pgvector <50ms + GPT-4o-mini 500ms–2s)
- Must be disclosed to merchants at signup — do not promise sub-1s response times

### 3.6 Message Cap Policy

A per-store message cap is required to protect margin and create upsell triggers. A store must send ~83,000 messages/month before API costs alone reach $15 — but caps prevent edge-case abuse and enable tiered pricing.

- Starter plan: 2,000 messages/month included
- Overage: $0.01 per message above cap (creates a natural upsell trigger)
- AppSumo LTD tier (if pursued): 500 messages/month hard cap — non-negotiable for LTD viability

---

## 4. Business Model

### 4.1 Pricing

| Benchmark | Value |
|-----------|-------|
| ShopAssist AI | $15/mo flat fee |
| Chatbase Hobby | $40/mo (53% more expensive) |
| Average Shopify app cost | $66.54/mo (ShopAssist is 77% below average) |

The core pricing narrative is **flat fee vs. credit model**. Chatbase's professional unbranded setup costs $100+/month when add-ons are included ($40 base + $39 branding removal + $59 custom domain). ShopAssist AI includes all of this at $15.

> **Post-PMF consideration:** Usage-based pricing (flat $15 + overage above 2,000 messages) delivers 10% higher NRR and 22% lower churn vs pure flat-fee. Test after reaching 100 stores.
> *— RockingWeb SaaS Benchmark Report, 2025*

### 4.2 Unit Economics

| Metric | Value |
|--------|-------|
| LTV at 5% monthly churn | $300 |
| LTV at 3% monthly churn | $500 |
| Max sustainable CAC (3:1 ratio) | $100 |

SMB monthly churn averages 3–5%. Plan for 5% churn as the base assumption. A 2-point improvement in churn is worth $200 in LTV per customer — making retention the single highest-leverage revenue action.

Primary churn drivers: poor onboarding (time-to-value > 7 days), feature gaps, and competitive switching. Target first-value moment within 7 days of signup.

### 4.3 Profitability Milestones

| Stores | MRR | Net/month | Paddle fees | Infra cost |
|--------|-----|-----------|-------------|------------|
| 50 | $750 | ~$615 | ~$60 | $75 |
| 100 | $1,500 | ~$1,300 | ~$125 | $75 |
| 200 | $3,000 | ~$2,650 | ~$250 | $100 |
| 500 | $7,500 | ~$6,750 | ~$625 | $125 |

Ramen-profitable at 100 stores (~$1,300/month net after Paddle fees and infrastructure). A Nigerian founder's living expenses are covered at this milestone with zero offshore entity required.

### 4.4 Entity & Payment Setup

| Option | Cost | Incorporation | Recommended? |
|--------|------|---------------|--------------|
| Paddle (MoR) | 5% + $0.50/txn | Not required | Yes — solo founders |
| Startbutton Africa | Varies | Not required | Yes — Africa-first |
| Stripe Atlas | $500 + ~$475/yr | Delaware LLC | Only if Stripe ecosystem needed |

Paddle is the recommended starting point: no incorporation required, Merchant of Record handles all global VAT and chargebacks, Nigerian founders receive payouts via Payoneer or Grey.co. Stripe Atlas is only worth pursuing if the Stripe ecosystem is strategically required — the $475+/year ongoing cost and mandatory IRS Form 5472 filing (non-filing penalty: $25,000) are significant overhead for a solo founder at early stage.

---

## 5. Compliance Specification

### 5.1 Regulatory Landscape

Chatbots are classified as **'limited risk'** under the EU AI Act. Non-compliance fines can reach €35M or 7% of global revenue — double the GDPR maximum. 73% of AI agent implementations in EU companies in 2024 had GDPR vulnerabilities. Compliance is a non-negotiable launch requirement, not a v2 feature.

### 5.2 Compliance Checklist

| Requirement | Regulation | Action |
|-------------|------------|--------|
| Disclose AI identity to users | EU AI Act | Add 'Powered by AI' label in widget |
| Explicit consent before data collection | GDPR Art. 6 | Consent checkbox before first message |
| Sign DPA with OpenAI | GDPR Art. 28 | Use API/Business tier only — not free ChatGPT |
| Conduct DPIA | GDPR Art. 35 | Required before EU merchant onboarding |
| Data retention limits | GDPR | Auto-delete conversations after 90 days (configurable) |
| Data deletion mechanism | GDPR | Merchant dashboard: delete all store data |
| Data minimization | GDPR | Collect only product + FAQ data; no PII in embeddings |
| CCPA opt-out | CCPA | Privacy page + 'Do not sell my data' link for CA users |

### 5.3 DPIA Requirement

A Data Protection Impact Assessment (DPIA) is mandatory under GDPR Article 35 for AI chatbots likely to create high risk to individuals. This must be completed before onboarding EU merchants. A DPIA template is a required launch artifact.

### 5.4 OpenAI DPA

OpenAI's Data Processing Agreement for GDPR compliance is only available on business/API accounts. Building on the OpenAI API is compliant. Using the free ChatGPT tier for a commercial product is not. This must be verified before any EU merchant goes live.

---

## 6. Competitive Positioning

### 6.1 Feature Comparison Matrix

| Feature | ShopAssist AI | Chatbase | Chatty | SiteGPT |
|---------|--------------|---------|--------|---------|
| Price/month | $15 flat | $40+ base | $19.99 | ~$39 |
| Shopify order lookup | Yes (RAG) | No | No | No |
| WooCommerce support | Yes | No | No | No |
| Human handoff | Yes | No | No | No |
| Usage limits | Message cap | Credits run out silently | Interaction limits | Credits |
| Branding removal | Included | $39/month add-on | Included | Paid |
| GDPR / EU AI Act ready | Yes | Partial | Unknown | Unknown |
| Trustpilot score | N/A (new) | 2.1 / 5 | 4.9 / 5 | Not listed |

### 6.2 Positioning Statement

ShopAssist AI is the only flat-fee AI chatbot with native Shopify and WooCommerce product catalog sync at under $20/month. No credits. No silent failures. No surprise charges. Set up in 5 minutes. 53% cheaper than Chatbase — and it actually knows your store.

### 6.3 Chatbase Switch Triggers

Primary switch triggers identified from 400+ verified reviews:

- Chatbot went silent — credits ran out without warning
- GPT-4 model costs 20x more credits, making advanced features unaffordable
- No Shopify order lookup — couldn't answer "where is my order?"
- No human handoff — shoppers left conversations unresolved
- Removing Chatbase branding costs an extra $39/month

### 6.4 Shopify App Store Strategy

15,508 apps are on the Shopify App Store as of November 2025. The 'Built for Shopify' badge is now the definitive benchmark for high-growth merchants — only 1,083 apps hold it. Certification is a medium-term priority (3–6 months post-launch).

Ranking is driven by review velocity and quality, keyword optimization in title and description, and regular app updates. 45.71% of all Shopify apps offer a free plan — a free tier or free trial will accelerate App Store discovery.

---

## 7. Distribution & Go-to-Market

### 7.1 Channel Prioritization

| Channel | Avg CAC | Viable? | Notes |
|---------|---------|---------|-------|
| SEO / Content | $53 | Yes | 3.3x better unit economics than paid social |
| Email marketing | $53 | Yes | Within $100 CAC ceiling |
| Shopify App Store | Low / organic | Yes | Review velocity critical |
| Community (Reddit, FB) | Near zero | Yes | Founder presence required |
| Meta / Google Ads | $937 | **No** | Structurally unviable at $15 ARPU |

Paid acquisition via Meta or Google Ads is structurally unviable: at $15 ARPU and 5% monthly churn, LTV is $300. A 3:1 LTV:CAC ratio allows a maximum $100 CAC. Social ads average $937 CAC — nearly 10x the ceiling. All acquisition must be organic.

### 7.2 SEO Keyword Strategy

High-competition terms to build toward over time: "best AI chatbot for Shopify" (Shopify.com ranks #1) and "AI chatbot for ecommerce."

Low-competition, high-intent targets for launch:

- "Chatbase alternative" — captures dissatisfied competitor users at decision stage
- "Chatbase for Shopify" — intent-rich, moderate competition
- "Cheap Shopify chatbot" — price-sensitive buyers
- "AI support chatbot $15" — bottom-of-funnel price searchers
- "WooCommerce chatbot RAG" — underserved, high merchant intent, low existing content

Traffic from generative AI sources to US retail sites grew 4,700% YoY by mid-2025. Building content for AI citation is now a distinct acquisition channel alongside traditional SEO.

### 7.3 Community Targeting

Key merchant communities: r/shopify (1M+ members), r/dropshipping, Facebook Groups (eCommerce Entrepreneurs, Shopify Entrepreneurs), Shopify Community forums, IndieHackers. The early majority has crossed for AI chatbot adoption — messaging should focus on ROI and simplicity, not AI novelty. Suggested hook: "Set up in 5 minutes, 53% cheaper than Chatbase."

### 7.4 AppSumo Assessment

AppSumo takes 70% of revenue. On a $59 LTD, the creator receives ~$17.70 while bearing recurring API costs for the lifetime of the product. This is structurally loss-making without strict message caps.

> **Decision:** Treat AppSumo as a marketing and validation expense, not a revenue strategy. If pursued, enforce a hard 500 messages/month cap on LTD tiers with paid overage. Non-negotiable.

### 7.5 Founder-Specific Risk Mitigations

A Nigerian founder serving US/UK customers faces structural risks that are all fully mitigated with the right tooling. Trust is built through product quality, App Store reviews, transparent pricing, and public founder presence — not geographic origin. 97% of retailers plan to increase AI spending in 2026.

- **Payment:** Paddle (no incorporation) or Startbutton Africa (NGN settlement) — Stripe Atlas not required
- **VAT/tax:** Fully handled by Paddle as Merchant of Record
- **USD receipt:** Payoneer or Grey.co for Paddle payouts
- **Trust:** Shopify App Store listing + 'Built for Shopify' certification as credibility anchors

---

## 8. Launch Artifacts Checklist

### 8.1 Pre-Build

- [ ] Unit cost model (100 / 500 / 1,000 stores)
- [ ] Message cap policy finalised
- [ ] Payment processor selected (Paddle recommended)
- [ ] OpenAI API business account with DPA signed
- [ ] Supabase Pro account (not free tier)
- [ ] Vendor ToS compliance register complete

### 8.2 Build

- [ ] Shopify Admin API OAuth integration (read-only)
- [ ] WooCommerce REST API v3 integration (read-only)
- [ ] Nightly product sync + incremental delta
- [ ] Supabase pgvector with HNSW index
- [ ] GPT-4o-mini inference with confidence scoring
- [ ] Human escalation flow (confidence threshold: 0.72)
- [ ] EU AI Act disclosure label in widget
- [ ] Consent checkbox before first message (GDPR)
- [ ] Conversation auto-delete at 90 days
- [ ] Merchant data deletion dashboard

### 8.3 Launch

- [ ] Shopify App Store listing with keyword-optimised title and description
- [ ] Free trial or free plan tier for App Store discovery
- [ ] Pricing page: flat-fee vs credit-model narrative
- [ ] DPIA template for EU merchants
- [ ] 'Chatbase alternative' landing page (SEO target)
- [ ] Founder presence on r/shopify and Shopify Community forums

### 8.4 Post-Launch Milestones

- [ ] **50 stores:** Validate containment rate (target: >86%), refine escalation threshold
- [ ] **100 stores:** Ramen-profitable — assess 'Built for Shopify' certification
- [ ] **200 stores:** Review pricing model — test usage-based overage tier
- [ ] **500 stores:** Consider exit-readiness documentation for Acquire.com

---

## 9. Key Metrics & Success Criteria

| Metric | Target | Source / Benchmark |
|--------|--------|--------------------|
| Containment rate | >86% | Tidio / industry benchmark, 2026 |
| Monthly churn | <5% (target <3%) | RockingWeb SaaS Benchmark, 2025 |
| LTV | $300 at 5% churn | Gatilab SaaS Metrics, 2026 |
| CAC ceiling | $100 (3:1 LTV:CAC) | Optifai Pipeline Study, 2026 |
| Time-to-value | <7 days post-signup | WeAreFounders SaaS benchmarks, 2026 |
| MRR growth (early stage) | 10–20% month-on-month | B2B SaaS benchmarks, RockingWeb, 2025 |
| Uptime SLA | 99.9% | Supabase Pro guarantee |
| Response latency | 1–3 seconds | pgvector + GPT-4o-mini benchmarks |
| App Store rating | >4.7 / 5 within 6 months | Shopify App Store ranking driver |

---

*All research verified April 2026. Sources referenced throughout this document are publicly available. This specification is confidential and intended for internal planning use.*
