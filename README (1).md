# 📘 Project Logbook

## Project Title: Digital Footprint Analyzer — Email & Domain Exposure Risk Scanner

---

## Index

| Sr.No | Week No | Contents | Date |
|-------|---------|----------|------|
| 1 | Week 01 | Project Group Formation | — |
| 2 | Week 02 | Project Topic Finalization | — |
| 3 | Week 03 | Requirement Analysis | — |
| 4 | Week 04 | UI Design & Layout Planning | — |
| 5 | Week 05 | Implementation Phase – I (Domain Lookup Module) | — |
| 6 | Week 06 | Implementation Phase – II (Dataset & Risk Scoring Model) | — |
| 7 | Week 07 | Implementation Phase – III (K-Means Clustering Algorithm) | — |
| 8 | Week 08 | Implementation Phase – IV (Risk Report & Filtering UI) | — |
| 9 | Week 09 | Implementation Phase – V (Testing & Hardening) | — |
| 10 | Week 10 | System Testing | — |
| 11 | Week 11 | Results & Analysis | — |
| 12 | Week 12 | Final Report & Conclusion | — |

---

## Week 01: Project Group Formation

**Date:** —

- Formed the project group and assigned roles:
  - Domain/public-record lookup logic
  - Risk scoring & feature engineering
  - Clustering & ML logic
  - Documentation & reporting

- Brainstormed real-world problems around personal data exposure — how little visibility a typical user has into their own account footprint.

### Guide Interaction

Guide suggested choosing a problem that goes beyond a simple breach-confirmation lookup and instead produces an actionable, ranked view of risk.

### Next Plan

Finalize project topic.

---

## Week 02: Project Topic Finalization

**Date:** —

- Explored domains:
  - Breach-lookup services
  - OSINT / identity-exposure tools
  - Browser-based security self-audit tools

- Identified that existing breach-checkers confirm a leak happened but never combine that signal with 2FA status, account sensitivity, or an overall ranked exposure tier.

- Finalized project:

### Digital Footprint Analyzer

### Guide Interaction

Guide suggested making it fully client-side (no backend, no server) so it could run directly in a browser using free public APIs.

### Next Plan

Requirement analysis.

---

## Week 03: Requirement Analysis

**Date:** —

- Defined system inputs:
  - Email address to analyze
  - Domain extracted from the email
  - Platform-login dataset (platform name, category, breach flag, 2FA flag)

- Defined system outputs:
  - Domain-level public DNS / RDAP report (mail servers, hosting, name servers, SPF/DMARC, registration data)
  - Per-platform composite risk score with a stated reason
  - Overall exposure tier (Low / Medium / High) via clustering

- Studied existing tools and identified limitations:
  - Breach-checkers (point-in-time confirmation only, no ranked view)
  - OSINT username/email search tools (list sites but leave risk judgement to the user)
  - No tool combined live public-record lookup with per-platform scoring and unsupervised clustering

### Guide Interaction

Guide suggested clearly separating what is genuinely live public data (DNS/RDAP) from what is a constructed sample dataset, so the project doesn't overstate its data sources.

### Next Plan

UI design and layout planning.

---

## Week 04: UI Design & Layout Planning

**Date:** —

- Designed a single-page scan interface:
  - Email input with a "Scan" action
  - Sectioned results read-out (DNS records, platform list, risk report)

- Designed system components:
  - Dark, technical color theme with a signal-teal accent
  - Sectioned record rows styled like a diagnostics read-out
  - Color-coded risk tiers (Low = green, Medium = amber, High = red)
  - Grouped risk-report cards (Good Standing / Needs Attention / High Risk)

- Defined a CSS variable system for consistent theming (`--ink`, `--paper`, `--signal`, `--amber`, `--red`, `--wire`).

### Guide Interaction

Guide suggested keeping the interface minimal and read-only at first (scan → report), adding editable dataset entry only once the core scan flow was solid.

### Next Plan

Start implementation — Domain Lookup module.

---

## Week 05: Implementation Phase – I (Domain Lookup Module)

**Date:** —

- Implemented email-to-domain extraction via regex validation.

