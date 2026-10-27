# Kuaishou Video Metadata Lookup Demo

A focused integration example for developers who already have one JSON video object from a Kuaishou search result and need a stable metadata record for indexing, monitoring, analytics, or an automated data pipeline.

## Product

Run the public [Kuaishou Video Metadata Lookup Actor](https://apify.com/gougousongsong/kuaishou-video-detail). It accepts **one** `video` object and emits **one** Dataset item. It does not download video files, perform OCR, run sentiment analysis, or provide an independent URL lookup endpoint.

## Input

```json
{
  "video": {
    "video_id": "5258515723732898742",
    "caption": "sample",
    "view_count": 12,
    "like_count": 3,
    "comment_count": 1,
    "share_count": 0,
    "collect_count": 0
  }
}
```

The `video` object should come from a public Kuaishou search-result workflow. Required input is the object itself; fields may vary by source, but identifiers and engagement fields should be supplied when available.

## Observed output

A completed run produced one normalized Dataset item:

```json
{
  "video_id": "5258515723732898742",
  "caption": "sample",
  "user_id": "",
  "user_name": "",
  "kwai_id": "",
  "view_count": 12,
  "like_count": 3,
  "comment_count": 1,
  "share_count": 0,
  "collect_count": 0,
  "published_timestamp": 0,
  "duration_ms": 0,
  "item_type": "",
  "source": "kuaishou-search-collector",
  "normalized_at": "2026-09-07T17:37:52Z"
}
```

## Calling repeatedly for a batch

The current Actor accepts one video per run. A client can call the API repeatedly for a batch, but each run has a start charge:

```python
from apify_client import ApifyClient

client = ApifyClient("APIFY_TOKEN")
for video in videos:
    run = client.actor("gougousongsong/kuaishou-video-detail").call(
        run_input={"video": video},
        wait_secs=300,
    )
    print(run["defaultDatasetId"])
```

Do not put an array in `video`; that is not the current input contract. For large batches, contact the developer before running many individual jobs.

## Current pricing

The Actor uses Pay-per-event pricing:

- `$0.01` per Actor start event, charged once per run at the configured 1 GB memory setting.
- `$0.002` per output Dataset item.
- A one-item run is therefore `$0.012` in event charges before any applicable platform usage charges.
- `1,000` results in one run would be `$2.01` in event charges, not `$2.00`; the Store headline reflects the result event only.
- Apify platform usage costs are separate and are deducted from creator payout under the monetization rules.

## Error handling

Check the run status before reading the Dataset. A failed run may have no output item and can still consume platform resources. Validate that `video` is an object, not an array, and provide source fields when available. The public API returns normal HTTP error responses for invalid authentication or malformed requests; inspect the response body before retrying.

The Actor's public OpenAPI definition is available from the product page. Use synthetic or public data only and follow Kuaishou's terms and applicable law.

## Links

- [Apify product page](https://apify.com/gougousongsong/kuaishou-video-detail)
- [OpenAPI definition](https://api.apify.com/v2/acts/n51JlABA8MvFUcyfh/builds/ClyX0iyaCxNBCeVCl/openapi.json)
- [Apify monetization documentation](https://docs.apify.com/actors/monetize/pay-per-event)
