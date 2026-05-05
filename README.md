# 🗺️ Tourism Australia AI Chatbot

> **Course:** BUS5001 – Cloud Platforms and Analytics | La Trobe University  
> **Tools:** Google Dialogflow CX · Google Cloud Platform · australia-southeast1  
> **Author:** Rahul Narayan Bale | Student ID: 22380204

---

## 📌 Project Overview

This project builds a **rule-based conversational AI chatbot** for Tourism Australia using **Google Dialogflow CX**. The chatbot helps tourists find attractions and activities across Australian cities by guiding them through a structured conversation — collecting their city of interest and preferred activity type, then returning personalised suggestions.

The chatbot uses **Flow mode** (deterministic, rule-based) rather than generative AI, meaning every response follows a predefined path. This makes it predictable, auditable, and safe for a public-facing tourism service.

---

## 🏗️ Chatbot Architecture

```
User starts conversation
          │
          ▼
     Start Page
  (Welcome message → asks for city)
          │
          ▼
  CollectCityPage
  [Parameter: city → @sys.geo-city]
  (Required field — won't proceed until city is provided)
          │
          ▼
  AskActivityPage
  [Parameter: activity → @activity_type]
  (Custom entity with 5 values + synonyms)
          │
          ▼
  SuggestAttractionsPage
  [Uses $session.params.city + $session.params.activity]
  (Returns 3 personalised suggestions)
          │
     ┌────┴────┐
     │  User can│
     │  choose: │
     ▼          ▼
 Change city  More like this  Change activity
     │              │               │
     ▼              ▼               ▼
 Resets params  Shows 3 more   Updates activity
 → back to      suggestions    → back to
 CollectCity    (same city +   SuggestAttractions
                activity)
```

---

## ⚙️ Agent Configuration

| Setting | Value |
|---------|-------|
| Agent Name | Tourism-Australia-Bot |
| Platform | Google Dialogflow CX (Conversational Agents) |
| Region | australia-southeast1 |
| Time Zone | Australia/Sydney (GMT+10) |
| Conversation Mode | Flow (deterministic — not Playbooks/generative) |
| Language | English (en) |
| Total Pages | 4 |
| Total Custom Intents | 4 |
| Total Custom Entities | 1 |

---

## 📄 Conversation Pages

### 1. Start Page
- Triggered by the **Default Welcome Intent**
- Fulfillment message: *"Hi! Welcome to Tourism Australia 🇦🇺. Which city are you planning to visit?"*
- Transition route: condition `true` → always moves to **CollectCityPage**

### 2. CollectCityPage
- Collects one required parameter: **`city`**
- Entity type: **`@sys.geo-city`** (built-in system entity — automatically recognises any valid city name)
- Initial prompt: *"Which city are you visiting? (e.g. Sydney, Melbourne, Brisbane)"*
- Moves forward when: `$page.params.status = "FINAL"`
- Transitions to: **AskActivityPage**

### 3. AskActivityPage
- Collects one required parameter: **`activity`**
- Entity type: **`@activity_type`** (custom entity — see below)
- Initial prompt: *"What would you like to do? Beaches, restaurants, museums, nature, or bars?"*
- Moves forward when: `$page.params.status = "FINAL"`
- Transitions to: **SuggestAttractionsPage**

### 4. SuggestAttractionsPage
- Uses session parameters `$session.params.city` and `$session.params.activity`
- Fulfillment response format:
```
Here are ideas in [city] for [activity]:
1) Spot A — short tip
2) Spot B — short tip
3) Spot C — short tip
Want more like this, or change city?
```
- Has 4 route intents for continued interaction (see Intents below)

---

## 🎯 Custom Intents

| Intent Name | Purpose | Example Training Phrases |
|-------------|---------|--------------------------|
| `intent.provide_city` | Captures user's initial city input | "Perth", "Sydney", "Melbourne" |
| `intent.change_city` | Changes city mid-conversation | "go to Sydney instead", "change to Melbourne", "different city" |
| `intent.provide_activity` | Updates the activity type | "restaurants", "beaches", "museums" |
| `intent.more_like_this` | Requests more suggestions | "show me more", "more like these" |

---

## 🏷️ Custom Entity — @activity_type

| Entity Value | Synonyms |
|-------------|---------|
| museums | art, exhibition, gallery |
| nature | hiking, mountains, trail |
| bars | clubs, dance, drinks |
| restaurants | cafe, dine-in, food |
| beaches | beach, surfing, swimming |

By defining rich synonym lists, users can say *"I want to go hiking"* and the bot correctly maps it to **nature**, or *"I feel like visiting an art gallery"* maps to **museums**.

---

## 🔄 Session Parameter Management

| Parameter | Set By | Used In |
|-----------|--------|---------|
| `$session.params.city` | CollectCityPage | SuggestAttractionsPage fulfillment |
| `$session.params.activity` | AskActivityPage | SuggestAttractionsPage fulfillment |

