Dialogflow CX Agent Automation Toolkit

A Python automation toolkit for managing Dialogflow CX agents using YAML configuration files.

This project supports:

✅ Creating custom entity types

✅ Creating intents with parameters

✅ CSV → YAML conversion

✅ Optional Gemini-based training phrase enrichment

✅ Rate-limit handling with exponential backoff

✅ Intent verification via listing

📦 Project Components
1️⃣ dialogflow_agent_manager.py

Manages Dialogflow CX entities and intents using a YAML configuration file.

Capabilities

Create custom entity types

Create intents

Add parameters linked to entity types

Resolve system and custom entities

Retry on API rate limits (HTTP 429)

Skip existing resources

List current intents

2️⃣ csv2yaml.py

Converts a CSV file of intents and training phrases into a YAML configuration file.

Groups phrases by intent

Applies default metadata

Outputs agent_config_params.yaml

3️⃣ config.yaml

Central configuration file controlling:

Dialogflow credentials and agent

Input/output YAML files

Gemini enrichment settings

🗂 Project Structure
project/
│
├── dialogflow_agent_manager.py
├── csv2yaml.py
├── config.yaml
├── intents.csv
├── agent_config_params.yaml
├── enriched_agent_config.yaml
└── service-account.json
⚙️ Configuration File (config.yaml)
# Dialogflow CX Agent Configuration
dialogflow:
  creds_path: "cisco-sales-demo-bot.json"
  agent_path: "projects/cisco-sales-demo-bot/locations/global/agents/xxxx"

# Agent Configuration YAML Files
agent_config:
  original_file: "agent_config_params.yaml"
  enriched_file: "enriched_agent_config.yaml"

# Gemini Enrichment Configuration
gemini_enrichment:
  enabled: false
  api_key: "YOUR_GEMINI_API_KEY_HERE"
  phrases_to_generate: 5
🔹 Dialogflow Section
Field	Description
creds_path	Path to GCP service account JSON key
agent_path	Full Dialogflow CX agent resource path

Example format:

projects/<project-id>/locations/<location>/agents/<agent-id>
🔹 Agent Config Section
Field	Description
original_file	Base YAML generated from CSV
enriched_file	Output YAML after Gemini enrichment
🔹 Gemini Enrichment Section

Optional AI-based training phrase expansion.

Field	Description
enabled	Enable/disable enrichment
api_key	Required if enabled
phrases_to_generate	Number of phrases to generate per intent

If enabled: false, the system skips enrichment and uses the original YAML.

🚀 Installation
Requirements

Python 3.8+

Google Cloud project with Dialogflow CX agent

Service account with appropriate IAM permissions

Install dependencies:

pip install dfcx-scrapi pyyaml
🔐 Required IAM Permissions

Your service account must have:

dialogflow.intents.create

dialogflow.intents.list

dialogflow.entityTypes.create

dialogflow.entityTypes.list

Recommended role:

Dialogflow CX Admin
or

Custom least-privilege role with the above permissions

📝 Workflow
Step 1 — Create YAML from CSV

Prepare intents.csv:

Intents,training_phrases
ValidateAccount,Validate my account
ValidateAccount,Check my savings account
TransferMoney,Transfer funds

Run:

python csv2yaml.py

Output:

agent_config_params.yaml
Step 2 — (Optional) Gemini Enrichment

If enabled in config.yaml, the system:

Reads original_file

Generates additional training phrases

Outputs enriched_file

If disabled, enrichment is skipped.

Step 3 — Deploy to Dialogflow CX

Example:

from dialogflow_agent_manager import DialogflowAgentManager
import yaml

with open("config.yaml") as f:
    config = yaml.safe_load(f)

creds_path = config["dialogflow"]["creds_path"]
agent_path = config["dialogflow"]["agent_path"]

manager = DialogflowAgentManager(creds_path, agent_path)

config_file = config["agent_config"]["original_file"]

with open(config_file) as f:
    agent_config = yaml.safe_load(f)

manager.create_entities(agent_config)
manager.create_intents(agent_config)# DFCX-Intent-entity-creation
