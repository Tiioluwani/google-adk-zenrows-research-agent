# Google ADK Zenrows research agent

## Description

This project shows how to give a Google Agent Development Kit agent access to a protected or JavaScript-rendered web page. Google ADK handles the agent workflow, while a Python function calls Zenrows and returns Markdown that the model can use for its answer.

The included comparison script sends the same Cloudflare test URL through a standard HTTP request and Zenrows so you can record the difference for the accompanying tutorial.

## Features

- Registers a typed Python function as a Google ADK tool.
- Retrieves protected pages with Zenrows Adaptive Stealth Mode.
- Returns Markdown instead of passing an entire raw HTML document to the model.
- Reports retrieval errors as structured tool results.
- Compares Zenrows with a standard HTTP request on the same URL.
- Includes mocked tests that do not spend Zenrows or Gemini credits.

## Prerequisites

- Python 3.10 or later
- A Google AI Studio API key
- A Zenrows API key

## Installation

Clone the repository and enter its directory:

```bash
git clone https://github.com/Tiioluwani/google-adk-zenrows-research-agent.git
cd google-adk-zenrows-research-agent
```

Create and activate a virtual environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

On Windows PowerShell, activate it with:

```powershell
.venv\Scripts\Activate.ps1
```

Install the project and its development dependencies:

```bash
python -m pip install -e ".[dev]"
```

## Configuration

Copy the environment template:

```bash
cp .env.example .env
```

On Windows PowerShell, use:

```powershell
Copy-Item .env.example .env
```

Add your keys to `.env`:

```dotenv
GOOGLE_API_KEY=your_google_api_key
ZENROWS_API_KEY=your_zenrows_api_key
```

The `.gitignore` file excludes `.env` so the keys are not committed.

## Project structure

```text
google-adk-zenrows-research-agent/
├── scripts/
│   └── compare_fetch.py
├── tests/
│   ├── test_agent.py
│   └── test_web.py
├── zenrows_research_agent/
│   ├── __init__.py
│   ├── agent.py
│   └── web.py
├── .env.example
├── .gitignore
├── pyproject.toml
└── README.md
```

## How it works

The `fetch_protected_page` function accepts a public URL and sends it to the Zenrows Fetch API. It enables Adaptive Stealth Mode with `mode=auto`, asks for Markdown, and returns a dictionary containing the retrieval status, URL, and page content.

Google ADK inspects the function signature and docstring when the function is added to the agent's `tools` list. The agent uses the stable `gemini-3.6-flash` model, which can select the tool, pass it a URL, and use the returned content to answer the user's research question.

The function limits returned content to 12,000 characters so a large page does not fill the model context unnecessarily. For a production system, replace this simple limit with section selection, extraction, or chunking based on the pages you need to research.

## Running the project

Run the mocked test suite first:

```bash
pytest -q
```

Compare standard HTTP and Zenrows access to the protected test page:

```bash
python scripts/compare_fetch.py
```

Start the Google ADK command-line interface from the directory above the project folder:

```bash
adk run google-adk-zenrows-research-agent/zenrows_research_agent
```

Then submit this task:

```text
Fetch https://www.scrapingcourse.com/cloudflare-challenge and report the page title, whether the challenge was passed, and the visible verification text. Cite the source URL.
```

## Output

The comparison script prints valid JSON with separate `standard_http` and `zenrows` results. A successful validation should show the HTTP request as blocked and the Zenrows request as successful, but the exact status and page text must come from your live run.

The ADK agent should call `fetch_protected_page` and answer from the returned Markdown. If retrieval fails, its instruction requires it to report the failure instead of inventing page content.

## Technologies

- Python
- Google Agent Development Kit
- Gemini API
- Zenrows Fetch API
- Requests
- pytest
- Ruff

## Related article
