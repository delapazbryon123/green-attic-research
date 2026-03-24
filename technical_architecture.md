# Green Attic: Technical Architecture & Implementation Guide
*A step-by-step blueprint for building the 5-Pillar Enterprise Automation System.*

This document details the exact systems, APIs, and n8n nodes required to execute the strategy proposed in the presentation. By treating Green Attic's business logic as a set of decoupled API events, we can achieve massive scale.

---

## Pillar 1: End-to-End ClickUp Operations & RAG
**Objective**: Eliminate manual project management by dynamically building ClickUp tasks upon CRM deal closure, and implementing an internal RAG assistant.

### The Tech Stack
* **Orchestration**: n8n
* **APIs**: CRM Webhooks, ClickUp API
* **AI Engine**: OpenAI (GPT-4o), Pinecone (Vector Database)
* **Code Layer**: Basic JavaScript inside n8n `Code` nodes

### The Workflow Architecture
1. **Trigger (CRM Deal Won)**: n8n receives a webhook from the CRM containing the Job Details.
2. **Data Transformation (JavaScript)**: An n8n `Code` node parses the `Scope of Work` string.
3. **ClickUp Automation**: Create a new Folder/List in ClickUp and auto-generate Sub-Tasks.
4. **RAG Integration**: Every task status change is summarized by an LLM and Upserted into a Pinecone Vector Database for staff to query instantly.

---

## Pillar 2: AI B2B Lead Generator (Commercial Outreach)
**Objective**: Automate the process of finding and emailing Property Managers in Chicagoland.

### The Tech Stack
* **Orchestration**: Python (Custom GUI Agent) + n8n
* **Data Sources**: LinkedIn / Apollo.io
* **AI Engine**: OpenAI (GPT-4o)
* **Delivery**: Gmail / SMTP API

### The Workflow Architecture
1. **Trigger**: Python GUI scrapes LinkedIn and webhooks JSON to n8n.
2. **AI Personalization**: An `OpenAI` node writes a hyper-personalized email.
3. **Delivery & Sync**: Route via Gmail and post to Airtable for follow-ups.

---

## Pillar 3: Proactive SMS (Closing the "Project Gap")
**Objective**: Automatically update homeowners via text message when a multi-day installation phase is completed.

### The Tech Stack
* **Orchestration**: n8n
* **Field CRM**: Airtable / ServiceTitan / FieldEdge
* **Messaging**: Twilio API

### The Workflow Architecture
1. **Trigger**: Technician taps "Phase 1 Complete" on iPad. Webhook fires to n8n.
2. **Formatting**: JavaScript formats the extracted customer phone number to E.164.
3. **Delivery**: The `Twilio` node fires the SMS.

---

## Pillar 4: AI Content & Omnichannel Marketing Pipeline
**Objective**: Eradicate the manual bottlenecks of social media marketing by using AI Vision models to transform raw field photos into cross-platform campaigns, and automatically repurpose reviews and short-form video.

### The Tech Stack
* **Orchestration**: n8n
* **Inbound Triggers**: ClickUp item updates, Internal Submission Forms, Google My Business (GMB)
* **AI Vision Engine**: OpenAI (GPT-4o Vision API)
* **Image Generation**: Bannerbear API
* **Social APIs**: Facebook/Instagram Graph API, LinkedIn API, YouTube API

### The Workflow Architecture
1. **The "Field-to-Feed" AI Vision Agent**:
    - **Trigger**: An installer uploads a raw "Before and After" photo attachment to a designated ClickUp task or Internal Form.
    - **Extraction**: n8n downloads the binary file.
    - **Vision Processing**: The image file is passed to an `OpenAI` node (Vision capable). The prompt directs the AI to identify what type of insulation job it is, and write three distinct captions (Casual for FB, Hashtag-heavy for IG, Professional for LinkedIn).
    - **Distribution**: The image binary and the corresponding caption texts are routed directly to Facebook, Instagram, and LinkedIn posting nodes simultaneously.
2. **The Social Proof Generator**:
    - **Trigger**: n8n `Schedule` node queries GMB API daily for new reviews.
    - **Filter & Generate**: If Rating == 5, n8n sends the Reviewer Name to Bannerbear which injects the text into a branded Green Attic template URL.
    - **Distribution**: The returned image URL is instantly scheduled to the brand's social feeds.
3. **Video Syndication**:
    - **Trigger**: `YouTube RSS/API` detects a new Short uploaded by a marketing manager.
    - **Transformation**: n8n fetches the video file and uses the native `Instagram Graph API` to auto-publish the Reel, maximizing video reach with zero manual re-uploading.
4. **Omnichannel Output**:
    - **Extraction**: n8n extracts the localized captions and automatically schedules the postings.
    - **Native APIs**: Meta Graph API (Facebook/Instagram), LinkedIn API.
    - **Notification**: Optionally triggers a ClickUp notification to leadership that marketing assets have been deployed.

---

## Pillar 5: Hyper-Local SEO & GEO Engine
**Objective**: Automate SEO blog creation based on real-world triggers to dominate Google and train Generative Engines.

### The Tech Stack
* **Orchestration**: n8n
* **Triggers**: OpenWeatherMap API
* **Content Engine**: OpenAI (GPT-4o) + Perplexity API
* **CMS**: WordPress REST API

### The Workflow Architecture
1. **Trigger**: n8n polls Chicago Weather APIs.
2. **Drafting**: Perplexity retrieves current energy prices; OpenAI drafts an 800-word SEO post referencing the freeze.
3. **Publishing**: The text is beamed to the WordPress API and mapped live automatically.
