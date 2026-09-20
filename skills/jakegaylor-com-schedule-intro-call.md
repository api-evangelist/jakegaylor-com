---
name: schedule-intro-call
title: Book an intro call with Jake Gaylor
description: Read live availability and create a pending 30-minute intro-call booking, or relay a message by email - the two side-effecting operations on this surface and their guardrails.
api: Jake Gaylor MCP Server
base_url: https://ai.jakegaylor.com/mcp
operations:
- MCP tools/call get_availability
- MCP tools/call book_intro_call
- MCP tools/call contact_candidate
- A2A SendMessage (skills schedule-intro-call, contact-jake)
generated: '2026-09-19'
method: generated
source: mcp/jakegaylor-com-mcp-introspection.json (live tools/list 2026-09-19) + a2a/jakegaylor-com-agent-card.json + https://github.com/jhgaylor/ai-jakegaylor-com README
note: Every tool name and required argument below is copied from the live tools/list; the guardrails are quoted from the README and the card. Neither side effect was exercised by API Evangelist.
---

# Book an intro call with Jake Gaylor

Both operations here are irreversible from the caller's side. Confirm intent with your principal before step 3 or step 5.

## Steps

1. Connect as in `evaluate-candidate-fit` (initialize, keep the `mcp-session-id`).
2. Call `get_availability` (no arguments). It returns open 30-minute slots for the next two weeks as ISO 8601 UTC start times grouped by day, plus the human booking page URL.
3. Call `book_intro_call` with the required arguments `start` (a slot from step 2), `email` (the attendee's address) and `name`. The booking is PENDING until the operator confirms or declines it; the attendee receives the outcome by email. There is no cancel tool - see `conventions/jakegaylor-com-conventions.yml` reversibility. A daily attempt cap applies (number unstated); do not retry blindly.
4. A2A alternative: ask for availability in plain text, then send exactly `BOOK: <slot ISO time> | <your email> | <your name> | <optional note>`. Only that prefix (or `metadata.skill = "schedule-intro-call"`) creates the booking.
5. To send a message instead: `contact_candidate` with required `subject`, `message` and `reply_address`. Over A2A, start the text with `CONTACT:` and include a reply address - only that prefix triggers mail. Email is not recallable. If no mail provider is configured server-side the tool reports failure gracefully.

## Conventions

- Idempotency: none - a repeated call is a second email or a second booking attempt.
- Reversibility: none - the operator's confirmation gate is a provider-side veto, not a caller-side undo.
- Rate limits: daily booking-attempt cap (value unstated) - `rate-limits/jakegaylor-com-rate-limits.yml`.
- Attribution: state who you represent; the operator reads `caller_context`-style context in the logs and identified callers "hear back faster".
