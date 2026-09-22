# Validation record

## Environment

- Live run date: September 20, 2026
- Python: 3.12.14
- Google ADK: 2.9.2
- Requests: 2.34.2
- Model: `gemini-3.6-flash`
- Target: `https://www.scrapingcourse.com/cloudflare-challenge`

## Automated checks

- `pytest -q`: 5 passed
- `ruff check .`: passed
- `uv build`: source distribution and wheel built successfully

Google ADK experimental warnings appeared during the live run but did not prevent execution. Google ADK 2.9.2 also emits one upstream deprecation warning for `BaseAgentConfig` while importing the agent. The project does not import or use `BaseAgentConfig` directly.

## Live ADK result

An earlier live ADK attempt reached the model provider, but `gemini-flash-latest` returned a temporary `503 UNAVAILABLE` response because the model was experiencing high demand. The project was then pinned to `gemini-3.6-flash`.

The live run succeeded on September 20, 2026, using `gemini-3.6-flash`.

Command:

```text
adk run zenrows_research_agent
```

User prompt:

```text
Fetch https://www.scrapingcourse.com/cloudflare-challenge and report the page title, whether the challenge was passed, and the visible verification text. Cite the source URL.
```

Verified result:

- Source URL: `https://www.scrapingcourse.com/cloudflare-challenge`
- Page title/main heading: `Cloudflare Challenge`
- Challenge passed: Yes
- Visible verification text: `You bypassed the Cloudflare challenge! :D`

## Standard HTTP result

The comparison script returned HTTP `403`. The response began with a page titled `Just a moment...`, which confirms that the ordinary request received the Cloudflare challenge rather than the target content.

## Zenrows comparison result

The earlier Zenrows comparison returned `success` with the protected page content.
The Zenrows request configuration now uses Adaptive Stealth Mode with `mode=auto` and `response_type=markdown`.
