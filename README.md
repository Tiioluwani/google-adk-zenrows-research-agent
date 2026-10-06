# Google ADK Zenrows Research Agent

## Description

This project shows how to give a Google Agent Development Kit agent access to a protected or JavaScript-rendered web page. Google ADK handles the agent workflow, while a Python function calls Zenrows [Fetch](https://docs.zenrows.com/fetch/api-reference) and returns Markdown that the model can use for its answer.

The included comparison script sends the same Cloudflare test URL through a standard HTTP request and Zenrows so you can record the difference for the accompanying tutorial.

## Features

- Registers a typed Python function as a Google ADK tool.
- Retrieves protected pages with Zenrows Adaptive Stealth Mode.
- Returns Markdown instead of passing an entire raw HTML document to the model.
- Reports retrieval errors as structured tool results.
- Compares Zenrows with a standard HTTP request on the same URL.
- Includes unit tests that replace external HTTP calls with test responses, so running `pytest` does not spend Zenrows or Gemini credits.

## Prerequisites

- Python 3.10 or later
- A Google AI Studio API key
- A [Zenrows API key](https://app.zenrows.com/register)

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
python -m pip install -r requirements.txt
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

## Project Structure

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
├── LICENSE
├── requirements.txt
├── pyproject.toml
├── README.md
├── uv.lock
└── VALIDATION.md
```

## How It Works

The `fetch_protected_page` function accepts a public URL and sends it to Fetch. It enables Adaptive Stealth Mode with `mode=auto`, asks for Markdown, and returns a dictionary containing the retrieval status, URL, and page content.

The 180-second timeout gives `mode=auto` enough time to complete retrieval on the protected target. The function also checks that the response contains page content before returning it to the agent, preventing an empty response from being treated as a successful retrieval.

Google ADK inspects the function signature and docstring when the function is added to the agent's `tools` list. The agent uses the `gemini-3.6-flash` model, which can select the tool, pass it a URL, and use the returned content to answer the user's research question.

The function limits returned content to 12,000 characters so a large page does not fill the model context unnecessarily. For a production system, replace this simple limit with section selection, extraction, or chunking based on the pages you need to research.

## Running the Project

Run the unit test suite first:

```bash
pytest -q
```

Compare standard HTTP and Zenrows access to the protected test page:

```bash
python scripts/compare_fetch.py
```

Start the Google ADK command-line interface from the repository root:

```bash
adk run zenrows_research_agent
```

Then submit this task:

```text
Fetch https://www.scrapingcourse.com/cloudflare-challenge and report the page title, whether the challenge was passed, and the visible verification text. Cite the source URL.
```

## Output

The successful comparison run produced:

```json
{
  "standard_http": {
    "status": "blocked",
    "url": "https://www.scrapingcourse.com/cloudflare-challenge",
    "status_code": 403,
    "content_preview": "<!DOCTYPE html><html lang=\"en-US\"><head><title>Just a moment...</title><meta http-equiv=\"Content-Type\" content=\"text/html; charset=UTF-8\"><meta http-equiv=\"X-UA-Compatible\" content=\"IE=Edge\"><meta name=\"robots\" content=\"noindex,nofollow\"><meta name=\"viewport\" content=\"width=device-width,initial-scal",
    "error": null
  },
  "zenrows": {
    "status": "success",
    "url": "https://www.scrapingcourse.com/cloudflare-challenge",
    "status_code": null,
    "content_preview": "[![](https://www.scrapingcourse.com/assets/images/logo.svg) Scraping Course](http://www.scrapingcourse.com/)\n\n# Cloudflare Challenge\n\n![](https://www.scrapingcourse.com/assets/images/challenge.svg)\n\n## You bypassed the Cloudflare challenge! :D",
    "error": null
  }
}
```

The successful ADK response was:

```text
Based on the content retrieved from the URL, here are the requested details:

* **Source URL:** https://www.scrapingcourse.com/cloudflare-challenge
* **Page Title / Main Heading:** Cloudflare Challenge
* **Challenge Passed Status:** Yes, the Cloudflare challenge was successfully passed/bypassed.
* **Visible Verification Text:** `You bypassed the Cloudflare challenge! :D`
```

## Technologies

- Python
- Google Agent Development Kit
- Gemini API
- Zenrows Fetch API
- Requests
- pytest
- Ruff

## Related Article
