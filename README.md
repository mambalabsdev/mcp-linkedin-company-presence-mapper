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

The actor is pay per event and consumes Apify credits per call. Pricing is on the
[actor page](https://apify.com/mambalabs/linkedin-company-presence-mapper).

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

## Full actor documentation

[apify.com/mambalabs/linkedin-company-presence-mapper](https://apify.com/mambalabs/linkedin-company-presence-mapper)

## Mamba Labs GTM Suite

Mamba Labs builds a fleet of GTM enrichment actors that share one flat, Clay
ready output convention, so their rows join on `company_domain` with no cleaning
step. Full fleet: [apify.com/mambalabs](https://apify.com/mambalabs)

## License

MIT
