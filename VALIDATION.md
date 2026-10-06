# Validation Record

## Environment

- Validation date: October 6, 2026
- Python: 3.12.14
- Google ADK: 2.9.2
- python-dotenv: 1.2.3
- Requests: 2.34.2
- pytest: 9.1.1
- Ruff: 0.16.8
- Model: `gemini-3.6-flash`
- Target: `https://www.scrapingcourse.com/cloudflare-challenge`

## Automated Checks

- `python -m pip install -r requirements.txt`: completed successfully
- `python -m pytest -q`: 6 passed, 1 warning in 1.10s
- `python -m ruff check .`: `All checks passed!`
- `python -m pip wheel . --no-deps -w dist`: successfully built `google_adk_zenrows_research_agent-0.1.0-py3-none-any.whl`

The test run emitted the upstream Google ADK `BaseAgentConfig` deprecation warning. The project does not import or use `BaseAgentConfig` directly.

## Comparison Result

The successful comparison run returned HTTP `403` with `Just a moment...` for the standard request and nonempty Markdown containing `Cloudflare Challenge` and `You bypassed the Cloudflare challenge! :D` for Fetch.

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

## Google ADK Result

Command:

```text
adk run zenrows_research_agent
```

User prompt:

```text
Fetch https://www.scrapingcourse.com/cloudflare-challenge and report the page title, whether the challenge was passed, and the visible verification text. Cite the source URL.
```

The successful live run occurred on September 20, 2026. Google ADK experimental warnings appeared but did not prevent execution. The exact response was:

```text
Based on the content retrieved from the URL, here are the requested details:

* **Source URL:** https://www.scrapingcourse.com/cloudflare-challenge
* **Page Title / Main Heading:** Cloudflare Challenge
* **Challenge Passed Status:** Yes, the Cloudflare challenge was successfully passed/bypassed.
* **Visible Verification Text:** `You bypassed the Cloudflare challenge! :D`
```
