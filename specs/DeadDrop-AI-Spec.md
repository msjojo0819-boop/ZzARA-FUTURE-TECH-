# DeadDrop AI - Technical Specification

## Product Overview
**Product Name:** DeadDrop AI
**Version:** 1.0
**Category:** Whistleblower Anonymization & Secure Communication Platform
**Priority:** Tier 3 - Vertical AI Solution

## Executive Summary
DeadDrop AI is a secure, anonymous communication platform designed for whistleblowers, journalists, and organizations that need to receive sensitive information while protecting source identity. It uses AI to automatically strip identifying metadata, rephrase writing to remove stylometric fingerprints, and create secure one-way communication channels.

## Core Features

### 1. Anonymization Engine
- **Metadata Scrubber:** Strip EXIF data, document metadata, device identifiers from all uploads
- **Stylometric Anonymizer:** AI rewrites text to remove writing style fingerprints
- **Voice Anonymizer:** Real-time voice modification for audio submissions
- **Location Masking:** Tor integration + timestamp randomization
- **Device Fingerprint Prevention:** Browser fingerprint randomization

### 2. Secure Drop System
- **One-Way Channels:** Create anonymous submission portals for organizations
- **Encrypted Storage:** Zero-knowledge encryption (organization can read, platform cannot)
- **Ephemeral Messaging:** Self-destructing messages with configurable timers
- **Dead Man's Switch:** Auto-publish if submitter doesn't check in
- **Multi-Party Encryption:** Share with multiple vetted recipients simultaneously

### 3. AI Analysis Tools
- **Document Classification:** Auto-categorize submissions by topic, urgency, credibility
- **Verification Assist:** Cross-reference claims against public databases
- **Redaction Engine:** Auto-identify and redact PII in documents before sharing
- **Summary Generator:** Create briefings from lengthy document submissions
- **Pattern Detection:** Identify related submissions from different sources

### 4. Organization Portal
- **Intake Dashboard:** Manage incoming submissions with workflow
- **Source Protection:** Internal access controls to limit who sees source indicators
- **Compliance Module:** Track handling procedures for legal compliance
- **Audit Trail:** Cryptographic proof of chain of custody without revealing sources
- **API Access:** Integrate with existing newsroom/compliance systems

## Technical Architecture

### Security-First Design
```
Source Device → Tor Network → 
  → DeadDrop Onion Service →
  → Anonymization Pipeline →
  → Zero-Knowledge Encrypted Storage →
  → Organization Portal (Client-Side Decryption)
```

### Encryption Stack
- **Transport:** TLS 1.3 + Tor Hidden Service
- **At Rest:** AES-256-GCM with per-submission keys
- **Key Management:** Organization-held keys (platform has zero knowledge)
- **Metadata:** Stripped before storage; no IP logging
- **Authentication:** Optional PGP-based identity verification

### Database Schema
```sql
CREATE TABLE drop_boxes (
    id UUID PRIMARY KEY,
    organization_id UUID REFERENCES organizations(id),
    name VARCHAR(255),
    public_key TEXT NOT NULL,
    onion_address VARCHAR(255),
    submission_guidelines TEXT,
    active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE submissions (
    id UUID PRIMARY KEY,
    drop_box_id UUID REFERENCES drop_boxes(id),
    encrypted_content BYTEA NOT NULL,
    content_type VARCHAR(50),
    anonymization_level VARCHAR(20),
    classification VARCHAR(50),
    urgency VARCHAR(20),
    status VARCHAR(30) DEFAULT 'new',
    received_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE secure_messages (
    id UUID PRIMARY KEY,
    submission_id UUID REFERENCES submissions(id),
    direction VARCHAR(10),
    encrypted_body BYTEA NOT NULL,
    expires_at TIMESTAMP,
    read BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### API Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/submit` | Anonymous submission (via Tor) |
| GET | `/api/dropbox/:id/info` | Get submission guidelines |
| GET | `/api/submissions` | List submissions (org auth) |
| GET | `/api/submissions/:id` | Get submission (org auth) |
| POST | `/api/messages` | Send secure reply to source |
| POST | `/api/analyze/redact` | Auto-redact PII from document |
| POST | `/api/analyze/classify` | Classify submission |

## Security Requirements
- Mandatory Tor access for submitters
- No server-side logs of source activity
- Regular security audits by independent firms
- Bug bounty program
- Warrant canary system
- Infrastructure distributed across privacy-friendly jurisdictions
- Open-source core components for public audit

## Monetization
- **Newsroom ($149/mo):** 1 drop box, basic anonymization, 10 submissions/mo
- **Investigation ($399/mo):** 5 drop boxes, full AI analysis, unlimited submissions
- **Enterprise ($999/mo):** Unlimited, API, compliance module, audit trail, SLA
- **Open Source:** Self-hosted option available (community edition)

## Compliance
- First Amendment protections (press freedom)
- EU Whistleblower Protection Directive compliant
- Dodd-Frank whistleblower provisions compatible
- SOX compliance channel capability
