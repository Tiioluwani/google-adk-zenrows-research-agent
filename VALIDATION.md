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

The standard HTTP request returned `403`, and its response contained `Just a moment...`. Fetch did not return page content because the configured Zenrows credential received HTTP `401 Unauthorized`.

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
    "status": "error",
    "url": "https://www.scrapingcourse.com/cloudflare-challenge",
    "status_code": null,
    "content_preview": "",
    "error": "401 Client Error: Unauthorized for url: https://api.zenrows.com/v1/?apikey=[REDACTED]&url=https%3A%2F%2Fwww.scrapingcourse.com%2Fcloudflare-challenge&mode=auto&response_type=markdown&original_status=true"
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

The run emitted experimental warnings for `InMemoryCredentialService`, `BaseCredentialService`, and `FeatureName.JSON_SCHEMA_FOR_FUNC_DECL`. It then failed before calling Fetch:

```text
google.genai.errors.ServerError: 503 UNAVAILABLE. This model is currently experiencing high demand. Spikes in demand are usually temporary. Please try again later.
```

No successful ADK response was produced during this validation run.
