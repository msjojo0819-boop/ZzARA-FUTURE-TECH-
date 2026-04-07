# JuryBox AI - Technical Specification

## Product Overview
**Product Name:** JuryBox AI
**Version:** 1.0
**Category:** Jury Selection & Trial Analysis AI
**Priority:** Tier 3 - Vertical AI Solution

## Executive Summary
JuryBox AI is an AI-powered jury selection and trial strategy platform that helps attorneys make data-driven decisions during voir dire. It analyzes juror profiles, predicts verdict tendencies, tracks courtroom dynamics in real-time, and recommends strike strategies based on case-specific factors.

## Core Features

### 1. Juror Profile Analyzer
- **Public Records Integration:** Auto-pull voter registration, property records, litigation history
- **Social Media Scanner:** Analyze public social media for bias indicators (with consent/compliance)
- **Demographic Modeling:** Statistical models for juror tendency by demographics
- **Questionnaire Analyzer:** NLP analysis of written juror questionnaire responses
- **Bias Detection:** AI-flagged potential bias indicators relevant to case facts
- **Attitude Mapping:** Map juror attitudes on relevant scales (authority, empathy, risk tolerance)

### 2. Voir Dire Assistant
- **Question Generator:** AI-suggested voir dire questions tailored to case type
- **Follow-Up Recommender:** Real-time suggested follow-up questions during voir dire
- **Body Language Notes:** Quick-entry system for recording non-verbal cues
- **Strike Recommendation:** Peremptory and for-cause strike recommendations with reasoning
- **Batson Compliance:** Flag potential Batson challenges before they arise
- **Panel Composition Tracker:** Real-time visualization of seated panel composition

### 3. Trial Strategy Engine
- **Jury Sentiment Tracker:** Log and analyze juror reactions during trial
- **Argument Effectiveness:** Predict which arguments will resonate with seated jury
- **Witness Impact Predictor:** Estimate witness credibility impact on specific jurors
- **Exhibit Strategy:** Recommend exhibit presentation order for maximum impact
- **Closing Argument Optimizer:** Tailor closing themes to seated jury profile

### 4. Analytics & Research
- **Verdict Prediction Model:** Estimate likely verdict range based on jury composition
- **Historical Case Database:** Searchable database of jury verdicts by case type, jurisdiction
- **Venue Analysis:** Statistical profiles of jury pools by venue/county
- **Attorney Win Rates:** Track opposing counsel's historical trial outcomes
- **Damage Award Modeling:** Predict damage ranges based on case and jury factors

## Technical Architecture

### System Design
```
┌─────────────────────────────────────────┐
│         JuryBox AI Dashboard            │
│   (React + Real-time Jury Tracker)      │
├─────────────────────────────────────────┤
│           API Gateway (Express)          │
├──────────┬──────────┬───────────────────┤
│ Profile  │ Voir Dire│ Trial             │
│ Analyzer │ Assistant│ Strategy          │
├──────────┴──────────┴───────────────────┤
│     NLP + ML Pipeline (Claude + Py)      │
├──────────┬──────────┬───────────────────┤
│ Postgres │ Elastic  │ Redis             │
│ (Core)   │ (Search) │ (Cache)           │
└──────────┴──────────┴───────────────────┘
```

### Database Schema
```sql
CREATE TABLE cases (
    id UUID PRIMARY KEY,
    attorney_id UUID REFERENCES users(id),
    case_number VARCHAR(100),
    case_type VARCHAR(100),
    jurisdiction VARCHAR(100),
    venue VARCHAR(100),
    opposing_counsel VARCHAR(255),
    judge VARCHAR(255),
    trial_date DATE,
    case_summary TEXT,
    key_issues JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE jurors (
    id UUID PRIMARY KEY,
    case_id UUID REFERENCES cases(id),
    seat_number INT,
    juror_number VARCHAR(50),
    demographics JSONB,
    public_records JSONB,
    questionnaire_analysis JSONB,
    bias_indicators JSONB,
    attitude_scores JSONB,
    voir_dire_notes JSONB,
    body_language_notes JSONB,
    strike_recommendation VARCHAR(30),
    strike_reason TEXT,
    status VARCHAR(20) DEFAULT 'in_panel'
);

CREATE TABLE voir_dire_questions (
    id UUID PRIMARY KEY,
    case_id UUID REFERENCES cases(id),
    question TEXT,
    category VARCHAR(50),
    target_bias VARCHAR(100),
    priority INT,
    asked BOOLEAN DEFAULT FALSE,
    responses JSONB
);

CREATE TABLE trial_observations (
    id UUID PRIMARY KEY,
    case_id UUID REFERENCES cases(id),
    juror_id UUID REFERENCES jurors(id),
    observation_type VARCHAR(50),
    content TEXT,
    sentiment VARCHAR(20),
    trial_phase VARCHAR(50),
    recorded_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE verdict_predictions (
    id UUID PRIMARY KEY,
    case_id UUID REFERENCES cases(id),
    predicted_outcome VARCHAR(50),
    confidence FLOAT,
    liability_probability FLOAT,
    damage_range JSONB,
    key_factors JSONB,
    generated_at TIMESTAMP DEFAULT NOW()
);
```

### API Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/cases` | Create new case |
| POST | `/api/jurors/analyze` | Analyze juror profile |
| GET | `/api/jurors/:caseId` | Get all juror analyses for case |
| POST | `/api/voir-dire/questions` | Generate voir dire questions |
| POST | `/api/trial/observe` | Log trial observation |
| GET | `/api/trial/:caseId/sentiment` | Get jury sentiment tracker |
| POST | `/api/verdict/predict` | Generate verdict prediction |
| GET | `/api/venue/:jurisdiction` | Get venue analysis |

## Ethical & Legal Compliance
- No use of protected class data for discriminatory strikes
- Batson v. Kentucky compliance built-in
- All data sourced from legally permissible public records
- Transparent AI reasoning (explainable recommendations)
- Attorney work product privilege protections
- State bar ethics rules compliance by jurisdiction
- Data deletion after case conclusion (configurable)

## Monetization
- **Solo ($199/mo):** 3 active cases, juror analysis, basic voir dire tools
- **Trial Team ($499/mo):** 10 cases, real-time trial tracking, verdict prediction
- **Firm ($999/mo):** Unlimited cases, historical database, venue analytics, API
- **Per Trial ($299/trial):** Pay-per-use option for occasional trial attorneys
