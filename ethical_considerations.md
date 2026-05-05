# Ethical Considerations — Tourism Australia Chatbot

## Overview
This document outlines the ethical design decisions made for the Tourism Australia Dialogflow CX chatbot, aligned with Australian Privacy Principles (APPs), WCAG accessibility standards, and responsible AI guidelines.

---

## 1. Data Minimisation
- The chatbot only collects **city** and **activity type** — nothing else
- No personally identifiable information (PII) is requested or stored
- Session parameters are cleared when users switch topics using `Parameter presets → null`

## 2. Transparency and Consent
- A short privacy notice is shown at the start of every session explaining:
  - What data is collected (city, activity preference)
  - Why it is collected (to provide attraction suggestions)
  - How long it is retained
- Users are offered an opt-out of analytics ("Don't save my chat" option)

## 3. Purpose Limitation and Retention
- Data is used **only** to improve tourism recommendations
- Retention policy: aggregate logs deleted after 30 days; raw transcripts deleted after 90 days

## 4. Security and Access Controls
- Data encrypted in transit and at rest
- Service credentials rotated regularly
- Raw logs are not shared with third parties
- Access to aggregated dashboards is restricted

## 5. Bias and Fairness
- Entity synonyms and suggestions include diverse activity types
- Smaller regions and diverse attractions (First Nations cultural sites, accessibility-friendly venues) are represented in recommendations
- Suggestion rates are audited periodically to detect regional or activity-type skew

## 6. Children and Vulnerable Users
- No user profiling is performed
- Family-friendly filter available
- Adult nightlife suggestions (bars, clubs) are suppressed when users indicate family travel

## 7. Human Oversight and Escalation
- "Talk to an advisor" option available at any point in the conversation
- Emergency guidance included (e.g. "call 000" in Australia for emergencies)
- Escalation events are logged to improve training data — not to penalise users

## 8. Accessibility (WCAG Compliance)
- Prompts are short and written in plain language — no idioms or complex grammar
- Numbered lists (1, 2, 3) used in responses so screen readers announce items clearly
- Fixed quick replies ("Change city", "More like this") reduce typing effort
- Works with both text and voice input
- When embedded on web: minimum 16px font, accessible colour contrast, meaning not conveyed by colour alone

## 9. Compliance
- Aligned with **Australian Privacy Principles (APPs)**
- Aligned with **WCAG 2.1** accessibility guidelines
- A Data Protection Impact Summary is recommended before public deployment
