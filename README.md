# LinkedIn Company Page Mapper MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-linkedin-company-presence-mapper)](https://smithery.ai/servers/mambabuilt/mcp-linkedin-company-presence-mapper) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-linkedin-company-presence-mapper/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-linkedin-company-presence-mapper) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-linkedin-company-presence-mapper)](https://www.npmjs.com/package/@mambalabsdev/mcp-linkedin-company-presence-mapper) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-linkedin-company-presence-mapper)](https://www.npmjs.com/package/@mambalabsdev/mcp-linkedin-company-presence-mapper) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-linkedin-company-presence-mapper)](https://github.com/mambalabsdev/mcp-linkedin-company-presence-mapper/blob/main/LICENSE)

MCP server for the Mamba Labs **LinkedIn Company Page Mapper** actor on Apify.

Resolve a company domain to its LinkedIn page with the exact follower count and public firmographics.

## What it does

Resolve a company domain to its LinkedIn company page and return the EXACT follower count, plus the industry, declared company size band, headquarters, founded year and specialties that LinkedIn publishes on the public page. LinkedIn renders every digit, so unlike most social platforms these counts can be summed across a list. Everything comes from the logged out page: no login, no session cookie, no vendor. Employee lists, employee growth and post engagement are NOT reachable logged out and are not returned. A guessed slug that resolves to a different company is reported as identity_mismatch. Read only; requires an APIFY_TOKEN and consumes Apify credits per call.

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

The actor is pay per event and consumes Apify credits per call. Prices from the live pricing record, read 2026-10-05:

| Event | Price per event (USD) | Fires when |
| --- | --- | --- |
| `apify-actor-start` | $0.00005 | Charged when the Actor starts running. Number of events charged depends on Actor memory (one event per GB, minimum one event). |
| `company-checked` | $0.004 on the Free plan, down to $0.0028 on higher Apify plans | Once per company for which the discovery cascade completed and a non degraded row was produced, whether or not a LinkedIn profile was found. Does not fire on a degraded row, because on a degraded row no discovery was performed. |
| `profile-resolved` | $0.003 on the Free plan, down to $0.0021 on higher Apify plans | Once per company whose candidate LinkedIn URL passed the identity gate. Fires on the validation work, not on a populated count. A candidate dropped as an impersonator does not charge: the work was done and the honest answer is that there is no such profile. |
| `follower-count-extracted` | $0.0025 on the Free plan, down to $0.00175 on higher Apify plans | Once per company where a numeric follower count was read off the public page. Does not fire on not_extractable, blocked, identity_mismatch or url only runs. |

This server starts the run at 512 MB, the actor's own default, so a run charges one `apify-actor-start` event. The [actor page](https://apify.com/mambalabs/linkedin-company-presence-mapper) carries the current prices.

## Example prompts

- "How many LinkedIn followers does gitlab.com have?"
- "Get the LinkedIn industry, size band and headquarters for stripe.com."
- "Rank these domains by LinkedIn follower count: notion.com, figma.com, gitlab.com."

## Tool and inputs

Tool: `map_linkedin_company_presence`

| Input | Type | Meaning |
|---|---|---|
| `company_domain` | string | Bare company domain, for example shopify.com. Supply this or a handle. With a domain the actor runs full discovery; with a handle it skips straight to |
| `company_name` | string | Optional. Improves search accuracy and is what the identity gate checks a discovered profile against, so supplying it reduces wrong matches. |
| `handle` | string | Optional. The company slug from linkedin.com/company/<slug>, for example shopify. Supplying it skips discovery and goes straight to the fetch. |
| `includeFollowerCounts` | boolean | When "true" (default) the profile page is fetched and the counts are extracted. Set "false" to resolve the profile URL only, which is cheaper and need |
| `skipCache` | boolean | When "false" (default) a successful lookup is cached for seven days and reused. Set "true" to force a fresh fetch. Sent as a string for Clay compatibi |
| `includeFirmographics` | boolean | When "true" (default) industry, company size band, headquarters, founded year and website are parsed off the page alongside the follower count. Set "f |

## Reading the output

Every row carries a per platform `_status` field, and it is the field to read
first. The vocabulary is the same across the whole Mamba Labs social family:

| Status | Meaning |
|---|---|
| `ok` | fetched and parsed, the value is there |
| `not_found` | we looked and there is no such profile |
| `not_extractable` | the profile exists and the value is not on the wire to us |
| `blocked` | the platform refused us, worth retrying later |
| `identity_mismatch` | we found a real profile and it belongs to someone else |
| `skipped` | you did not ask for this platform |

**`false` and `null` are never interchangeable.** `false` means we looked and the
answer is no. `null` means we could not look. If you filter for companies with no
presence, filter on `false`, because `null` rows are unknown rather than absent.

## How each call runs

Each call starts the actor run, polls it until it finishes, then reads the dataset. The run is allowed 300 seconds at 512 MB, as before. If the run is still going when this call stops waiting, the call returns the run id and a console link instead of a timeout, so the result is never lost.

## Full actor documentation

[apify.com/mambalabs/linkedin-company-presence-mapper](https://apify.com/mambalabs/linkedin-company-presence-mapper)

## Mamba Labs GTM Suite

Mamba Labs builds a fleet of GTM enrichment actors that share one flat, Clay
ready output convention, so their rows join on `company_domain` with no cleaning
step. Full fleet: [apify.com/mambalabs](https://apify.com/mambalabs)

## License

MIT
