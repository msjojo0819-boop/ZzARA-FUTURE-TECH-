# ZzARA Golden Ticket - Engine 2: Ya Estoy Aqui
## Technical Specification v2.0

## Product Overview
**Product Name:** Golden Ticket - Engine 2 (Ya Estoy Aqui / I'm Already Here)
**Version:** 2.0
**Category:** Immigration Status Verification & Pathway Optimization Engine
**Priority:** Tier 1 - Flagship Product
**Language Support:** English / Spanish (Bilingual)

## Executive Summary
"Ya Estoy Aqui" (I'm Already Here) is the second engine in the El Camino Secreto suite. It serves immigrants who are already in the United States and need to understand, verify, or change their immigration status. The engine maps every possible legal pathway from current status to desired status, identifies the fastest and most cost-effective routes, and monitors for policy changes that open new opportunities.

## Problem Statement
- 11M+ undocumented immigrants don't know if they qualify for any legal pathway
- Status transitions are complex with 50+ possible case types and subtypes
- Policy changes (executive orders, new regulations) create windows that close quickly
- Most immigrants only discover options when it's too late
- "Adjustment of Status" vs "Consular Processing" decision trees are poorly understood

## Core Features

### 1. Status Mapper
- **Current Status Identifier:** Interview-style questionnaire to determine exact current status
  - Lawful Permanent Resident (LPR)
  - Conditional Resident
  - Non-immigrant visa holder (H1B, F1, B1/B2, etc.)
  - TPS holder
  - DACA recipient
  - Asylum pending
  - Undocumented
  - Overstay
- **Status History Builder:** Map complete immigration history timeline
- **Derivative Status Tracker:** Identify family member derivative eligibility

### 2. Pathway Finder Engine
- **Decision Tree AI:** Map ALL possible pathways from current → desired status
- **Pathway Comparison:** Side-by-side comparison of timeline, cost, risk, requirements
- **Eligibility Screener:** Real-time eligibility check against current law
- **Priority Date Tracker:** Visa bulletin monitoring with wait time estimates
- **Pathway Risk Score:** ML-predicted risk assessment for each option
- **"Hidden" Pathway Detection:** Identify lesser-known options (e.g., registry, private bills)

### 3. Policy Monitor
- **Federal Register Scanner:** AI monitors Federal Register for immigration rule changes
- **Executive Order Tracker:** Real-time alerts on presidential immigration actions
- **Court Decision Monitor:** Track federal court decisions affecting immigration
- **USCIS Policy Manual Updates:** Detect and interpret policy manual changes
- **State-Level Tracking:** Monitor state laws affecting immigrants (driver's licenses, in-state tuition)

### 4. Opportunity Alerts
- **Window Detector:** Alert when a policy change opens a new pathway for user's profile
- **Deadline Alerts:** Filing deadlines, visa bulletin movements, TPS re-registration
- **Priority Date Alerts:** Notify when priority date becomes current
- **Program Announcements:** New programs, parole initiatives, special registrations
- **Closing Window Warnings:** Alert when a pathway is about to close

### 5. Document Readiness System
- **Document Checklist Generator:** Auto-generate required documents list per pathway
- **Document Validity Checker:** Verify documents aren't expired/will expire during process
- **Translation Tracker:** Identify documents needing certified translation
- **Evidence Builder:** Help gather supporting evidence for each pathway requirement
- **I-94 Analyzer:** Automatically parse and verify I-94 records

## Technical Architecture

### System Design
```
┌─────────────────────────────────────────────┐
│       Ya Estoy Aqui User Interface          │
│  (React Native + Progressive Web App)        │
├─────────────────────────────────────────────┤
│              API Gateway                     │
├──────────┬──────────┬───────────────────────┤
│ Status   │ Pathway  │ Policy               │
│ Mapper   │ Finder   │ Monitor              │
├──────────┼──────────┼───────────────────────┤
│ Document │ Alert    │ Visa Bulletin        │
│ System   │ Engine   │ Tracker              │
├──────────┴──────────┴───────────────────────┤
│         Claude AI (Decision Engine)          │
├──────────┬──────────┬───────────────────────┤
│ Postgres │ Redis    │ ElasticSearch         │
│ (Core)   │ (Cache)  │ (Policy Search)       │
└──────────┴──────────┴───────────────────────┘
```

### Pathway Decision Engine
```
User Profile → Status Classification →
  → INA Eligibility Matrix (250+ pathways) →
  → Filter by: bars, grounds of inadmissibility, waivers available →
  → Rank by: speed, cost, risk, approval probability →
  → Generate Pathway Report with action items
```

### Database Schema
```sql
CREATE TABLE user_profiles (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    current_status VARCHAR(50),
    status_history JSONB,
    entry_date DATE,
    entry_type VARCHAR(50),
    country_of_birth VARCHAR(100),
    country_of_nationality VARCHAR(100),
    family_relationships JSONB,
    criminal_history JSONB,
    employment_history JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE pathways (
    id UUID PRIMARY KEY,
    profile_id UUID REFERENCES user_profiles(id),
    from_status VARCHAR(50),
    to_status VARCHAR(50),
    pathway_type VARCHAR(100),
    requirements JSONB,
    timeline_estimate JSONB,
    cost_estimate JSONB,
    risk_score FLOAT,
    approval_probability FLOAT,
    steps JSONB,
    active BOOLEAN DEFAULT TRUE
);

CREATE TABLE policy_changes (
    id UUID PRIMARY KEY,
    source VARCHAR(100),
    title TEXT,
    summary TEXT,
    full_text TEXT,
    affected_statuses JSONB,
    affected_pathways JSONB,
    effective_date DATE,
    expiration_date DATE,
    impact_level VARCHAR(20),
    detected_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE alerts (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    alert_type VARCHAR(50),
    title VARCHAR(255),
    message TEXT,
    related_pathway_id UUID REFERENCES pathways(id),
    related_policy_id UUID REFERENCES policy_changes(id),
    priority VARCHAR(20),
    read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE visa_bulletin (
    id UUID PRIMARY KEY,
    month DATE,
    category VARCHAR(50),
    country VARCHAR(100),
    final_action_date DATE,
    dates_filing DATE,
    movement VARCHAR(20),
    fetched_at TIMESTAMP DEFAULT NOW()
);
```

### API Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/profile` | Create/update user immigration profile |
| GET | `/api/profile/:id/status` | Get current status assessment |
| POST | `/api/pathways/find` | Find all eligible pathways |
| GET | `/api/pathways/:id/compare` | Compare pathway options |
| GET | `/api/visa-bulletin/current` | Get current visa bulletin |
| GET | `/api/policy/changes` | Get recent policy changes |
| GET | `/api/alerts/:userId` | Get user alerts |
| POST | `/api/documents/checklist` | Generate document checklist |
| GET | `/api/documents/:id/status` | Check document readiness |

## Data Sources
- USCIS Case Status API
- DOS Visa Bulletin (monthly)
- Federal Register API
- EOIR case data (FOIA)
- CBP I-94 records
- State Department visa statistics
- Immigration court calendars

## Security & Privacy
- Zero-knowledge architecture for status data
- End-to-end encryption (AES-256)
- No data sharing with any government entity
- Option for anonymous/pseudonymous profiles
- Auto-delete data after configurable period
- VPN/Tor compatible for access safety
- GDPR and state privacy law compliant

## Monetization
- **Free:** Status assessment + 1 pathway identification
- **Explorer ($14.99/mo):** All pathways + policy alerts + visa bulletin tracking
- **Family ($29.99/mo):** Up to 6 family members + derivative tracking
- **Attorney ($149/mo):** Unlimited clients, API access, bulk pathway analysis

## Success Metrics
- Pathways identified per user
- Policy alert → action conversion rate
- Pathway completion rate (started → filed)
- New pathway discovery rate (from policy changes)
- User retention (monthly active users)