### How intent.change_city works:
- Resets both `city` and `activity` to **null**
- Transitions back to **CollectCityPage**
- User can enter a new city without restarting the entire conversation

### How intent.provide_activity works:
- Updates `$session.params.activity` using `$intent.parameter`
- Bot responds: *"Sure — switching to [new activity]."*
- Returns to SuggestAttractionsPage with updated context

---

## ✅ Sample Conversation

```
User:  hi
Bot:   Hi! Welcome to Tourism Australia 🇦🇺. Which city are you planning to visit?
       Which city are you visiting? (e.g. Sydney, Melbourne, Brisbane)

User:  Sydney
Bot:   What would you like to do? Beaches, restaurants, museums, nature, or bars?

User:  beaches
Bot:   Here are ideas in Sydney for beaches:
       1) Bondi Beach — iconic surf beach, great for swimming and people-watching
       2) Manly Beach — accessible by ferry, family-friendly
       3) Coogee Beach — quieter alternative with ocean pools
       Want more like this, or change city?

User:  change city
Bot:   Sure! Which city should we switch to?
       Which city are you visiting? (e.g. Sydney, Melbourne, Brisbane)
```

---

## ⚖️ Ethical Considerations

| Consideration | Implementation |
|---------------|----------------|
| Data minimisation | Only city and activity collected — no PII stored |
| Transparency | Privacy notice shown at session start |
| Purpose limitation | Data used only to improve tourism recommendations |
| Bias & fairness | Entity synonyms include diverse activity types; smaller regions represented |
| Accessibility (WCAG) | Short prompts, numbered lists, screen-reader friendly responses |
| Children & family | Family-friendly filter; adult nightlife suppressed for family travel |
| Human escalation | "Talk to an advisor" option available |
| Compliance | Aligned with Australian Privacy Principles (APPs) |

---

## 📁 Repository Structure

```
tourism-australia-chatbot/
│
├── README.md                          ← You are here
│
├── screenshots/
│   ├── 01_agent_general_settings.png  ← Agent name, region, timezone, Flow mode
│   ├── 02_flow_overview.png           ← Full flow diagram (all 4 pages)
│   ├── 03_start_page_routes.png       ← Start page welcome intent + transition
│   ├── 04_collect_city_page.png       ← CollectCityPage parameter setup
│   ├── 05_city_parameter_config.png   ← @sys.geo-city entity config
│   ├── 06_ask_activity_page.png       ← AskActivityPage parameter setup
│   ├── 07_activity_entity.png         ← @activity_type custom entity + synonyms
│   ├── 08_suggest_attractions_page.png ← SuggestAttractionsPage fulfillment
│   ├── 09_route_intents_overview.png  ← All 4 route intents listed
│   ├── 10_intent_change_city.png      ← intent.change_city config + parameter reset
│   ├── 11_intent_more_like_this.png   ← intent.more_like_this fulfillment
│   ├── 12_intents_overview.png        ← All intents list in Dialogflow CX
│   └── 13_simulator_conversation.png  ← Live conversation in the preview simulator
│
└── docs/
    └── ethical_considerations.md      ← Full ethical review write-up
```

---

## 📸 Screenshots

### Agent General Settings
![Agent Settings](screenshots/01_agent_general_settings.png)

### Full Flow Diagram
![Flow Overview](screenshots/02_flow_overview.png)

### Custom Entity — @activity_type
![Activity Entity](screenshots/07_activity_entity.png)

### Live Conversation in Simulator
![Simulator](screenshots/13_simulator_conversation.png)

---

## 💡 Key Design Decisions

- **Flow mode over Playbooks** — ensures predictable, auditable responses suitable for a public-facing government tourism service
- **@sys.geo-city system entity** — eliminates the need to manually define every Australian city; Dialogflow handles extraction automatically
- **$page.params.status = FINAL condition** — ensures the bot never skips ahead without collecting required information
- **Looping routes** — users can change their preferences at any point without restarting, reducing frustration and drop-off
- **Deployed in australia-southeast1** — minimises latency for Australian users

---

## 🛠️ Technologies Used

| Technology | Purpose |
|------------|---------|
| Google Dialogflow CX | Chatbot platform (Flow mode) |
| Google Cloud Platform | Hosting and deployment |
| @sys.geo-city | Built-in system entity for city recognition |
| @activity_type | Custom entity for activity classification |
| Session parameters | Context management across conversation turns |

---

## 👤 About

**Rahul Narayan Bale**  
Master of Business Analytics — La Trobe University, Melbourne  
📧 rahulbale2804@gmail.com  
🔗 [GitHub Profile](https://github.com/RahulBale28)

---

> *This project was completed as part of BUS5001 – Cloud Platforms and Analytics at La Trobe University.*
