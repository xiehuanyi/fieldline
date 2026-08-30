# AI usage disclosure — FieldLine

Honest log of which AI tools built what (workspace rule; Devpost
disclosure norm).

## Tools

- **Claude (Anthropic) via an agentic coding CLI** — research, design,
  and code generation for the entire project, directed and reviewed by
  the author (Huanyi Xie).

## What AI did (2026-08-31 session)

- Researched the CALL-E platform: Devpost rules pages, the
  `CALLE-AI/call-e-integrations` repo, the official docs/OpenAPI spec,
  the `calle-ai` PyPI package (installed and read its actual source to
  mirror the SDK interface faithfully), and the
  `CALLE-AI/awesome-phone-call-agents` contribution format (including
  the 28 existing Python apps, to avoid overlap).
- Ideated candidates and scored them (see docs/idea-brief.md);
  the author's domain (KAUST fieldwork) chose FieldLine.
- Wrote all application code (`src/fieldline/`), tests (32), example
  plan, and scripts; ran the demo end-to-end and the test suite.
- Wrote the demo-mode call transcripts. **These are hand-written
  simulation data, not real calls** — marked `// DEMO` in source, with
  fictional +1-555-01xx numbers.
- Drafted all docs: README, idea brief, plan, this file, submission
  copy, video script, submit checklist.
- Produced the demo video (`docs/video.mp4`) and gallery screenshots
  (`docs/img/`): captured real CLI output, replayed it in a styled
  terminal page, recorded headless, and assembled with ffmpeg. The
  voiceover is **synthetic TTS** (Microsoft Edge neural TTS, voice
  en-US-AndrewNeural) reading the narration in
  `docs/video-narration.md` — it is not the author's voice. All calls
  shown are demo-mode simulations, labeled as such on screen and in
  the narration.

## What AI did NOT do

- No real phone calls were placed (no CALL-E account existed during the
  build; live mode is implemented against the real SDK but unverified
  end-to-end until the author runs `fieldline checkin-now`).
- No git operations, deployment, or submissions — all performed by the
  author.

## Simulated vs real (for judges who ask)

- **Real:** the protocol state machine, schemas, prompts, dispatcher
  retry/fail-soft logic, tests, rendering, incident briefs; the live
  code path calls the official `calle-ai` SDK
  (`client.calls.create_and_wait`) unchanged.
- **Simulated in demo mode:** the telephone conversations themselves
  (scripted transcripts shaped per CALL-E's OpenAPI `call_task` schema,
  enforced by shape-conformance tests).
