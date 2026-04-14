# ShopAssist AI — Product Specification Document
**The $15/month Shopify AI Support Chatbot**

| | |
|---|---|
| **Version** | 2.0 — Audit-Revised Spec |
| **Date** | April 2026 |
| **Status** | Pre-build — Ready for development |
| **Author** | Solo founder |
| **Target market** | US/UK Shopify store owners |
| **Changelog from v1.0** | Architecture corrected (order lookup, cost model, multi-tenancy); WooCommerce deferred to v1.1; unlimited/cap contradiction resolved; human handoff fully specified; error states added; auth spec added; missing components filled |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Product Overview](#2-product-overview)
3. [Technical Specification](#3-technical-specification)
4. [Business Model](#4-business-model)
5. [Compliance Specification](#5-compliance-specification)
6. [Competitive Positioning](#6-competitive-positioning)
7. [Distribution & Go-to-Market](#7-distribution--go-to-market)
8. [Merchant Onboarding Flow](#8-merchant-onboarding-flow)
9. [Launch Artifacts Checklist](#9-launch-artifacts-checklist)
10. [Key Metrics & Success Criteria](#10-key-metrics--success-criteria)
11. [Appendix — Deferred Scope (v1.1)](#11-appendix--deferred-scope-v11)

---

## 1. Executive Summary

ShopAssist AI is a **$15/month AI-powered support chatbot purpose-built for Shopify stores**. It connects directly to a store's product catalog and FAQ data via a RAG (Retrieval-Augmented Generation) pipeline for product questions, and performs **live authenticated API calls** for real-time order status — two separate, correctly-scoped subsystems.

The market gap is clear: the leading competitor (Chatbase) scores 2.1/5 on Trustpilot due to a confusing credit model, no native Shopify integration, and no human handoff. ShopAssist AI is 53–62% cheaper, with transparent flat-fee pricing including a clearly disclosed message cap, and features Chatbase deliberately omits.

WooCommerce support is **deferred to v1.1** to keep the initial build focused and the solo-founder support burden manageable. Shopify (29% US market share) alone covers the core ICP.

> **Pre-build validation requirement:** Before committing to full build, conduct 5–10 interviews with active Shopify store owners via r/shopify and Shopify Community forums to validate willingness to pay $15/month and confirm real switching intent from Chatbase. The business case is research-grounded but not yet primary-validated.

| Metric | Value |
|--------|-------|
| Price | $15/mo flat — vs $40+ for Chatbase (53% cheaper) |
| Target containment rate | >86% (industry benchmark: 86%) |
| Ramen-profitable milestone | 100 stores (~$1,312/mo net) |
| v1.0 platform scope | Shopify only |
| v1.1 platform scope | + WooCommerce |

---

## 2. Product Overview

### 2.1 Problem Statement

50%+ of Shopify merchants plan to implement AI tools, but only 36% of top stores have actually adopted chatbots. Current solutions are too expensive, credit-based (unpredictable cost), or technically broken for real e-commerce use cases — specifically, they cannot look up live order data and have no human escalation path.

> **Primary complaint from 400+ Chatbase reviews:** Credit system is confusing and unpredictable. Chatbot goes completely silent when credits run out. No live order lookup. No human escalation.
> *— SiteGPT analysis of 400+ Chatbase reviews, Dec 2025*

### 2.2 Solution

ShopAssist AI is a store-native chatbot that:

- Syncs product catalog, FAQ, and policy data from Shopify via API and answers product/shipping/returns questions using RAG over the store's own data
- Answers order status questions via **live, real-time Shopify API calls** (separate from the RAG pipeline — see §3.4)
- Escalates to a human via a defined handoff mechanism when it cannot answer confidently (see §3.5 for full specification)
- Charges a **transparent flat monthly fee with a clearly disclosed 2,000 message/month cap** — no hidden credits, no silent failures
- Is GDPR and EU AI Act compliant from day one

### 2.3 Target Users

**Primary (v1.0):** US/UK Shopify store owners with 100–10,000 SKUs and 50–500 support queries per day.

**Secondary:** Their end customers (shoppers) who interact with the widget.

**Deferred (v1.1):** WooCommerce store owners. *(Rationale: no primary research validates WooCommerce merchant demand; integration doubles build complexity and solo-founder support burden across diverse WordPress hosting environments. Add when Shopify PMF is confirmed.)*

### 2.4 Key Differentiators

| Claim | Status |
|-------|--------|
| Flat fee with clearly disclosed cap — no credits, no silent cutoffs | ✅ Buildable as specified |
| Native Shopify product catalog RAG (accurate product/FAQ answers) | ✅ Buildable as specified |
| Live order status lookup via real-time Shopify API | ✅ Buildable — separate subsystem from RAG (see §3.4) |
| Human handoff with defined email delivery mechanism | ✅ Fully specified in §3.5 |
| GDPR + EU AI Act compliant from day one | ✅ Fully specified in §5 |
| 53% cheaper than Chatbase at equivalent functionality | ✅ Verified pricing data |

> **Removed from v1.0 differentiators:** "Set up in 5 minutes" — no usability research validates this claim. Replace with "Set up today" or measure actual onboarding time during beta before using a specific time claim in copy.

> **Removed from v1.0 differentiators:** "Unlimited messaging" — ShopAssist AI uses a transparent 2,000 message/month cap. The differentiator vs Chatbase is not the absence of limits but the transparency and predictability of them. No silent cutoffs. No credit confusion.

---

## 3. Technical Specification

### 3.1 System Architecture

The system has **five layers**. Version 1.0 of this spec incorrectly merged order lookup into the RAG pipeline. These are distinct subsystems:

| Layer | Purpose | Technology |
|-------|---------|-----------|
| 1. Data ingestion | Sync product catalog + FAQ from Shopify | Shopify Admin REST API, nightly + delta |
| 2. Vector storage | Embed and index store content for semantic search | Supabase pgvector (HNSW) |
| 3. RAG inference | Answer product, FAQ, shipping, returns questions | GPT-4o-mini + pgvector retrieval |
| 4. Live order lookup | Answer real-time order status questions | Shopify Admin REST API, called at query time |
| 5. Widget delivery | Embed chatbot on merchant storefront | Shopify App Block (Online Store 2.0) |

**Routing logic:** On each incoming shopper query, a lightweight intent classifier determines whether the query is (a) product/FAQ/policy → route to RAG pipeline, or (b) order status → route to live order lookup. Ambiguous queries default to RAG with a fallback prompt to provide an order number if relevant.

### 3.2 Platform Integration — Shopify (v1.0)

**Authentication:** OAuth 2.0. The merchant installs ShopAssist AI via the Shopify App Store. The app requests read-only scopes only. Admin API keys are never stored client-side and never exposed in the widget.

**Required OAuth scopes (read-only):**
- `read_products` — product titles, descriptions, variants, pricing
- `read_shipping` — shipping policies
- `read_orders` — order status for live lookup (see §3.4)
- `read_content` — store pages (FAQ, About, Returns policy)

**Sync endpoints (for RAG ingestion):**
```
GET /admin/api/2024-01/products.json        — product catalog
GET /admin/api/2024-01/pages.json           — FAQ and policy pages
GET /admin/api/2024-01/shipping_zones.json  — shipping information
```

**Sync schedule:**
- Full catalog sync: nightly at 02:00 UTC
- Incremental delta: every 30 minutes via webhook (`products/update`, `products/create`, `products/delete`)
- Webhook registration: handled automatically at app install; re-registered on webhook failure detection

**Shopify App Store requirements (pre-submission checklist):**
- Partner account active with accepted Partner Agreement
- Privacy policy URL live before submission
- App passes Shopify's automated security scan
- OAuth flow tested end-to-end in a development store
- Expected review timeline: **2–6 weeks — build timeline must account for this**
- Fallback GTM path if review is delayed: direct website sign-up with manual Shopify token setup (documented for early beta users)

### 3.3 RAG Pipeline — Product, FAQ & Policy Questions

#### Chunking Strategy

| Content type | Chunking rule | Rationale |
|---|---|---|
| Product catalog | One embedding per product (title + description + all variants + price + availability) | Product is the atomic unit of retrieval |
| FAQ / policy pages | Chunk by heading (H2/H3 level) | Preserves semantic coherence of each policy section |
| Shipping zones | One embedding per zone + method | Keeps zone-specific rates together |

**Average product entry: ~500 tokens.** Embedding a 500-product catalog costs ~$0.005 (text-embedding-3-small at $0.02/M tokens) — effectively zero.

#### Vector Storage

Supabase pgvector with HNSW indexing. Sub-50ms retrieval confirmed in production deployments of 1.6M+ embeddings.

**Infrastructure requirement:** Supabase Pro ($25/month) is mandatory before serving any paying customer. The free tier has no uptime SLA.

#### Multi-Tenant Data Isolation

Each merchant's embeddings are stored with a `store_id` (UUID, assigned at OAuth install) as a non-nullable column. All pgvector queries are filtered by `store_id` at the query level — no cross-store retrieval is architecturally possible.

```sql
-- Schema for vector store (simplified)
CREATE TABLE store_documents (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  store_id    UUID NOT NULL REFERENCES merchants(id) ON DELETE CASCADE,
  content     TEXT NOT NULL,
  embedding   VECTOR(1536),
  doc_type    TEXT NOT NULL,  -- 'product' | 'faq' | 'policy' | 'shipping'
  source_id   TEXT,           -- Shopify resource ID
  updated_at  TIMESTAMPTZ DEFAULT now()
);

-- HNSW index on embeddings
CREATE INDEX ON store_documents USING hnsw (embedding vector_cosine_ops);

-- Row-Level Security (second isolation layer)
ALTER TABLE store_documents ENABLE ROW LEVEL SECURITY;

CREATE POLICY store_isolation ON store_documents
  USING (store_id = current_setting('app.current_store_id')::UUID);

-- ALL retrieval queries MUST include: WHERE store_id = $current_store_id
```

Row-Level Security (RLS) is enabled on `store_documents` as a second layer of isolation. A misconfigured query that omits `store_id` will return zero rows, not another store's data.

#### Inference Model

Primary model: GPT-4o-mini ($0.15/M input, $0.60/M output tokens).

> **Model cost warning:** Switching to GPT-5.4 Standard for the same volume would cost approximately $518/month at 500 conversations/day across the platform. Model selection is the single most important financial decision in this product. GPT-4o-mini handles FAQ-style e-commerce queries adequately at this stage.

#### Retrieval-to-Answer Flow

1. Shopper query arrives → intent classifier routes to RAG
2. Query is embedded using text-embedding-3-small
3. pgvector similarity search over `store_documents WHERE store_id = $store_id` returns top-3 chunks
4. **Similarity score evaluation:** pgvector returns cosine similarity (0.0–1.0). A score ≥ 0.72 on the top result triggers a confident answer. A score < 0.72 triggers escalation. **Important:** 0.72 is a calibration starting point — not a fixed value. The relationship between cosine similarity and answer quality must be measured on real e-commerce query sets before treating this threshold as correct. See §10 for the calibration milestone.
5. Top chunks + query are sent to GPT-4o-mini with a system prompt constraining answers strictly to retrieved context
6. Response is streamed to the shopper widget

### 3.4 Live Order Lookup — Order Status Questions

**Order status is not a RAG problem.** Order data changes in real time (processing → shipped → in transit → delivered). Embedding order records at nightly sync and retrieving them semantically would return stale data. This is a separate subsystem with a completely different implementation path.

#### Order Lookup Flow

1. Shopper query is classified as order-related by the intent router
2. Widget prompts the shopper: *"To check your order status, please enter your order number and the email address used at checkout."*
3. ShopAssist AI calls the Shopify Admin REST API in real time:
   ```
   GET /admin/api/2024-01/orders.json?name={order_number}&email={email}
   ```
4. The live response is returned directly to the shopper. **No order data is stored in the ShopAssist AI database** — it is fetched, displayed, and discarded.
5. If the Shopify API returns an error or no matching order: the chatbot informs the shopper and offers to escalate to a human (§3.5).

**Data minimisation (GDPR):** Order data is fetched transiently and never persisted. Only conversation log text (query + response, no order PII) is stored, subject to the 90-day retention limit.

**API rate limits:** Shopify REST API allows 40 requests/second (burst) and 2 requests/second (sustained) on the standard plan. Order lookups are one API call per query and will not approach rate limits at realistic usage volumes.

### 3.5 Human Handoff — Full Specification

Human escalation is triggered when:
- (a) RAG similarity score < 0.72 (low retrieval confidence), or
- (b) Live order lookup returns no result or an API error, or
- (c) The shopper explicitly requests a human agent ("talk to a person", "speak to support", etc.)

#### What the handoff delivers to the merchant

When escalation is triggered, ShopAssist AI:

1. **Informs the shopper:** *"I'm not able to fully answer this — I'm passing you to the store's support team. They'll follow up by email."*
2. **Prompts the shopper for their email** (if not already collected): *"Please share your email so the team can follow up with you."*
3. **Sends an email notification** to the merchant's registered support email address containing:
   - Shopper's email address
   - Full conversation transcript
   - The specific query that triggered escalation
   - Escalation reason (low confidence / order lookup failure / shopper request)
   - Timestamp and store identifier
4. **Logs the escalation** in the merchant dashboard under "Unresolved Conversations" — visible, searchable, exportable, and deletable per GDPR requirements
5. **Closes the widget interaction gracefully:** the widget displays a confirmation to the shopper that the team will follow up

#### What the merchant must configure (required during onboarding)

- Support email address (required — app cannot go live without this field completed)
- Optional: custom escalation message to show shoppers

#### What the handoff does NOT do in v1.0 (explicitly deferred)

- Live chat transfer (deferred to v1.1 — requires helpdesk integration)
- Ticket creation in Zendesk, Gorgias, or Freshdesk (deferred to v1.1)
- SMS notification (deferred)

These limitations are disclosed on the product page and during onboarding.

### 3.6 Widget Delivery

The chatbot is delivered as a **Shopify App Block** compatible with Online Store 2.0 themes. This is the correct Shopify-native embed mechanism and is required for 'Built for Shopify' certification.

**Fallback for non-OS2.0 themes:** A JavaScript embed snippet is provided as a secondary install option for merchants on legacy themes, documented in the onboarding flow with a clear preference for the App Block method.

**Widget configuration (merchant dashboard):**
- Brand colours (primary, text, background)
- Bot display name (default: "Support")
- Opening message
- Support email for escalation (required)
- Language (English only in v1.0)

### 3.7 Merchant Authentication & Session Management

#### Merchant dashboard authentication

- OAuth 2.0 via Shopify — merchants log in using their Shopify credentials. No separate password required.
- Session tokens: JWT, 24-hour expiry, refreshed on activity
- Dashboard is served at `app.shopassist.ai` (separate subdomain from marketing site)

#### Shopper session management

- Shopper sessions are anonymous by default — no login required
- Session ID: randomly generated UUID stored in a browser cookie (`SameSite=Strict; Secure; HttpOnly`; 30-day expiry)
- Conversation history within a session is retained for follow-up question context
- Sessions are not linked to Shopify customer accounts in v1.0
- On escalation, shopper provides their email — this is the only PII collected and is used solely for the merchant follow-up notification

### 3.8 Infrastructure Budget — Corrected Cost Model

**Critical correction from v1.0:** The previous spec stated "~$4 per 500 stores/day" for OpenAI API costs. This was wrong — the $4.05/month figure from the research applies to 500 conversations/day **total across the platform**, not per store. The tables below are recalculated on the correct per-store basis.

#### Per-store API cost (GPT-4o-mini)

| Messages/month per store | Input tokens | Output tokens | API cost/store/month |
|---|---|---|---|
| 500 (low-volume store) | 400,000 | 200,000 | ~$0.18 |
| 2,000 (cap threshold) | 1,600,000 | 800,000 | ~$0.72 |
| 5,000 (overage user) | 4,000,000 | 2,000,000 | ~$1.80 |

At the 2,000-message cap, per-store API cost is **~$0.72/month** — leaving $14.28 gross margin before platform overhead. A store would need to send ~20,000 messages/month before API cost alone reaches $15. The cap exists for edge-case protection and upsell triggers, not because the unit economics require it at normal volumes.

#### Platform-wide infrastructure costs

| Component | Monthly cost | Notes |
|---|---|---|
| Supabase Pro (pgvector + DB) | $25 | Mandatory — free tier has no SLA |
| Vercel Pro (Next.js frontend + API routes) | $20 | **Pro required** — Hobby plan prohibits commercial use per Vercel ToS |
| OpenAI Embeddings (text-embedding-3-small) | ~$0.01 | Near zero at any realistic catalog size |
| Paddle MoR fees | 5% + $0.50/txn | Handles all global VAT/tax/chargebacks |
| OpenAI API (GPT-4o-mini) | Scales with usage — see below | |

#### Profitability at scale (corrected)

| Stores | MRR | OpenAI API est. | Fixed infra | Paddle fees | **Net/month** |
|---|---|---|---|---|---|
| 50 | $750 | ~$9 | $45 | ~$60 | **~$636** |
| 100 | $1,500 | ~$18 | $45 | ~$125 | **~$1,312** |
| 200 | $3,000 | ~$36 | $45 | ~$250 | **~$2,669** |
| 500 | $7,500 | ~$90 | $70 | ~$625 | **~$6,715** |

*OpenAI costs assume average 500 messages/month per store. Fixed infra increases modestly at 500 stores (Supabase storage, Vercel bandwidth) — estimated $70/month. Recalculate with actual observed usage after 50-store milestone.*

### 3.9 SLA Commitments

- **Uptime:** 99.9% (guaranteed by Supabase Pro — ~8.7 hours downtime/year). Disclosed to merchants at signup.
- **Response latency — RAG queries:** 1–3 seconds per message (pgvector retrieval <50ms + GPT-4o-mini generation 500ms–2s). Do not promise sub-1s response times.
- **Response latency — order lookup queries:** Additional 200–500ms for the live Shopify API call. Total round trip: 1.5–4 seconds.
- **Sync lag:** Product catalog reflects changes within 30 minutes (delta webhook) or by the following morning (nightly sync). This lag must be disclosed so merchants understand the chatbot may not immediately reflect a product change.

### 3.10 Message Cap Policy

A per-store message cap is in place. This is **not** marketed as "unlimited messaging" — the cap is clearly disclosed on the pricing page, during onboarding, and in the merchant dashboard at all times.

| Plan | Messages/month | Overage |
|---|---|---|
| Starter ($15/month) | 2,000 included | $0.01 per message above cap |
| Free tier | 100 included | Upgrade required |
| AppSumo LTD (if pursued) | 500 hard cap | Paid add-on required — non-negotiable |

**Cap enforcement behaviour:**
- At 80% of monthly cap: automated email warning to merchant
- At 100%: chatbot displays to shoppers — *"Our support bot is temporarily unavailable. Please email [support@store.com] for help."* — showing the merchant's configured support email
- No silent failures. No blank responses. This is the direct fix to the primary Chatbase complaint.

### 3.11 Error States & Fallback Behaviour

| Error condition | Shopper-facing behaviour | System action |
|---|---|---|
| OpenAI API timeout (>5s) | "I'm having trouble answering right now. Please try again in a moment." | Log error; retry once after 2s; escalate to human if retry fails |
| OpenAI API 5xx error | "Something went wrong on our end. The store's support team has been notified." | Trigger escalation email to merchant; log to error dashboard |
| Shopify product sync failure | Chatbot continues from last successful sync; stale data badge shown in merchant dashboard | Email merchant: "Your product catalog hasn't synced in X hours" |
| Shopify order lookup API error | "I wasn't able to retrieve your order. Let me connect you with the support team." | Trigger full escalation flow (§3.5) |
| Shopify webhook failure | Fallback to polling on next scheduled sync | Log missed webhooks; alert merchant if >3 consecutive failures |
| pgvector returns zero results | "I don't have information about that in my knowledge base." + escalation offer | Log as unresolved query; surface in merchant dashboard |
| Message cap reached | Friendly cap-reached message with merchant's support email shown to shopper | See §3.10 |
| Widget fails to load on storefront | Widget silently does not render — no error shown to shopper | Log client-side JS error to monitoring service |
| Consent not given by shopper | Chatbot does not activate; consent prompt remains visible | No data collected, no API call made |

### 3.12 Rate Limiting & Abuse Prevention

- **Per-session rate limit:** Maximum 10 widget requests per shopper session per minute (prevents bot abuse of the widget endpoint)
- **IP-level rate limit:** Maximum 60 requests per IP per hour (handled at Vercel edge layer)
- **Message cap enforcement:** Checked at the application layer on every request, before any OpenAI or Shopify API call is made
- **Order lookup gating:** Live Shopify API calls for order status are gated behind the message cap check — a store at cap cannot trigger additional API calls

---

## 4. Business Model

### 4.1 Pricing

| Benchmark | Value |
|---|---|
| ShopAssist AI Starter | $15/mo flat (2,000 messages included, clearly disclosed) |
| ShopAssist AI Free | $0 (100 messages, 50 products, "Powered by ShopAssist AI" label) |
| Chatbase Hobby | $40/mo base (credit model, opaque usage) |
| Chatbase unbranded professional setup | $100+/mo ($40 base + $39 branding + $59 custom domain) |
| Chatty | $19.99/mo (interaction limits, no catalog RAG sync) |
| Average Shopify app cost | $66.54/mo (ShopAssist is 77% below average) |

**Pricing narrative:** Transparent cap vs. credit model. ShopAssist AI's 2,000 message/month cap is clearly disclosed and notified before it is hit — the exact opposite of Chatbase's silent credit exhaustion. "No confusing credits. No silent cutoffs. No surprise charges."

**Post-PMF consideration:** Usage-based pricing (flat $15 + $0.01 overage per message) delivers 10% higher NRR and 22% lower churn vs. pure flat-fee. Test a hybrid overage tier after reaching 100 stores and gathering real usage distribution data.

### 4.2 Unit Economics

| Metric | Value |
|---|---|
| LTV at 5% monthly churn | $300 |
| LTV at 3% monthly churn | $500 |
| Max sustainable CAC (3:1 LTV:CAC ratio) | $100 |

SMB monthly churn averages 3–5%. Plan for 5% as the base assumption. A 2-point churn improvement is worth $200 in LTV per customer — making onboarding quality and time-to-value the single highest-leverage retention action.

Primary churn drivers: poor onboarding (time-to-value > 7 days), feature gaps, and competitive switching. Target first-value moment within 7 days of signup.

### 4.3 Profitability Milestones (corrected — see §3.8)

| Stores | MRR | Net/month | Notes |
|---|---|---|---|
| 50 | $750 | ~$636 | Below ramen-profitable |
| 100 | $1,500 | ~$1,312 | **Ramen-profitable milestone** |
| 200 | $3,000 | ~$2,669 | Comfortable solo-founder runway |
| 500 | $7,500 | ~$6,715 | Exit-readiness consideration |

### 4.4 Entity & Payment Setup

| Option | Cost | Incorporation | Recommended? |
|---|---|---|---|
| Paddle (MoR) | 5% + $0.50/txn | Not required | **Yes — first choice** |
| Startbutton Africa | Varies | Not required | Yes — NGN settlement option |
| Stripe Atlas | $500 + ~$475/yr | Delaware LLC | Only if Stripe ecosystem required |

Paddle is the recommended starting point: no incorporation required, Merchant of Record handles all global VAT and chargebacks, Nigerian founders receive payouts via Payoneer or Grey.co. Stripe Atlas is not recommended at early stage — the $475+/year ongoing cost and mandatory IRS Form 5472 filing (non-filing penalty: $25,000) are significant overhead pre-revenue.

---

## 5. Compliance Specification

### 5.1 Regulatory Landscape

Chatbots are classified as **'limited risk'** under the EU AI Act. Non-compliance fines can reach €35M or 7% of global revenue — double the GDPR maximum. Compliance is a non-negotiable launch requirement, not a v2 feature.

### 5.2 Compliance Checklist

| Requirement | Regulation | Action | Required by |
|---|---|---|---|
| Disclose AI identity | EU AI Act | 'Powered by AI' label permanently visible in widget chrome | Launch |
| Explicit consent before data collection | GDPR Art. 6 | Consent checkbox before first widget message — cannot proceed without it | Launch |
| Sign DPA with OpenAI | GDPR Art. 28 | Use API/Business tier only — free ChatGPT tier is non-compliant | Before any EU merchant goes live |
| Conduct DPIA | GDPR Art. 35 | Required before EU merchant onboarding | Before EU launch |
| Data retention limits | GDPR | Auto-delete conversation logs after 90 days (configurable; minimum 30 days) | Launch |
| Data deletion mechanism | GDPR | Merchant dashboard: "Delete all my store data" — deletes embeddings, logs, account | Launch |
| Data minimisation | GDPR | Product + FAQ data only in embeddings; no PII; order data fetched transiently, never stored | By architecture (§3.4) |
| CCPA opt-out | CCPA | Privacy page with 'Do not sell my data' link for California users | Launch |
| Shopper email collected only on escalation | GDPR | Email collected at escalation with explicit purpose statement | By design (§3.5) |

### 5.3 DPIA Requirement

A Data Protection Impact Assessment (DPIA) is mandatory under GDPR Article 35 for AI chatbots likely to create high risk to data subjects. This must be completed and documented **before onboarding any EU merchant.** A DPIA template is a required launch artifact (see §9).

### 5.4 OpenAI DPA

OpenAI's Data Processing Agreement for GDPR compliance is only available on business/API accounts. Building on the OpenAI API (as specified) is compliant. Using the free ChatGPT tier for a commercial product is not compliant. Verify DPA is signed before any EU merchant goes live.

### 5.5 Data Residency Note

Supabase Pro supports region selection (e.g., EU-West for EU merchant data). If specifically targeting EU merchants, confirm whether a dedicated EU-region Supabase instance is required or whether Supabase's standard Standard Contractual Clauses (SCCs) in their DPA are sufficient. Verify with a GDPR advisor before EU launch. [UNVERIFIED — recommend independent legal check]

---

## 6. Competitive Positioning

### 6.1 Feature Comparison Matrix

| Feature | ShopAssist AI | Chatbase | Chatty | SiteGPT |
|---|---|---|---|---|
| Price/month | $15 flat | $40+ base | $19.99 | ~$39 |
| Shopify product catalog RAG | Yes | No (upload-only) | No | No |
| Live order status lookup | Yes (real-time API) | No | No | No |
| Human handoff | Yes (email escalation) | No | No | No |
| Usage model | Transparent cap, notified before hit | Credits exhaust silently | Interaction limits | Credits |
| Branding removal | Included | $39/month add-on | Included | Paid |
| GDPR / EU AI Act ready | Yes | Partial | Unknown | Unknown |
| WooCommerce support | v1.1 | No | No | No |
| Trustpilot score | N/A (new) | 2.1/5 | 4.9/5 | Not listed |

### 6.2 Positioning Statement

ShopAssist AI is the only flat-fee AI chatbot with native Shopify product catalog sync and live order status lookup at under $20/month. No confusing credits. No silent cutoffs. No surprise charges. 53% cheaper than Chatbase — and it actually knows your store and your orders.

### 6.3 Chatbase Switch Triggers

Primary switch triggers from 400+ verified reviews:

- Chatbot went silent — credits ran out without warning
- GPT-4 model costs 20x more credits, making advanced features unaffordable
- No Shopify order lookup — couldn't answer "where is my order?"
- No human handoff — shoppers left conversations unresolved
- Removing Chatbase branding costs an extra $39/month

### 6.4 Shopify App Store Strategy

15,508 apps on the Shopify App Store as of November 2025. The 'Built for Shopify' badge is held by only 1,083 apps and is the definitive quality signal for high-growth merchants.

**'Built for Shopify' certification is a structured medium-term goal, not a checkbox.** It requires specific UX standards, app performance benchmarks, and a formal Shopify review process.

**Certification plan:**
- Months 1–3: Launch with App Block delivery; pass initial App Store review; begin review collection
- Months 3–6: Audit app against current 'Built for Shopify' requirements; close UX and performance gaps
- Month 6+: Submit for certification — target at 100-store milestone

**Free tier definition (required before App Store submission):**

45.71% of Shopify apps offer a free plan, which measurably accelerates App Store discovery.

| Free tier | Limit |
|---|---|
| Messages/month | 100 |
| Products synced | 50 |
| Duration | Permanent (not a time-limited trial) |
| Human handoff | Included |
| Branding | "Powered by ShopAssist AI" label — cannot be removed on free tier |

*Per-store API cost on the free tier is < $0.05/month — sustainable at any scale.*

---

## 7. Distribution & Go-to-Market

### 7.1 Channel Prioritization

| Channel | Avg CAC | Viable? | Notes |
|---|---|---|---|
| SEO / Content | ~$53 | ✅ Yes | 3.3x better unit economics than paid social |
| Email marketing | ~$53 | ✅ Yes | Within $100 CAC ceiling |
| Shopify App Store | Low / organic | ✅ Yes | Review velocity critical; free tier accelerates installs |
| Community (Reddit, FB Groups) | Near zero | ✅ Yes | Founder presence required; ROI messaging, not AI novelty |
| Meta / Google Ads | ~$937 | ❌ No | Structurally unviable — nearly 10x the $100 CAC ceiling |

Paid acquisition via Meta or Google Ads is structurally unviable: LTV at 5% churn is $300; 3:1 LTV:CAC allows a maximum $100 CAC. Social ads average $937 CAC. All early acquisition must be organic.

### 7.2 SEO Keyword Strategy

**Low-competition, high-intent targets for launch (build content immediately):**
- "Chatbase alternative" — captures dissatisfied competitor users at decision stage
- "Chatbase for Shopify" — intent-rich, moderate competition
- "Cheap Shopify chatbot" — price-sensitive buyers
- "AI support chatbot $15" — bottom-of-funnel price searchers
- "WooCommerce chatbot RAG" — underserved, build now, monetise when WooCommerce launches in v1.1

**High-competition terms to build toward over 6–12 months:**
- "Best AI chatbot for Shopify" (Shopify.com ranks #1)
- "AI chatbot for ecommerce"

**AI search as acquisition channel:** Traffic from generative AI sources to US retail sites grew 4,700% YoY by mid-2025. Publishing structured, factual content optimised for AI citation is a distinct acquisition channel alongside traditional SEO.

### 7.3 Community Targeting

Key merchant communities: r/shopify (1M+ members), r/dropshipping, Facebook Groups (eCommerce Entrepreneurs, Shopify Entrepreneurs), Shopify Community forums, IndieHackers.

The AI chatbot early majority has crossed — messaging should focus on ROI and simplicity, not AI novelty. Suggested hook: "53% cheaper than Chatbase, and it actually looks up orders."

**Consumer trust note from research:** 60% of consumers worry AI chatbots cannot understand their queries; 46% prefer live support even when the bot is faster. Community messaging should lead with human handoff as a feature — not bury it.

### 7.4 AppSumo Assessment

AppSumo takes 70% of revenue. On a $59 LTD, the creator receives ~$17.70 while bearing recurring API costs indefinitely. Structurally loss-making without strict message caps.

> **Decision:** Treat AppSumo as a marketing and validation expense, not a revenue strategy. If pursued: enforce a **hard 500 messages/month cap** on LTD tiers with a paid overage add-on. Non-negotiable. Do not pursue AppSumo before reaching 50 paying App Store subscribers — validate the product first.

### 7.5 Founder-Specific Risk Mitigations

Trust is built through product quality, App Store reviews, transparent pricing, and public founder presence — not geographic origin.

- **Payment:** Paddle (no incorporation) or Startbutton Africa (NGN settlement) — Stripe Atlas not required
- **VAT/tax:** Fully handled by Paddle as Merchant of Record
- **USD receipt:** Payoneer or Grey.co for Paddle payouts
- **Trust:** Shopify App Store listing + 'Built for Shopify' certification as credibility anchors
- **Personal brand:** r/shopify and Indie Hackers founder presence as pre-launch distribution surface

---

## 8. Merchant Onboarding Flow

The spec targets "time-to-value < 7 days." The onboarding flow is the primary lever for hitting this target.

**Target: Chatbot live on storefront within 30 minutes of install for a standard Shopify store.**

| Step | Action | Owner | Blocks go-live? |
|---|---|---|---|
| 1 | Install ShopAssist AI from Shopify App Store | Merchant | Yes |
| 2 | OAuth: grant read-only scopes | Merchant | Yes |
| 3 | Enter support email address for escalations | Merchant | Yes — required for escalation flow |
| 4 | Automatic: first product catalog sync triggered (runs in background) | System | No — chatbot goes live immediately; sync completes within minutes |
| 5 | Customise widget (name, colour, opening message) | Merchant | No — defaults are usable |
| 6 | Add App Block to storefront theme (or use JS snippet fallback) | Merchant | Yes — widget not visible until added |
| 7 | Send a test message in the widget preview | Merchant | No — but strongly encouraged |
| 8 | Chatbot is live | — | — |

**First-value milestone:** Chatbot answers at least one product question correctly on the live storefront. Demonstrable within 30 minutes of install for stores with < 500 products.

**Activation email sequence (automated):**
- Email 1 (immediately): Installation confirmation + step-by-step setup guide
- Email 2 (day 2, if widget not yet added to storefront): Gentle reminder with App Block instructions
- Email 3 (day 5, if no queries recorded): "Is your chatbot working?" — link to dashboard
- Email 4 (day 7): First-week summary — queries answered, escalations, containment rate

---

## 9. Launch Artifacts Checklist

### 9.1 Pre-Build

- [ ] **5–10 primary user research interviews** with active Shopify store owners (validate WTP and pain before committing to build)
- [ ] Unit cost model per store at 100 / 500 / 1,000 stores — using corrected per-store cost model (§3.8)
- [ ] Message cap policy finalised (2,000 included, $0.01 overage — confirmed)
- [ ] Free tier structure finalised (100 messages / 50 products — confirmed)
- [ ] Payment processor selected (Paddle recommended)
- [ ] OpenAI API business account with DPA signed
- [ ] Supabase Pro account (not free tier) — correct region selected
- [ ] Vercel Pro account (not Hobby — commercial use required per Vercel ToS)
- [ ] Vendor ToS compliance register complete

### 9.2 Build

- [ ] Shopify Admin API OAuth integration — read-only scopes (§3.2)
- [ ] Shopify App Block widget (Online Store 2.0 compatible) + JS snippet fallback
- [ ] Product catalog nightly sync + incremental delta webhook
- [ ] Supabase pgvector with HNSW index + `store_id` RLS isolation (§3.3)
- [ ] Intent router — product/FAQ → RAG; order status → live API (§3.1)
- [ ] GPT-4o-mini inference with cosine similarity scoring
- [ ] Live order lookup subsystem — real-time Shopify API, no persistence (§3.4)
- [ ] Human escalation flow with email notification to merchant (§3.5)
- [ ] "Unresolved Conversations" view in merchant dashboard
- [ ] EU AI Act disclosure label permanently visible in widget chrome
- [ ] Consent checkbox before first widget message (GDPR)
- [ ] Conversation auto-delete at 90 days
- [ ] Merchant data deletion dashboard ("Delete all my data")
- [ ] Message cap enforcement + 80% warning email + cap-reached shopper message (§3.10)
- [ ] All error states implemented (§3.11)
- [ ] Rate limiting at session and IP level (§3.12)
- [ ] Merchant auth via Shopify OAuth → JWT session (§3.7)
- [ ] Escalation metric logging (for containment rate KPI)
- [ ] Exit-readiness metrics capture from day one — MRR, churn events, containment rate, active stores (§10)
- [ ] Automated onboarding email sequence (§8)

### 9.3 Pre-Submission (App Store)

- [ ] Privacy policy live at permanent public URL
- [ ] Shopify Partner Agreement accepted
- [ ] App passes Shopify automated security scan
- [ ] OAuth flow tested end-to-end in development store
- [ ] Widget tested on ≥3 Online Store 2.0 themes and ≥1 legacy theme
- [ ] DPIA template completed for EU merchants
- [ ] Fallback GTM path documented — direct signup URL for beta users during App Store review period

### 9.4 Launch

- [ ] Shopify App Store listing with keyword-optimised title and description
- [ ] Free tier live and visible on pricing page
- [ ] Pricing page: transparent cap narrative — **no "unlimited messaging" language**
- [ ] 'Chatbase alternative' SEO landing page live
- [ ] Founder presence established on r/shopify and Shopify Community forums
- [ ] Onboarding email sequence live and tested

### 9.5 Post-Launch Milestones

- [ ] **50 stores:** Measure containment rate; calibrate escalation threshold from 0.72 baseline against real query data; validate per-store API cost vs. model predictions
- [ ] **100 stores:** Ramen-profitable — begin formal 'Built for Shopify' certification audit; pilot usage-based overage tier with consenting cohort
- [ ] **200 stores:** Review pricing model based on real usage distribution; assess WooCommerce v1.1 based on volume of merchant requests
- [ ] **500 stores:** Exit-readiness documentation for Acquire.com — all required metrics already instrumented from day one

---

## 10. Key Metrics & Success Criteria

| Metric | Target | Benchmark source | Notes |
|---|---|---|---|
| Containment rate | >86% | Tidio / industry benchmark, 2026 | Calibrate escalation threshold at 50-store milestone |
| Monthly churn | <5% (target <3%) | RockingWeb SaaS Benchmark, 2025 | — |
| LTV | $300 at 5% / $500 at 3% churn | Gatilab SaaS Metrics, 2026 | — |
| CAC ceiling | $100 (3:1 LTV:CAC) | Optifai Pipeline Study, 2026 | — |
| Time-to-first-chatbot | <30 minutes from install | Internal target | Measure during beta |
| First-value within 7 days | >80% of new signups | WeAreFounders SaaS benchmarks, 2026 | — |
| MRR growth (early stage) | 10–20% month-on-month | B2B SaaS benchmarks, RockingWeb, 2025 | — |
| Uptime SLA | 99.9% | Supabase Pro guarantee | — |
| RAG response latency | 1–3 seconds | pgvector + GPT-4o-mini benchmarks | — |
| Order lookup latency | 1.5–4 seconds | Shopify API + model benchmarks | — |
| App Store rating | >4.7/5 | Shopify App Store ranking driver | Aspirational — track from first review; no external baseline for new apps |
| Escalation threshold accuracy | Calibrated at 50-store milestone | Internal — no external benchmark | Adjust from 0.72 based on false escalation and missed escalation rates |

### Exit-readiness metrics — instrumented from day one

Acquirers on Acquire.com require these specific data points. The data infrastructure to capture them must be built from the first paying customer — not retrofitted at 500 stores.

| Metric | How to capture |
|---|---|
| MRR (monthly, documented) | Paddle dashboard export — automated monthly snapshot |
| Monthly churn rate | Calculated from Paddle subscription cancellation events |
| Containment rate | Log every escalation trigger; divide by total queries per period |
| Active customer base | Stores with ≥1 widget query in the last 30 days |
| No key-person dependency documentation | Written runbook: all vendor credentials, API keys, deploy process, and support procedures |

---

## 11. Appendix — Deferred Scope (v1.1)

The following features are explicitly out of scope for v1.0. They must not be built until Shopify PMF is confirmed.

| Feature | Rationale for deferral |
|---|---|
| WooCommerce integration | No primary research validates demand; doubles build + support complexity; arbitrary WordPress hosting creates disproportionate solo-founder support burden. Add when merchants request it post-Shopify PMF. |
| Helpdesk integrations (Zendesk, Gorgias, Freshdesk) | Requires third-party API integrations; deferred until email escalation (v1.0) is validated and merchants request deeper integration |
| Live chat transfer (real-time handoff) | Requires live chat infrastructure; email escalation is sufficient for v1.0 |
| Multi-language widget | English-only for v1.0; add on merchant request |
| SMS escalation notifications | Lower priority vs. email; add if merchants request |
| Shopper account linking (Shopify customer accounts) | Privacy and technical complexity; not required for core use case |
| A/B testing framework for widget copy | Nice-to-have; not a launch requirement |
| Tidio / Gorgias / Richpanel competitive analysis | Dominant Shopify support players not covered in research; conduct before v1.1 positioning work |

---

*Version 2.0 — April 2026. Revised from v1.0 following full multi-dimensional audit. All research cited throughout is publicly available. This specification is confidential and intended for internal planning use only.*
