# El Camino Secreto - Engine 1: Quien Paga
## Technical Specification v2.0

## Product Overview
**Product Name:** El Camino Secreto - Engine 1 (Quien Paga / Who Pays)
**Version:** 2.0
**Category:** Immigration Financial Pathway Engine
**Priority:** Tier 1 - Flagship Product
**Language Support:** English / Spanish (Bilingual)

## Executive Summary
"Quien Paga" (Who Pays) is the first engine in the El Camino Secreto (The Secret Path) immigration platform suite. It solves the #1 barrier for immigrants: understanding and navigating the financial costs of immigration processes. The engine maps every fee, identifies fee waivers, finds financial assistance programs, and creates personalized payment roadmaps.

## Problem Statement
- Average immigration case costs $5,000-$15,000+ in legal fees and filing costs
- 73% of immigrants don't know about fee waiver eligibility
- Financial stress causes 40% of cases to be abandoned before completion
- Predatory "notarios" charge 3-5x fair rates for simple filing assistance

## Core Features

### 1. Cost Calculator Engine
- **Case Type Analyzer:** Input case type → get complete cost breakdown
  - Family-based (IR1, CR1, F1-F4)
  - Employment-based (EB1-EB5, H1B, L1, O1)
  - Humanitarian (Asylum, TPS, VAWA, U-Visa, T-Visa)
  - Naturalization (N-400)
  - DACA renewals
- **Fee Database:** Real-time USCIS fee schedule integration
- **Attorney Cost Estimator:** Fair market rate ranges by case type and region
- **Hidden Cost Alerts:** Medical exams, translations, travel, biometrics, photos

### 2. Fee Waiver Navigator
- **Eligibility Screener:** Income-based (I-912) qualification check
- **Means-Tested Benefits Check:** Cross-reference with Medicaid, SNAP, SSI
- **Hardship Analysis:** AI-guided hardship statement generator
- **Form Auto-Fill:** Pre-populate I-912 based on user financial data
- **Approval Predictor:** ML model trained on historical fee waiver approval rates

### 3. Financial Assistance Finder
- **National Database:** 2,500+ organizations providing immigration financial aid
- **State/Local Programs:** Filtered by user's state and county
- **Faith-Based Resources:** Churches, temples, mosques with immigrant aid programs
- **Pro Bono Attorney Matching:** Connect with free legal services
- **Scholarship/Grant Finder:** Immigration-specific financial aid

### 4. Payment Roadmap Generator
- **Timeline Mapping:** Visual timeline of when each payment is due
- **Savings Calculator:** Monthly savings targets to meet deadlines
- **Payment Plan Options:** Attorneys offering installment plans
- **Priority Ranking:** Which fees to pay first for optimal case progression
- **Alert System:** Reminders before payment deadlines

## Technical Architecture

### System Design
```
┌─────────────────────────────────────────────┐
│          Quien Paga User Interface           │
│    (React Native - iOS/Android + Web)        │
├─────────────────────────────────────────────┤
│              API Gateway (Express)           │
├──────────┬──────────┬───────────────────────┤
│ Cost     │ Waiver   │ Assistance            │
│ Engine   │ Navigator│ Finder                │
│          │          │                       │
├──────────┴──────────┴───────────────────────┤
│        Claude AI (Bilingual NLP)            │
├──────────┬──────────┬───────────────────────┤
│ Postgres │ Redis    │ USCIS Fee             │
│ (Users)  │ (Cache)  │ API (Scraper)         │
└──────────┴──────────┴───────────────────────┘
```

### Database Schema
```sql
CREATE TABLE cases (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    case_type VARCHAR(50) NOT NULL,
    sub_type VARCHAR(100),
    beneficiary_count INT DEFAULT 1,
    state VARCHAR(2),
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE cost_estimates (
    id UUID PRIMARY KEY,
    case_id UUID REFERENCES cases(id),
    uscis_fees JSONB NOT NULL,
    legal_fees JSONB,
    ancillary_costs JSONB,
    total_estimate DECIMAL(10,2),
    fee_waiver_eligible BOOLEAN DEFAULT FALSE,
    potential_savings DECIMAL(10,2),
    generated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE assistance_programs (
    id UUID PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    organization VARCHAR(255),
    type VARCHAR(50),
    coverage_area JSONB,
    eligibility_criteria JSONB,
    max_assistance DECIMAL(10,2),
    contact_info JSONB,
    verified_at TIMESTAMP,
    active BOOLEAN DEFAULT TRUE
);

CREATE TABLE payment_roadmaps (
    id UUID PRIMARY KEY,
    case_id UUID REFERENCES cases(id),
    milestones JSONB NOT NULL,
    monthly_savings_target DECIMAL(10,2),
    total_timeline_months INT,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### API Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/cases` | Create new immigration case |
| POST | `/api/calculate` | Calculate total costs for case |
| GET | `/api/fees/current` | Get current USCIS fee schedule |
| POST | `/api/waiver/check` | Check fee waiver eligibility |
| POST | `/api/waiver/generate` | Generate I-912 fee waiver form |
| GET | `/api/assistance/:state` | Find assistance programs by state |
| POST | `/api/roadmap` | Generate payment roadmap |
| GET | `/api/attorneys/:zip` | Find pro bono attorneys nearby |

## AI Features
- **Bilingual Chatbot:** Answer questions about costs in English or Spanish
- **Document Scanner:** Upload documents to auto-extract financial information
- **Hardship Statement Writer:** AI-assisted narrative for fee waiver applications
- **Cost Comparison:** Compare costs across different case strategies
- **Scam Detection:** Flag suspicious fee quotes from attorneys/notarios

## Data Sources
- USCIS Fee Schedule (updated within 24 hours of changes)
- DOJ EOIR fee schedule
- State bar association attorney fee surveys
- IRS poverty guidelines (for fee waiver eligibility)
- National immigration legal services directory
- 211.org social services database

## Security & Compliance
- HIPAA-adjacent data handling (immigration status is sensitive)
- End-to-end encryption for all user data
- No data sharing with government agencies
- Attorney-client privilege protections where applicable
- SOC 2 Type II certification target
- Data residency options for user privacy

## Monetization
- **Free:** Basic cost calculator + fee waiver eligibility check
- **Premium ($9.99/mo):** Full roadmap, assistance finder, payment alerts
- **Family Plan ($19.99/mo):** Up to 5 family members, shared dashboard
- **Attorney Portal ($99/mo):** Bulk client management, branded reports

## Success Metrics
- Fee waiver applications generated per month
- Financial assistance connections made
- Average user savings identified
- Case abandonment rate reduction
- User satisfaction (NPS score)
