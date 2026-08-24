# RoadPulse — Road Intelligence Agent

**Agentic AI Hackathon '26 — Problem Statement 2**

RoadPulse is an AI-powered civic reporting tool that turns a citizen's photo and location into a classified, department-routed, trackable road-issue complaint — and automatically clusters duplicate reports of the same problem.

---

## What it does

1. **Citizen reporting** — A resident describes a road issue (pothole, waterlogging, broken traffic signal, accident debris, blocked road, etc.), optionally attaches a photo, and provides a location (typed manually or captured via GPS).
2. **AI triage** — An AI agent classifies the report, determining:
   - Issue type (e.g. Pothole, Waterlogging, Signal Failure)
   - The correct department to route it to (Traffic Police, Municipal Road Department, or Drainage Department)
   - Severity (High / Medium / Low)
   - A one-line official complaint log summary
   - A short alternate-route tip for commuters
3. **Duplicate detection** — Reports are matched against existing ones in the same ward using keyword overlap, so repeat complaints about the same issue get merged instead of creating noise.
4. **Public dashboard** — A live, ward-level tracker shows total reports, reports pending over 60 days, duplicates merged, and wards affected, plus a full table of all submitted issues with status pills (Pending / In Progress / Resolved).

## How the AI triage works

- Reports are sent to the Claude API (`claude-sonnet-4-6`) with a system prompt instructing it to return structured JSON (issue type, department, severity, summary, alternate route).
- **Offline fallback:** if the live model call fails (no network, CORS restrictions, offline demo environment), RoadPulse automatically falls back to a client-side, rule-based classifier (`classifyIssueLocally`) that pattern-matches keywords like "pothole," "waterlog," "signal," or "accident" to produce the same structured result. The agent panel indicates whether a result came from the **live model** or **offline mode**.

## Duplicate clustering logic

- Each report's location is mapped to a ward (either extracted directly if "Ward ##" appears in the text, or derived deterministically from a hash of the location string).
- New reports are compared against existing reports in the same ward, checking for overlapping issue keywords (pothole, waterlog, signal, accident, block, crack, flood, debris, cave-in).
- On a match, the new report is marked as a duplicate and inherits the original report's status.

## Tech stack

- Single-file HTML/CSS/JS — no build step, no dependencies
- Fonts: Barlow Condensed, Inter, JetBrains Mono (Google Fonts)
- Browser Geolocation API for "Use GPS"
- Anthropic Messages API (`/v1/messages`) for live AI triage, with a fully offline rule-based fallback

## Running it

Just open the HTML file in a browser. No server or build process is required — all state (reports, dashboard stats) lives in memory for the session.

> Note: the AI-powered triage requires network access to `api.anthropic.com`. In restricted or offline environments, RoadPulse transparently falls back to local rule-based classification so the demo still works end-to-end.

## File structure

This is a single self-contained HTML file:

- `<style>` — all styling (asphalt/yellow civic-infrastructure theme)
- `<header class="hero">` — landing hero section
- `Step 01` — citizen report form + live agent result panel
- `Step 02` — public ward-level dashboard
- `<script>` — form handling, AI triage call, offline fallback, duplicate detection, and dashboard rendering

---

*Built for Reshma K, Product Space × Code Benders.*
