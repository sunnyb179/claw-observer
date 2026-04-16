# ActivityWatch API Research

## Summary

For the MVP, ActivityWatch should be treated as a local event source accessed over its localhost REST API.

Claw Observer should read, normalize, and react to ActivityWatch buckets rather than embedding or forking ActivityWatch.

## Observed local instance

Local server base URL:
- `http://127.0.0.1:5600`

Buckets currently present on this machine:
- `aw-watcher-window_RikkaLuvAurora`
- `aw-watcher-afk_RikkaLuvAurora`

This confirms the current system is already producing:
- active window/app events
- AFK status events

There is currently no browser bucket visible, which suggests the browser extension watcher is not enabled yet.

## Key REST endpoints

From the docs and local inspection, the most important endpoints for MVP are:

### List buckets
- `GET /api/0/buckets/`

Returns bucket metadata keyed by bucket id.

Useful fields observed:
- `id`
- `created`
- `type`
- `client`
- `hostname`
- `last_updated`

### Read events for a bucket
- `GET /api/0/buckets/<bucket_id>/events`

This is the main endpoint Claw Observer should consume.

Observed event shape:

```json
{
  "id": 47,
  "timestamp": "2026-04-16T01:20:44.743000+00:00",
  "duration": 71.147,
  "data": {
    "app": "chrome.exe",
    "title": "Telegram Web - Google Chrome"
  }
}
```

### Create/read buckets
- `GET /api/0/buckets/<bucket_id>`
- `POST /api/0/buckets/<bucket_id>`

Important mainly if we later decide to write our own watcher or export bucket.

### Heartbeat endpoint
- `POST /api/0/buckets/<bucket_id>/heartbeat`

Important for custom watchers later, since ActivityWatch uses heartbeats to merge adjacent identical events within a `pulsetime` window.

### Query API
The docs confirm a query API exists, but the MVP does not need to depend on it immediately.
We can start by reading raw bucket events directly.

## Relevant ActivityWatch data model

### Buckets
Buckets group events from the same source.
Recommended pattern is one bucket per watcher per host.

Examples from docs:
- `currentwindow`
- `afkstatus`
- `web.tab.current`
- editor activity buckets

### Events
Core event structure:

```json
{
  "timestamp": "ISO8601 UTC",
  "duration": 3.14,
  "data": {"key": "value"}
}
```

### Current useful event types

#### `currentwindow`
Data fields:
- `app`
- `title`

#### `afkstatus`
Data fields:
- `status` (`afk` or `not-afk`)

#### `web.tab.current` (not yet observed locally)
Docs indicate fields:
- `url`
- `title`
- `audible`
- `incognito`

This will become important once `aw-watcher-web` is enabled.

## What we can build immediately

With only the currently visible buckets, Claw Observer can already:
- detect app switches
- infer rough task categories from app and title
- detect long focus windows
- detect rapid context switching
- detect returns from AFK
- produce simple timeline summaries
- decide whether some behavior is worth messaging about

## What ActivityWatch does not yet give us in the current setup

Not currently available from observed buckets:
- exact browser URL data
- video progress/completion
- subtitles/comments semantics
- Discord voice participants
- project/file-level attribution

These should remain out of MVP scope for now.

## MVP integration recommendation

### Recommended read path
1. Poll `GET /api/0/buckets/`
2. Discover relevant bucket ids
3. Poll `GET /api/0/buckets/<bucket_id>/events`
4. Store cursor/checkpoint per bucket
5. Normalize new events into Claw Observer schema
6. Run trigger logic on newly ingested events

### Recommended first buckets
- `currentwindow`
- `afkstatus`
- later `web.tab.current`

## Why this is enough for MVP

The product goal is not perfect surveillance.
The product goal is useful interpretation.

ActivityWatch already gives enough signal to build:
- a current activity state
- a lightweight timeline
- basic proactive coaching triggers
- selective memory updates based on repeated patterns

That is enough to validate the product before adding deeper collectors.
