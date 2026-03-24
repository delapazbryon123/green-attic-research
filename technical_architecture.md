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
1. **Trigger (CRM Deal Won)**: n8n receives a webhook from the CRM containing the Job Details (Address, Scope of Work, Estimated Days).
2. **Data Transformation (JavaScript)**:
    - An n8n `Code` node executes a JavaScript function to parse the `Scope of Work` string and split it into an array of actionable elements (e.g., mapping "Cellulose Blown-in" to the specific install Crew ID).
3. **ClickUp Automation (n8n native nodes)**:
    - **API Call 1**: Create a new Folder/List in ClickUp named `[Job Address] - [Client Last Name]`.
    - **API Call 2**: Loop over the JS array to auto-generate Sub-Tasks ("Removal", "Install", "Cleanup"), assign the parsed Crew IDs, and set cascading due dates based on the project parameters.
4. **Follow-Up Logic**:
    - A secondary n8n cron job runs daily. If a ClickUp task status is "Past Due," it triggers a warning message to the Operations Slack/Teams channel.
5. **RAG Integration**:
    - Every time a task status changes, the ClickUp payload is summarized by an LLM and Upserted into a Pinecone Vector Database, ready for internal staff to query instantly via a custom chat interface.

---

## Pillar 2: AI B2B Lead Generator (Commercial Outreach)
**Objective**: Automate the process of finding and emailing Property Managers in Chicagoland for commercial warehouse insulation.

### The Tech Stack
* **Orchestration**: Python (Custom GUI Agent) + n8n
* **Data Sources**: LinkedIn / Apollo.io
* **AI Engine**: OpenAI (GPT-4o)
* **Delivery**: Gmail / SMTP API

### The Workflow Architecture
1. **Trigger**: Python GUI scrapes LinkedIn for "Chicago Property Managers" and webhooks the JSON payload to n8n.
2. **AI Personalization**: An `OpenAI` node writes a hyper-personalized email referencing the lead's company.
3. **Delivery**: Route the AI response to a `Gmail` branch node.
4. **Follow-Up Loop**: Send to an Airtable node; use a `Wait` node to evaluate if a reply was received after 3 days.

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
3. **Delivery**: The `Twilio` node fires the SMS: *"Hi there! Phase 1 is complete. Our team will arrive tomorrow for the cellulose installation!"*

---

## Pillar 4: Omnichannel Social Proof Engine
**Objective**: Turn new 5-star reviews into beautiful, branded graphics automatically shared across social media.

### The Tech Stack
* **Orchestration**: n8n
* **Review Source**: Google My Business (GMB)
* **Image Generation**: Bannerbear API
* **Social APIs**: Facebook/Instagram Graph API, LinkedIn API

### The Workflow Architecture
1. **Trigger**: n8n `Schedule` node queries GMB API daily for new reviews.
2. **Filter & Generate**: If Rating == 5, n8n sends the Reviewer Name to Bannerbear which injects the text into a branded Green Attic graphical template.
3. **Distribution**: The returned image URL is passed to n8n social API nodes and instantly scheduled to the brand's feeds.

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
2. **Generative Logic**: `IF` Temp < 32°F → Trigger "Winter Warning" flow.
3. **Drafting**: Perplexity retrieves current energy prices; OpenAI drafts an 800-word SEO post referencing the freeze.
4. **Publishing**: The text is beamed to the WordPress API and mapped live automatically.

---

## Development Notes for the n8n Engineer
* **Error Handling**: All workflows must include global `Error Trigger` nodes that route failure JSONs to a Slack debugging channel so jobs do not silently fail.
* **Hosting**: The entire backbone can be deployed on a high-availability Docker container on AWS EC2, mitigating n8n cloud execution costs.
