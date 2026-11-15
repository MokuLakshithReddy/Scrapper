# Playwright Title Crawler (Apify Actor)

[![Forks](https://img.shields.io/github/forks/MokuLakshithReddy/Scrapper?style=flat-square)](https://github.com/MokuLakshithReddy/Scrapper/forks) [![Open Issues](https://img.shields.io/github/issues/MokuLakshithReddy/Scrapper?style=flat-square)](https://github.com/MokuLakshithReddy/Scrapper/issues)


A small Apify Actor starter that crawls a bounded set of web pages with **Python, Playwright, and the Apify request queue**, then stores each page's URL and document title in the default dataset.

It is intentionally a minimal crawler example—not a general-purpose extraction platform.

[![Python](https://img.shields.io/badge/Python-3-blue?logo=python)](https://www.python.org/)
[![Playwright](https://img.shields.io/badge/Browser-Playwright-2EAD33?logo=playwright)](https://playwright.dev/python/)
[![Apify SDK](https://img.shields.io/badge/Platform-Apify-00AEEF)](https://docs.apify.com/sdk/python)
[![Stars](https://img.shields.io/github/stars/MokuLakshithReddy/Scrapper?style=flat-square)](https://github.com/MokuLakshithReddy/Scrapper/stargazers)
[![Last commit](https://img.shields.io/github/last-commit/MokuLakshithReddy/Scrapper?style=flat-square)](https://github.com/MokuLakshithReddy/Scrapper/commits/main)

## What it does

- Accepts one or more starting URLs from the Actor input.
- Adds URLs to an Apify request queue and opens pages in headless Chromium through Playwright.
- Follows links while the configured crawl depth allows it.
- Extracts the page URL and title and pushes each record to the default dataset.
- Logs extraction failures and marks processed requests as handled.

## Crawl flow

~~~mermaid
flowchart TD
    A["Actor input: start_urls + max_depth"] --> B["Apify request queue"]
    B --> C["Playwright / Chromium page"]
    C --> D["Read page title and URL"]
    C --> E{"Depth below max_depth?"}
    E -->|Yes| F["Enqueue discovered HTTP(S) links"]
    F --> B
    E -->|No| G["Skip link expansion"]
    D --> H["Push record to Apify dataset"]
    G --> H
~~~

## Current limits

| Setting | Current behavior |
|---|---|
| Maximum handled requests | **10 per actor run** (MAX_REQUESTS_PER_CRAWL) |
| Default starting URL | https://apify.com when omitted |
| Default crawl depth | 1 |
| Extracted fields | url, title |
| Browser | Playwright Chromium |

The crawler does not currently extract article bodies, images, prices, or arbitrary CSS-selected fields. It also does not publish a throughput benchmark; runtime depends on the target sites and network conditions.

## Run locally

Requirements: Python 3.10+ and a Playwright-compatible Chromium installation.

~~~bash
python -m venv .venv
pip install -r requirements.txt
playwright install chromium
apify run
~~~

Configure the Actor using .actor/input_schema.json:

~~~json
{
  "start_urls": [{ "url": "https://example.com" }],
  "max_depth": 1
}
~~~

See my_actor/main.py for the crawl loop and [Apify Python SDK documentation](https://docs.apify.com/sdk/python) for Actor runtime details.

## Why it matters

A compact, inspectable starting point for learning how a browser-driven crawler can combine a request queue, bounded link discovery, and structured dataset output.

## License

See the repository license file if present.
