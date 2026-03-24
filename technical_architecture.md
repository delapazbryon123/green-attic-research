# Green Attic: Technical Architecture & Implementation Guide
*A step-by-step blueprint for building the 4-Pillar Automation System using your portfolio skills.*

This document details the exact systems, APIs, and n8n nodes required to execute the strategy proposed in the presentation. By treating Green Attic's business logic as a set of decoupled API events, we can achieve massive scale.

---

## Pillar 1: AI B2B Lead Generator (Commercial Outreach)
**Objective**: Automate the process of finding and emailing Property Managers in Chicagoland for commercial warehouse insulation.

### The Tech Stack
* **Orchestration**: Python (Custom GUI Agent) + n8n
* **Data Sources**: LinkedIn / Apollo.io (via API or scraping)
* **AI Engine**: OpenAI (GPT-4o) for email personalization
* **Delivery**: Gmail / SMTP API
* **CRM**: Airtable (or their existing CRM)

### The Workflow Architecture
1. **Trigger (Python GUI)**: The user opens your custom-built Python GUI and types "Chicago Property Managers". 
2. **Extraction Agent**: A Python script runs a headless browser (Playwright/Selenium) or uses Apollo.io APIs to scrape First Name, Last Name, Company, and LinkedIn bio.
3. **Webhook to n8n**: The Python app sends a JSON payload of leads to an n8n `Webhook` node.
4. **AI Personalization (n8n)**:
    - Connect an `OpenAI` node prompting the LLM: *"Write a 3-sentence cold email to [Name] at [Company] offering a free Commercial Insulation Audit. Mention their recent company milestone if available."*
5. **Email Delivery (n8n)**: 
    - Route the AI response to a `Gmail` branch node. Send the email from Dumitru's (or a sales rep's) account.
6. **CRM Sync**: 
    - Add the lead to an `Airtable` node with status "Email Sent." 
    - Set an n8n `Wait` node for 3 days. If no reply is logged, send Follow-Up Email #2 automatically.

---

## Pillar 2: Proactive SMS (Closing the "Project Gap")
**Objective**: Automatically update homeowners via text message when a multi-day installation phase is completed.

### The Tech Stack
* **Orchestration**: n8n
* **Field CRM**: Jobber, ServiceTitan, or Airtable (whatever the field techs use on their iPads)
* **Messaging**: Twilio API

### The Workflow Architecture
1. **Trigger (CRM Event)**: The field technician taps "Phase 1 Complete" on their iPad CRM app. This sends a webhook (or uses an n8n CRM polling node) to n8n.
2. **Data Extraction**: n8n extracts the `Customer Phone Number` and `Next Phase Date`.
3. **Formatting Node**: A simple `Code` node formats the phone number to E.164 format for Twilio.
4. **Delivery**: 
    - The `Twilio` node fires the SMS: *"Hi there! Green Attic here. The old insulation removal is complete. Our team will arrive tomorrow at [Time] for the new cellulose installation!"*

---

## Pillar 3: Omnichannel Social Proof Engine
**Objective**: Turn new 5-star reviews into beautiful, branded graphics automatically shared across social media.

### The Tech Stack
* **Orchestration**: n8n
* **Review Source**: Google My Business (GMB) / Local Search API
* **Image Generation**: Bannerbear or Placid.app (Dynamic Image Generation over API)
* **Social APIs**: Facebook Pages API, Instagram Business API, LinkedIn API

### The Workflow Architecture
1. **Trigger (Polling)**: An n8n `Schedule` node runs daily, triggering a `Google My Business` API call to fetch new reviews.
2. **Filtering**: An `IF` node checks: `Review Rating == 5`. If False, end workflow. If True, proceed.
3. **Image Generation**: 
    - Send the `Reviewer Name` and `Review Text` via API to a `Bannerbear` node.
    - Bannerbear injects the text into your pre-designed "Yellow & Black Glassmorphism" template and returns a high-res image URL.
4. **Social Distribution**:
    - The Image URL is passed to an `HTTP Request` node (or native n8n social nodes) for Facebook, Instagram, and LinkedIn.
    - The caption is generated via a quick `OpenAI` node: *"Thank you [Name] for the kind words! Keeping Chicago warm one attic at a time 🏠🍃"*

---

## Pillar 4: Hyper-Local SEO & GEO Engine
**Objective**: Automate SEO blog creation based on real-world triggers (like Chicago weather) to dominate Google and train AI Generative Engines (ChatGPT).

### The Tech Stack
* **Orchestration**: n8n
* **External Trigger**: OpenWeatherMap API
* **AI Content Engine**: OpenAI (GPT-4o) + Perplexity API (for real-time research)
* **CMS**: WordPress REST API

### The Workflow Architecture
1. **Trigger (Weather API)**: An n8n `Schedule` node runs daily, calling the `OpenWeatherMap API` for Chicago.
2. **Condition Logic**:
    - `IF` Temp < 32°F → Trigger "Winter Warning" flow.
    - `IF` Temp > 90°F → Trigger "Summer Heat" flow.
3. **AI Drafting (The Heavy Lifting)**:
    - Step A: Use `Perplexity API` (via HTTP node) to research *"Recent news about energy grid pricing in Chicago this week."*
    - Step B: Pass that data to an `OpenAI` node. Prompt: *"Write a 800-word SEO-optimized blog post on why Green Attic Insulation protects homes from freezing pipes, referencing the recent [Perplexity Data]. Format with Markdown H2s and H3s."*
4. **CMS Publishing**:
    - Route the generated text to a `WordPress` node.
    - Set post status to "Published" or "Draft" (if they want to manually review it first).
    - Automatically tag it with "Chicago Weather", "Energy Bills", "Frozen Pipes".
5. **Social Signal**: 
    - Send the blog URL back to the Social Proof Engine (Pillar 3) to tweet/post the link automatically.

---

## Implementation Phases (How to pitch the build-out)

**Phase 1 ($): The Quick Win (SMS & Reviews)**
Start by building Pillars 2 and 3. These require no front-end UI work, purely n8n backbone connections. They instantly solve customer anxiety and boost digital presence.

**Phase 2 ($$): The Growth Engine (B2B + GEO)**
Once trust is established, deploy the custom Python Lead Gen Agent and the Weather-Triggered SEO blogger. These directly correlate to massive revenue spikes and organic market domination.
