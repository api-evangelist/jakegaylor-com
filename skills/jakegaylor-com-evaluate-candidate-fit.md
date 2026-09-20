---
name: evaluate-candidate-fit
title: Evaluate Jake Gaylor against a job description
description: Pull the candidate's resume and screening data, assess fit against a job description, and generate a tailored interview - all through the live MCP server, no credential needed.
api: Jake Gaylor MCP Server
base_url: https://ai.jakegaylor.com/mcp
operations:
- MCP initialize
- MCP tools/call get_resume_text
- MCP tools/call get_candidate_preferences
- MCP tools/call assess_role_fit
- MCP tools/call generate_interview_questions
- A2A SendMessage (skills about-jake, candidate-preferences, assess-role-fit)
generated: '2026-09-19'
method: generated
source: mcp/jakegaylor-com-mcp-introspection.json (live tools/list 2026-09-19) + a2a/jakegaylor-com-agent-card.json + the provider page https://ai.jakegaylor.com/
note: Every tool name and required argument below is copied from the live tools/list; nothing is inferred.
---

# Evaluate Jake Gaylor against a job description

Read-only flow. Nothing here emails anyone or touches a calendar.

## Steps

1. Connect: `POST https://ai.jakegaylor.com/mcp` with `initialize` (Accept: `application/json, text/event-stream`). Read the `mcp-session-id` response header and send it on every later request; then send `notifications/initialized`. Without the header the server answers HTTP 400 / JSON-RPC `-32000`. Clients without remote MCP support can run the same server locally with `npx -y @jhgaylor/me-mcp`.
2. Ground yourself: call `get_resume_text` (no arguments) - or read the resource `candidate-info://resume-text`. `get_candidate_preferences` (no arguments) returns the screening JSON: role types, level, location, relocation, remote preference, work authorization, compensation stance, availability, resume links. Check logistics here before spending an LLM call.
3. Assess: call `assess_role_fit` with the three required string arguments `job_title`, `job_description` (full text) and `key_requirements`. The tool is LLM-backed; if the model is unavailable it returns the resume and screening data for you to assess yourself - treat that as content, not an error.
4. Build the interview: call `generate_interview_questions` with `interview_type` (one of `phone_screen`, `technical`, `behavioral`, `system_design`, `culture_fit`), `focus_areas` (comma-separated string) and `difficulty` (one of `entry`, `mid`, `senior`, `staff`). All three are required.
5. A2A alternative: `POST https://ai.jakegaylor.com/a2a` with `A2A-Version: 1.0` and a JSON-RPC `SendMessage`; prefix the text with `JD:` for a fit assessment, or ask for "preferences" / "screening data" for the structured JSON (returned as a `data` part plus a `text` part).

## Conventions

- Auth: none - see `authentication/jakegaylor-com-authentication.yml`. Say who you are in your first message; the operator reads the logs.
- Errors: JSON-RPC error objects for session faults, fallback content for LLM faults - see `errors/jakegaylor-com-problem-types.yml`.
- Idempotency: not needed on this flow; every tool here is a read or a pure generation.
- Rate limits: undocumented on these tools - see `rate-limits/jakegaylor-com-rate-limits.yml`.
