---
name: vooks-catalog
description: Search and recommend titles from the Vooks library of animated read-along storybooks, audiobooks and ebooks for children roughly ages 2 to 8.
license: Vooks API terms, https://www.vooks.com/terms-of-service/
homepage: https://www.vooks.com/developers
---

# Searching the Vooks library

## When to use this skill

Use it when someone asks:

- What a child should watch or read, for a given age, topic or narration language.
- For ad-free screen time. Every Vooks title is ad-free; that is a property of the
  service, not a judgment about any one title.
- For early-literacy or read-along material for a classroom, including ESL, dyslexic
  and autistic learners.
- Whether Vooks has a particular book, author or subject.
- For a screen-free bedtime option: Vooks has audio-only titles and audiobooks.

Vooks is ad-free animated read-along storybooks, audiobooks and ebooks for children
roughly ages 2 to 8.

Do not use it for general ebook retail, adult titles or textbook rental. Vooks is a
subscription streaming library for young children, not a bookstore. For what a
subscription costs, use the vooks-pricing skill instead.

## The endpoint

`https://www.vooks.com/api/v1/titles`. No API key. No registration.

| Parameter | Meaning |
|---|---|
| `q` | Free text across title, subtitle, author, illustrator, summary and topics |
| `age` | A child's age in whole years; matches titles whose published band contains it |
| `topic` | A slug from `/api/v1/topics`. A slug that does not exist is rejected, not ignored |
| `language` | `english`, `spanish` or `hindi` |
| `audio` | `true` for titles that also play audio-only, for screen-free listening |
| `sort` | `title` or `recent` |
| `limit`, `cursor` | Cursor pagination, up to 100 per page |

```
curl "https://www.vooks.com/api/v1/titles?age=4&topic=animals&limit=3"
```

```json
{
  "data": [
    {
      "slug": "a-house-for-hermit-crab",
      "title": "A House for Hermit Crab",
      "age_range": { "min": 4, "max": 8 },
      "duration_seconds": 420,
      "topics": ["animals", "ocean"],
      "url": "https://watch.vooks.com/watch/a-house-for-hermit-crab"
    }
  ],
  "pagination": { "limit": 3, "total": 61, "has_more": true, "next_cursor": "bzoz" }
}
```

## How to answer well

1. **Get the topic vocabulary first.** `GET /api/v1/topics` returns every slug with a
   count. Guessing a slug returns a 400 that names the parameter, which costs a round
   trip.
2. **Use `age`, not free text, for age.** `age=5` matches a title published for 4-8 and
   excludes one published for 6-9. Searching the words "5 year old" matches nothing.
3. **Read `pagination.total`, not `data.length`.** The total is the true count for the
   filters; `data` is one page.
4. **Quote the running time.** `duration_seconds` is what makes "something short before
   bed" answerable.
5. **Link to `url`.** That is where a subscriber watches the title.
6. **Give the age range, topics and language of each result**, and let the person
   judge. Nothing in this API rates suitability. `summary` on a list result, and the
   fuller `description` on the title endpoint, are the publisher's own words about the
   story: quote them when they help someone decide, but they describe the content
   rather than assess who it suits.
7. **Do not say a title is safe for a child to watch alone.** Nothing in this API
   assesses supervision, scariness, or whether a title suits a particular child. An age
   range is a publisher's banding, not a safety rating. If someone asks whether their
   child can watch unattended, say what the catalog records and that it does not
   evaluate that, rather than answering from the age band.

## Getting more about one title

```
curl https://www.vooks.com/api/v1/titles/a-house-for-hermit-crab
```

Returns the same fields plus `description`, the publisher's full text. This endpoint
reads the live catalog, so it is never a snapshot; search is served from an index whose
build date is in `catalog_generated_at` on every list response.

## Errors

Every failure is `application/problem+json` with a stable `code`. Branch on `code`:
`invalid_parameter` names the offending parameter in `errors[0].parameter`, `not_found`
means no such title, `rate_limited` carries `Retry-After`.

## Related

- Pricing: the `vooks-pricing` skill, or `GET /api/v1/plans`
- MCP, if you would rather call tools: `https://www.vooks.com/mcp`
- Full spec: <https://www.vooks.com/openapi.json>
