# Data Cleaning Rules

## Purpose

Transform raw search results into structured data suitable for report generation.

## Exclusion Rules

Discard results matching these URL patterns:

```
google.com/search
bing.com/search
youtube.com
twitter.com / x.com
facebook.com
reddit.com
linkedin.com/company
g2.com
capterra.com
trustpilot.com
```

## Structured Data Extraction

### Product Information

From each valid search result, extract:

| Field | Source | Rule |
|-------|--------|------|
| `name` | Page title | First segment before `-`, `|`, `–` separators |
| `vendor` | Domain or content | Company name from domain or "by {Vendor}" pattern |
| `website` | Result URL | Must be a valid HTTP URL |
| `description` | Content snippet | First 200 characters of relevant content |
| `features` | Content | Named capabilities mentioned in the text |

### Verification Status

- `verified`: Found official website with clear product information
- `partial`: Found product mention but limited official information
- `unverified`: Only found third-party mentions

### Market Insights

Extract qualitative statements about:
- Market trends and growth
- Technology directions
- User preferences
- Competitive dynamics

Mark each insight with its source URL.

## Deduplication

1. **By URL**: Keep only one result per URL, prefer the one with more content
2. **By product name**: Merge entries for the same product (case-insensitive match)
3. **By content**: Discard near-duplicate snippets (>80% similarity)

## Output Format

```json
{
  "products": [
    {
      "name": "string",
      "vendor": "string",
      "website": "string",
      "description": "string",
      "features": ["string"],
      "verification_status": "verified | partial | unverified",
      "sources": [{"url": "string", "title": "string"}]
    }
  ],
  "insights": [
    {
      "category": "string",
      "content": "string",
      "source_url": "string"
    }
  ],
  "excluded": [
    {
      "name": "string",
      "reason": "string"
    }
  ]
}
```