- Implemented live domain lookups using **Google Public DNS-over-HTTPS**:
  - MX (mail servers)
  - A / AAAA (hosting)
  - NS (name servers)
  - TXT (SPF / DMARC email-authentication records)

- Implemented **RDAP** lookup (`rdap.org`) for domain registration data (registrar, registration/expiry dates, status), with graceful fallback when a registry blocks cross-origin requests.

- Implemented a plain-language summary line combining all of the above into one readable sentence.

### Guide Interaction

Guide suggested handling the case where RDAP is unavailable for a given registry explicitly, rather than letting the section fail silently.

### Next Plan

Dataset and risk-scoring model.

---

## Week 06: Implementation Phase – II (Dataset & Risk Scoring Model)

**Date:** —

- Constructed a sample dataset of 25 synthetic profiles covering 107 platform-account records across 10 account categories (cloud, social, professional, shopping, finance, developer tools, entertainment, gaming, dating, health).

- Built a **platform reputation baseline** (`PLATFORM_RISK_PROFILE`) — a curated low/medium/high rating with a stated reason for ~70 common platforms, based on general public information about each platform's security history.

- Implemented `compositeRisk()` — combines:
  - Platform reputation baseline
  - This account's own breach flag
  - This account's own 2FA status

  into a single score with human-readable reasons, categorized as Good / Moderate / High risk.

### Guide Interaction

Guide suggested the dataset and its ratings be clearly labeled as illustrative/demo data in any written report, since platform security postures change over time and the ratings aren't a live, verified audit.

### Next Plan

Clustering algorithm.

---

## Week 07: Implementation Phase – III (K-Means Clustering Algorithm)

**Date:** —

- Implemented feature extraction per profile: breached-account count, accounts-without-2FA count, sensitive-category account count, and total platform count.

- Implemented min-max normalization across the dataset.

- Implemented **k-means clustering from scratch** (no external ML library) with k = 3:
  - Centroid initialization at spread-out sample points
  - Iterative assignment + centroid update until convergence
  - Cluster-to-tier labeling (Low / Medium / High) by ranking centroids on a weighted risk-feature sum

- Verified that clustering is **genuinely unsupervised** — tiers emerge from the data rather than from fixed thresholds.

### Guide Interaction

Guide suggested re-running clustering live in the browser on every dataset change (rather than hardcoding tier assignments), so the system remains honestly described as using real ML.

### Next Plan

Risk report and filtering UI.

---

## Week 08: Implementation Phase – IV (Risk Report & Filtering UI)

**Date:** —

- Implemented the grouped risk-report view: **Good Standing / Needs Attention / High Risk**, each card listing the specific reasons behind the score.

- Implemented a **"Show where my data is at risk"** toggle button that filters the report down to only the at-risk categories.

- Implemented an **"Add your own entry"** form allowing a user to extend the dataset, persisted via the browser's storage API and merged with the seed dataset on load.

- Implemented cluster-context messaging (e.g., "N other entries share this exposure tier").

### Guide Interaction

Guide suggested the filter button use plain, non-technical language ("Show where my data is at risk") rather than exposing internal cluster-label terminology.

### Next Plan

Testing and hardening.

---

## Week 09: Implementation Phase – V (Testing & Hardening)

**Date:** —

- Diagnosed and fixed a silent-failure bug where the Scan button appeared to do nothing when the typed email wasn't an exact dataset match.

- Added defensive guards:
  - `try/catch` around the full scan routine, surfacing errors instead of failing silently
  - Safe fallback when the browser storage API is unavailable (e.g., file opened outside the hosting environment)
  - Clickable sample-email chips for one-tap testing, instead of requiring exact typing

- Added auto-scroll to the result/error region so feedback is never missed below the fold.

### Guide Interaction

Guide suggested simulating the scan flow in a headless DOM environment (not just visual inspection) to catch silent JavaScript errors before they reached users.

### Next Plan

System testing.

---

## Week 10: System Testing

**Date:** —

