# AI Timeline Dataset

An open, dated, source-linked record of what artificial intelligence actually
did between July 2025 and today. Every row is a single real-world event with a
primary source attached.

**2,658 events · 632 distinct publishers · 2025-07-01 to 2026-09-12**

Maintained by [Present of AI](https://presentofai.com), a daily AI news site.
Updated as the timeline grows.

## Why this exists

Most AI datasets are benchmarks or model outputs. This one is a record of
events: deployments, funding, regulation, incidents, launches, and failures,
each dated and each carrying the URL of the source it came from.

It is built for people who want to answer questions like *when did this
actually start*, *who moved first*, or *how much of this quarter was robotics
versus data centres*, without re-reading a year of headlines.

## Files

| File | Rows | Description |
|------|------|-------------|
| `data/ai-timeline.csv` | 2,658 | Full dataset, CSV with header |
| `data/ai-timeline.json` | 2,658 | Same data as a JSON array |

## Schema

| Column | Type | Description |
|--------|------|-------------|
| `id` | int | Stable identifier; also the permalink slug |
| `event_date` | date | The day the event happened, not the day it was reported |
| `realm` | enum | `virtual` (software, models, chips), `terrestrial` (robotics, physical deployment, energy), `space` |
| `title` | text | One-line description of the event |
| `summary` | text | Short factual summary |
| `actor` | text | Primary organisation or person involved |
| `ticker` | text | Stock ticker where the actor is listed, else empty |
| `materiality` | numeric | 0-1 significance estimate; higher means more consequential |
| `source_url` | url | The primary source the event was extracted from |
| `source_title` | text | Title of that source document |
| `publisher` | text | Publisher of the source |
| `permalink` | url | The event's page on presentofai.com |

### Realm distribution

| Realm | Events |
|-------|--------|
| terrestrial | 1,555 |
| virtual | 956 |
| space | 147 |

## Quick start

```python
import pandas as pd

df = pd.read_csv(
    "https://raw.githubusercontent.com/techa-ai/ai-timeline-dataset/main/data/ai-timeline.csv",
    parse_dates=["event_date"],
)

# The most consequential events of the last 90 days
recent = df[df.event_date > df.event_date.max() - pd.Timedelta(days=90)]
print(recent.nlargest(10, "materiality")[["event_date", "title", "actor"]])

# Monthly volume by realm
print(df.groupby([df.event_date.dt.to_period("M"), "realm"]).size().unstack(fill_value=0))
```

```bash
# No Python needed: how many events mention export controls
curl -sL https://raw.githubusercontent.com/techa-ai/ai-timeline-dataset/main/data/ai-timeline.csv \
  | grep -ci "export control"
```

## How the data is produced

Events are extracted from primary sources (company announcements, regulatory
filings, research publications, and reporting), then dated to the day the event
occurred. `materiality` is a model-assigned estimate of significance, useful
for ranking but not a ground truth; treat it as a sort key, not a measurement.

## Caveats

Read these before using the data in analysis.

- **Coverage is not complete.** This is a curated timeline, not a census of all
  AI events. Recall is strongest for major English-language announcements and
  weakest for non-English sources and smaller regional deployments.
- **`materiality` is model-assigned**, not human-rated or externally validated.
- **`event_date` is the event date, not the publication date.** These differ,
  sometimes by weeks, when an event surfaces through a later filing.
- **Source links may rot.** They were valid when recorded.
- **Summaries are model-written** from the linked source. For anything
  load-bearing, read the `source_url`.

## Licence

Data is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
Use it freely, including commercially, with attribution to
[Present of AI](https://presentofai.com).

Linked sources remain the property of their respective publishers; the
`source_url` column points to them and no source text is redistributed here
beyond short factual summaries.

## Citation

```bibtex
@misc{presentofai_ai_timeline,
  title  = {AI Timeline Dataset},
  author = {{Present of AI}},
  year   = {2026},
  url    = {https://github.com/techa-ai/ai-timeline-dataset},
  note   = {CC BY 4.0}
}
```

## Related

- [presentofai.com](https://presentofai.com) - the daily timeline this is drawn from
- [RSS feed](https://presentofai.com/feed.xml)
- [The Sunny Nights](https://presentofai.com/podcast) - nightly AI news show
