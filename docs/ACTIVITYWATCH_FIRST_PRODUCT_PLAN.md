# ActivityWatch-First Product Plan

## Product thesis

Claw Observer should first prove that an assistant can become meaningfully more useful using only ActivityWatch data.

Before adding screenshots, transcripts, or custom collectors, the MVP should answer one question:

**Can a Claw use app/window/AFK activity data to form useful observations, decide when to speak, and build better memory of the user over time?**

## Product boundaries for MVP

### In scope
- consume ActivityWatch local API
- ingest current window events
- ingest AFK events
- optionally ingest browser tab events later if available
- normalize into a common event schema
- maintain recent activity state
- generate summaries
- run trigger logic
- decide between silence, proactive message, or memory update
- use web research when the Claw encounters an unfamiliar app/topic/title

### Out of scope
- screenshots
- OCR
- audio transcription
- video progress semantics
- Discord deep metadata
- custom collectors
- direct screen reading

## Core user value

The user should feel:
- understood without being over-instrumented
- gently accompanied rather than surveilled
- occasionally surprised by meaningful observations
- helped by an assistant that notices patterns over time

## MVP system loop

1. Read new ActivityWatch events.
2. Normalize them.
3. Update current state.
4. Compare against recent history.
5. Decide whether the change is notable.
6. If notable, choose one action:
   - stay quiet
   - store a short-lived observation
   - update memory
   - proactively message the user
   - do a quick web lookup for context

## Internal modules

### 1. Ingestion
Responsibilities:
- connect to ActivityWatch API
- discover buckets
- poll events
- checkpoint the latest ingested event per bucket

### 2. Normalization
Responsibilities:
- map ActivityWatch event types into Claw Observer schema
- tag app/title based activity categories
- merge overlapping signals where necessary

### 3. State engine
Responsibilities:
- maintain current app/title/AFK status
- maintain recent timeline windows
- detect transitions and durations

### 4. Trigger engine
Responsibilities:
- decide if behavior is worth reacting to
- avoid noisy or repetitive prompts
- score candidate triggers

### 5. Memory policy engine
Responsibilities:
- identify repeated or meaningful patterns
- distinguish short-term context from long-term memory
- avoid storing raw noise

### 6. Claw interaction engine
Responsibilities:
- phrase useful messages
- choose whether to speak now or wait
- use web research when context is insufficient

## Initial trigger candidates

### Category A: context shift
Examples:
- app change
- return from AFK
- unusually rapid switching

### Category B: sustained focus
Examples:
- unusually long session in one app or topic
- repeated return to one project-related title

### Category C: deviation
Examples:
- hours spent in entertainment/social apps after planning work
- fragmented switching during intended focus time

### Category D: emerging interest
Examples:
- repeated appearance of a new tool, website, or topic
- consistent revisit of similar pages/titles over days

### Category E: unknown context
Examples:
- unfamiliar app name
- unfamiliar title patterns
- new workflow/software category

## Messaging policy for MVP

The assistant should not message on every event.

### Prefer silence when:
- the change is routine
- confidence is low
- the observation is too shallow to be useful
- a similar message was recently sent

### Prefer messaging when:
- there is a meaningful pattern
- the user made visible progress
- behavior strongly deviates from recent intention
- the assistant has a genuinely interesting question or observation

## Memory policy for MVP

### Short-term state
Store:
- what the user appears to be doing now
- what the user has focused on today
- notable deviations or sessions

### Long-term memory
Store only when repeated or meaningful, for example:
- recurring work patterns
- recurring distraction patterns
- stable tool preferences
- strong emerging interests

## Recommended implementation order

### Step 1
Build ActivityWatch reader + bucket discovery.

### Step 2
Normalize `currentwindow` and `afkstatus` events.

### Step 3
Build recent-state and transition detector.

### Step 4
Implement a small trigger engine with conservative messaging.

### Step 5
Add memory update rules.

### Step 6
Add optional web lookup for unknown tools/topics.

## Success criteria

The MVP is successful if, using only ActivityWatch data, the system can:
- reconstruct a rough timeline of activity
- identify a few repeated behavior patterns
- send occasional messages that feel relevant rather than creepy
- improve the assistant's contextual understanding over time

## Decision

For MVP, ActivityWatch should be used as an external dependency and data source, not as an embedded fork.