- Tested:
  - Valid dataset emails via both manual entry and quick-scan chips
  - Emails not present in the dataset (error messaging)
  - Domain lookups for domains with and without published SPF/DMARC records
  - RDAP unavailability fallback for restrictive registries
  - Risk-report filter toggle in both directions
  - Custom dataset entry addition and persistence across reloads
  - Behavior with and without the browser storage API available

### Guide Interaction

Guide suggested testing edge cases explicitly: an email with zero platforms, a profile with all accounts breached, and a profile with a fully clean footprint.

### Next Plan

Results analysis.

---

## Week 11: Results & Analysis

**Date:** —

- Ran the full pipeline (feature extraction → normalization → k-means clustering) against the complete 25-profile, 107-account sample dataset.

- **Exposure tier distribution:** Low — 11 profiles (44%), Medium — 5 profiles (20%), High — 9 profiles (36%).

- **Platform-level risk category distribution:** Good — 61 accounts (57%), Moderate — 16 accounts (15%), High Risk — 30 accounts (28%).

- 30 of 107 accounts (28%) carried a known breach flag; 68 of 107 (64%) lacked two-factor authentication.

- Manual inspection confirmed High-tier profiles consistently combined multiple breached accounts, low 2FA coverage, and at least one sensitive-category account — indicating the unsupervised clusters align with intuitive exposure severity despite receiving no labels during training.

- Planned future enhancements:
  - Live breach-lookup API integration (e.g., XposedOrNot)
  - Cluster-count validation via the elbow method
  - Predictive scoring for emails outside the dataset
  - Larger, real-world evaluation dataset

### Guide Interaction

Guide suggested validating k = 3 against the elbow method in a future iteration rather than assuming three tiers is optimal.

### Next Plan

Final documentation.

---

## Week 12: Final Report & Conclusion

**Date:** —

- Completed documentation, project poster, and research paper draft.

- Prepared final demo walkthrough (domain lookup → dataset match → risk report → filter view).

- Project implemented successfully — fully browser-based, zero backend, zero server.

### Guide Interaction

Guide approved final submission.

### Next Plan

Project evaluation.

---

## Project Description

### Digital Footprint Analyzer

Digital Footprint Analyzer is a browser-based tool that assesses how exposed an email address is across its accumulated online footprint. It combines a **live public-record domain lookup** with a **platform-login risk-scoring model** and **unsupervised k-means clustering**, producing an explainable, ranked exposure report — entirely client-side, with no server or installation required.

### Features

- Live domain lookup via public DNS-over-HTTPS and RDAP (mail servers, hosting, name servers, SPF/DMARC, registration data)
- Platform-login dataset matching with per-account breach and 2FA flags
- Composite, per-platform risk scoring with a stated, human-readable reason for every score
- Genuine unsupervised k-means clustering (k = 3) into Low / Medium / High exposure tiers
- Grouped risk report (Good Standing / Needs Attention / High Risk) with a one-click "at-risk only" filter
- User-extensible dataset via an in-app "add your own entry" form, persisted in browser storage
- Fully client-side — no backend, no server, no installation

### Tech Stack

| Layer | Technology |
|-------|------------|
| Structure | HTML5 |
| Styling | CSS3 (custom variables, flexbox) |
| Logic | Vanilla JavaScript |
| Domain Lookup | Google Public DNS-over-HTTPS, RDAP (rdap.org) |
| Machine Learning | Custom k-means clustering (no external ML library) |
| Persistence | Browser Storage API (user-added dataset entries) |
| Fonts | Google Fonts (Space Grotesk + IBM Plex Mono) |

---

## Future Scope

- Integrate a live, free breach-lookup API (e.g., XposedOrNot) in place of the static dataset's breach flag
- Validate cluster count via the elbow method or silhouette analysis instead of a fixed k = 3
- Predictive risk scoring for email addresses not already present in the dataset
- Evaluate against a larger, consent-cleared real-world dataset
- Deletion / opt-out assistance checklist with direct links per flagged platform
- Browser extension version

---

## Team Members

- [Team Member Name 1]
- [Team Member Name 2]
- [Team Member Name 3]
- [Team Member Name 4]

---

## Guide

[Guide Name]
