# Onboarding Agent

An conversational onboarding agent that collects customer information, confirms each value with an LLM, and stores the completed profile in SQLite.

## Features

- Greets a customer and starts an onboarding conversation.
- Collects a first name, middle name, and last name.
- Uses an LLM to classify confirmation responses as:
  - `CONFIRMED`
  - `REJECTED`
  - `CORRECTED`
  - `UNCLEAR`
- Supports re-entering or correcting information.
- Collects an email address, phone number, and customer request and necessary confirmations.
- Saves completed profiles to a local SQLite database.
- Models the conversation as a LangGraph state machine.

## Repository contents

| Path | Purpose |
| --- | --- |
| `onboarding-agent.py` | Main Python-LangGraph workflow script |
| `notebook.ipynb` | Earlier onboarding workflow experiments |
| `prompts` | Example confirmation prompts |
| `Onboardingg.db` | Local SQLite databases created during experimentation |
| `.env` | Local configuration and API keys; do not commit this file |

## Requirements

- Python 3.10 or newer
- An OpenAI API key
- Python packages:
  - `langgraph`
  - `langchain`
  - `langchain-openai`
  - `langchain-core`
  - `pydantic`
  - `python-dotenv`
  - `ipython`

## Installation

Create and activate a virtual environment:

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

Install the dependencies:

```bash
pip install langgraph langchain langchain-openai langchain-core pydantic python-dotenv ipython
```

## Configuration

Create a local `.env` file and add your API key:

```env
OPENAI_API_KEY=your_openai_api_key
```

The application loads environment variables with `python-dotenv`. Never commit real API keys or other credentials to source control.

## Running the prototype

Run the main workflow with:

```bash
python onboarding-agent.py
```

The workflow prompts for an initial message and then requests the customer's name, email address, phone number, and request. A SQLite database named `Onboardingg.db` is initialized automatically, and completed profiles are written to the `profile` table.

The notebooks can be opened with Jupyter for experimenting with individual workflow components:

```bash
jupyter notebook
```

## Database schema

The main prototype creates the following table:

```sql
CREATE TABLE profile (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    first_name TEXT,
    last_name TEXT,
    middle_name TEXT,
    phone_number INTEGER,
    email_address TEXT,
    customer_request TEXT
);
```

## Workflow overview

```text
START
  -> take_input
  -> welcome
  -> get_name
  -> confirmation
  -> get_email
  -> confirmation
  -> get_phone_number
  -> confirmation
  -> get_customer_request
  -> insert_profile_to_db
  -> END
```

When a value is rejected, corrected, or unclear, the graph routes back through the relevant collection or clarification node.


## Security notes

- Keep `.env` local and rotate any credential that has been exposed.
- Do not commit API keys, database files containing personal information, or customer conversations.
- Treat collected names, email addresses, phone numbers, and requests as sensitive personal data.
