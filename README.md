# LinkedIn Company Page Mapper MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-linkedin-company-presence-mapper)](https://smithery.ai/servers/mambabuilt/mcp-linkedin-company-presence-mapper) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-linkedin-company-presence-mapper/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-linkedin-company-presence-mapper) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-linkedin-company-presence-mapper)](https://www.npmjs.com/package/@mambalabsdev/mcp-linkedin-company-presence-mapper) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-linkedin-company-presence-mapper)](https://www.npmjs.com/package/@mambalabsdev/mcp-linkedin-company-presence-mapper) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-linkedin-company-presence-mapper)](https://github.com/mambalabsdev/mcp-linkedin-company-presence-mapper/blob/main/LICENSE)

<a href="https://mcprush.com/to/linkedin-company-presence-mapper-mcp?ref=badge"><img width="380" height="200" src="https://mcprush.com/badge/linkedin-company-presence-mapper-mcp/card.svg" alt="LinkedIn Company Presence Mapper MCP server" /></a>

MCP server for the Mamba Labs **LinkedIn Company Page Mapper** actor on Apify.

Free. Resolve a company domain to its LinkedIn page, verified against the company's own site, with its name and tagline, and a follower count on some companies. No firmographics. No request is sent to LinkedIn.

## What it does

Free to run apart from Apify's start fee. Resolve a company domain to its LinkedIn company page, verified against the company's own site, and return the page name and tagline, plus a follower count when public search results show one (2 of 6 test domains on 2026-10-12). No request is sent to LinkedIn: the page comes from the company homepage (structured data or a link) or from a search result whose name matches the name the company site declares or the company_name you pass. A page that cannot be verified is not returned; the row says why in linkedin_verification. The follower count is the search engine's copy and can lag the live page. Industry, size band, headquarters, founded year, specialties and website are no longer returned. Read only; requires an APIFY_TOKEN and consumes Apify credits per call.

### What changed in 1.2.1

The actor is free: its three paid events were removed on 2026-10-12. The text now states the measured rates: a verified page on most companies, a follower count on some, no firmographics.

### What changed in 1.2.0

The actor stopped fetching linkedin.com on 2026-10-12. These fields came only from the LinkedIn page and are gone: `linkedin_followers_exact`, `linkedin_industry`, `linkedin_company_size`, `linkedin_headquarters`, `linkedin_founded_year`, `linkedin_specialties`, `linkedin_website`. The `includeFirmographics` input is gone; `minConfidenceScore` is new. New output fields: `linkedin_confidence_score`, `linkedin_verification`.

## Quick start

Add this to your MCP client configuration:

```json
{
  "mcpServers": {
    "mamba-linkedin-company-presence-mapper": {
      "command": "npx",
      "args": ["-y", "@mambalabsdev/mcp-linkedin-company-presence-mapper"],
      "env": { "APIFY_TOKEN": "your-apify-token" }
    }
  }
}
```

## Prerequisites

- Node.js 18 or newer
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations)

The actor is free. Since 2026-10-12 it declares no paid event, so the only charge is Apify's platform start event, `apify-actor-start`, at $0.00005 per GB of run memory. This server starts the run at 512 MB, the actor's own default, so a run costs $0.00005 whatever the number of companies. The [actor page](https://apify.com/mambalabs/linkedin-company-presence-mapper) carries the current pricing.

What it returns, measured 2026-10-12 on 6 domains: a verified page URL, name and tagline on 5, a follower count on 2, firmographics on none.

## Example prompts

- "What is the LinkedIn company page for gitlab.com, and what does its tagline say?"
- "Find the LinkedIn company page for stripe.com and tell me how it was verified."
- "Find the LinkedIn pages for notion.com, figma.com and gitlab.com, with follower counts where they are available."

## Tool and inputs

Tool: `map_linkedin_company_presence`

| Input | Type | Meaning |
|---|---|---|
| `company_domain` | string | Bare company domain, for example shopify.com. Supply this or a handle. |
| `company_name` | string | Optional. A page whose name is identical to it verifies (score 80). |
| `handle` | string | Optional. The company slug from linkedin.com/company/<slug>. Skips discovery; returned as given. |
| `includeFollowerCounts` | boolean | Default true. Reads the follower count, page name and description from the search result. False returns the URL only. |
| `skipCache` | boolean | Default false. True forces a fresh lookup. |
| `minConfidenceScore` | integer | Default 70. A page scoring under it is not returned. |

## Reading the output

Every row carries a per platform `_status` field, and it is the field to read
first. The vocabulary is the same across the whole Mamba Labs social family:

| Status | Meaning |
|---|---|
| `ok` | fetched and parsed, the value is there |
| `not_found` | no page verified; `linkedin_verification` says what was seen and why |
| `not_extractable` | the page is verified and the search result carries no follower count |
| `blocked` | the search engine refused the request, worth retrying later |
| `skipped` | you did not ask for this platform |

**`false` and `null` are never interchangeable.** `false` means we looked and the
answer is no. `null` means we could not look. If you filter for companies with no
presence, filter on `false`, because `null` rows are unknown rather than absent.

## How each call runs

Each call starts the actor run, polls it until it finishes, then reads the dataset. The run is allowed 1,800 seconds at 512 MB. If the run is still going when this call stops waiting, the call returns the run id and a console link instead of a timeout, so the result is never lost.

## Full actor documentation

[apify.com/mambalabs/linkedin-company-presence-mapper](https://apify.com/mambalabs/linkedin-company-presence-mapper)

## Mamba Labs GTM Suite

Mamba Labs builds a fleet of GTM enrichment actors that share one flat, Clay
ready output convention, so their rows join on `company_domain` with no cleaning
step. Full fleet: [apify.com/mambalabs](https://apify.com/mambalabs)

## License

MIT
