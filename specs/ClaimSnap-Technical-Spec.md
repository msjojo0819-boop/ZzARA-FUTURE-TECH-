# ClaimSnap - Technical Specification

## Product Overview
**Product Name:** ClaimSnap
**Version:** 1.0
**Category:** Insurance Claim Photo Analysis AI
**Priority:** Tier 3 - Vertical AI Solution

## Executive Summary
ClaimSnap is an AI-powered mobile app that helps insurance adjusters, policyholders, and contractors document damage accurately using computer vision. Users photograph damage, and the AI automatically estimates repair costs, identifies damage types, generates professional reports, and flags potential fraud indicators.

## Core Features

### 1. Damage Detection Engine
- **Multi-Category Recognition:** Auto-identify damage types:
  - Vehicle: dents, scratches, broken glass, frame damage, paint damage
  - Property: water damage, fire damage, wind damage, hail, mold, structural cracks
  - Roof: missing shingles, storm damage, age deterioration, flashing issues
  - Interior: flooring damage, wall damage, ceiling damage, appliance damage
- **Severity Scoring:** Rate damage severity on 1-10 scale with confidence interval
- **Multi-Angle Fusion:** Combine multiple photos of same damage for comprehensive assessment
- **Before/After Comparison:** AI overlay for comparing pre-loss and post-loss images

### 2. Cost Estimation Engine
- **Xactimate Integration:** Map damage to Xactimate line items and pricing
- **Regional Pricing:** Adjust estimates based on local labor and material costs
- **Material Identification:** Recognize materials (roofing type, flooring, siding) for accurate pricing
- **Scope of Work Generator:** Auto-generate detailed scope of repair work
- **Supplement Detection:** Identify additional damage that may require supplemental claims

### 3. Report Generator
- **Adjuster Reports:** Professional damage assessment reports with photos and estimates
- **Policyholder Summaries:** Easy-to-understand damage summaries for customers
- **Contractor Proposals:** Generate repair proposals from damage assessments
- **Export Formats:** PDF, Xactimate (.ESX), Excel, industry-standard formats
- **Photo Enhancement:** Auto-enhance and annotate damage photos for clarity

### 4. Fraud Detection
- **Photo Authenticity:** Detect manipulated/photoshopped damage images
- **Metadata Analysis:** Verify photo location, timestamp, device consistency
- **Damage Consistency Check:** Flag damage patterns inconsistent with claimed cause
- **Historical Cross-Reference:** Compare against database of known fraud patterns
- **Staged Damage Indicators:** Identify patterns consistent with intentionally staged damage

## Technical Architecture

### System Design
```
┌─────────────────────────────────────────┐
│       ClaimSnap Mobile App              │
│    (React Native - iOS + Android)       │
├─────────────────────────────────────────┤
│         API Gateway (Express)            │
├──────────┬──────────┬───────────────────┤
│ Vision   │ Cost     │ Report            │
│ Engine   │ Estimator│ Generator         │
│ (PyTorch)│ (Node)   │ (Claude AI)       │
├──────────┼──────────┼───────────────────┤
│ Fraud    │ Photo    │ Xactimate         │
│ Detector │ Storage  │ Integration       │
├──────────┴──────────┴───────────────────┤
│         ML Pipeline (GPU Cluster)        │
├──────────┬──────────┬───────────────────┤
│ Postgres │ S3       │ Redis             │
│ (Core)   │ (Photos) │ (Cache)           │
└──────────┴──────────┴───────────────────┘
```

### ML Models
| Model | Purpose | Architecture | Accuracy |
|-------|---------|-------------|----------|
| DamageNet-v3 | Damage detection/classification | ResNet-152 + FPN | 94.2% |
| SeverityScore-v2 | Damage severity estimation | EfficientNet-B4 | 91.8% |
| MaterialID-v1 | Building material recognition | MobileNet-v3 | 89.5% |
| FraudFlag-v2 | Photo manipulation detection | Custom CNN + metadata | 96.1% |
| CostPredict-v1 | Repair cost estimation | XGBoost + DamageNet features | ±12% |

### Database Schema
```sql
CREATE TABLE claims (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    policy_number VARCHAR(50),
    claim_type VARCHAR(50),
    damage_category VARCHAR(50),
    date_of_loss DATE,
    location JSONB,
    status VARCHAR(30) DEFAULT 'draft',
    total_estimate DECIMAL(12,2),
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE damage_photos (
    id UUID PRIMARY KEY,
    claim_id UUID REFERENCES claims(id),
    s3_key TEXT NOT NULL,
    thumbnail_key TEXT,
    damage_type VARCHAR(100),
    severity_score FLOAT,
    ai_annotations JSONB,
    metadata JSONB,
    fraud_score FLOAT,
    uploaded_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE cost_estimates (
    id UUID PRIMARY KEY,
    claim_id UUID REFERENCES claims(id),
    line_items JSONB NOT NULL,
    labor_cost DECIMAL(10,2),
    material_cost DECIMAL(10,2),
    total_cost DECIMAL(12,2),
    region VARCHAR(50),
    xactimate_sync BOOLEAN DEFAULT FALSE,
    generated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE reports (
    id UUID PRIMARY KEY,
    claim_id UUID REFERENCES claims(id),
    report_type VARCHAR(50),
    format VARCHAR(10),
    s3_key TEXT,
    generated_at TIMESTAMP DEFAULT NOW()
);
```

### API Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/claims` | Create new claim |
| POST | `/api/claims/:id/photos` | Upload damage photos |
| POST | `/api/claims/:id/analyze` | Run AI damage analysis |
| GET | `/api/claims/:id/estimate` | Get cost estimate |
| POST | `/api/claims/:id/report` | Generate report |
| GET | `/api/claims/:id/fraud-check` | Run fraud detection |
| POST | `/api/photos/enhance` | Enhance/annotate photo |

## Monetization
- **Adjuster ($29/mo):** 20 claims/mo, damage detection, basic estimates
- **Pro ($79/mo):** 100 claims/mo, Xactimate integration, full reports
- **Agency ($199/mo):** Unlimited claims, fraud detection, multi-user
- **Carrier ($999/mo):** Enterprise API, custom models, SLA, dedicated support

## Target Markets
- Independent insurance adjusters
- Insurance carriers (claims departments)
- Roofing and restoration contractors
- Property management companies
- Public adjusters
