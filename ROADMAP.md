# Roadmap

## Phase 0: Product clarity and documentation
- unify product thesis around ActivityWatch-first MVP
- finalize architecture layers
- finalize Claw integration contract
- finalize event schema
- finalize plugin direction as future-facing, not MVP-blocking

## Phase 1: ActivityWatch ingestion MVP
- connect to local ActivityWatch API
- discover buckets
- ingest `currentwindow` events
- ingest `afkstatus` events
- maintain checkpoints per bucket
- normalize raw events into Claw Observer schema

## Phase 2: State and trigger loop
- maintain current state and recent timeline
- detect transitions, sustained focus, and context switching
- implement conservative trigger scoring
- decide between silence, message, and memory update

## Phase 3: Claw integration and memory policy
- define Claw-facing summaries
- define short-term vs long-term memory rules
- define web-lookup behavior for unknown tools/topics
- make the system usable by an external Claw through docs/API

## Phase 4: Browser enrichment
- validate `aw-watcher-web`
- add browser title/URL/domain support
- refine category inference and topic tracking

## Phase 5: Plugin platform and deeper collectors
- plugin manifest loader
- enable/disable states
- recommendation flow for specialized collectors
- future collectors such as YouTube, Discord, project attribution
