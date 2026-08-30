# Devpost submission draft — FieldLine

**Event:** CALL-E: Your Code Is Calling (https://call-e.devpost.com/)
**Prize targeted:** Most Practical Use Case ($4,000); duress protocol also
argues Most Innovative.

## Form fields (fill on Devpost)

| Field | Value |
|---|---|
| Project name | FieldLine |
| Elevator pitch | A lone-worker safety net that phones the field: scheduled check-in calls, silent duress detection, and an automatic escalation call cascade. |
| PR URL (required) | `<AWESOME_PR_URL>` — see docs/submit-checklist.md |
| Video URL (required) | `<YOUTUBE_URL>` (public, <3 min) |
| CALL-E account email (required) | `<CALLE_ACCOUNT_EMAIL>` |
| Demo URL (optional) | leave blank (local CLI) or `https://github.com/xiehuanyi/fieldline` |
| Built with (tags) | python, uv, calle-ai, call-e, rich, pyyaml, pytest |

---

## Inspiration

I'm a researcher at KAUST on the Red Sea coast. When colleagues go out —
reef stations by boat, desert sites by truck — the "safety system" is a
filed PDF and a promise that someone will notice if they don't come
back. At 18:05 on a Friday, nobody is watching that clock. A phone-call
agent is the first technology that can actually *work* a safety plan:
not send a notification into a muted group chat, but ring a human,
ask the right questions, and climb a ladder of humans until one of them
says "I'm taking over."

## What it does

FieldLine turns a YAML trip plan into an active safety protocol. At each
scheduled check-in, CALL-E calls the worker and brings back a structured
result: safe / needs assistance / location / plan changes. Miss a
check-in and FieldLine retries, declares you OVERDUE, then walks the
escalation ladder call by call — briefing each contact with accumulated
facts (last confirmed contact, vehicle position, what the previous
contact reported) until someone explicitly assumes coordination. Every
incident ends in a written brief: timeline, transcripts, evidence,
recommended actions.

The wow: a pre-agreed **duress phrase**. Say it mid-call and the agent
does not react — it ends the call normally, then silently escalates
straight to the safety officer with instructions *not* to call your
phone back. FieldLine also never auto-dials emergency services; the last
rung is always a human who does.

## How we built it

Every conversation is one CALL-E call task: `POST /v1/calls` via the
official `calle-ai` Python SDK, with a natural-language `task` and a
JSON `result_schema`. CALL-E plans the call, dials, talks, and returns
`structured_result`, `task_completed`, `completion_confidence`, and
`evidence` — FieldLine's decisions consume only those fields. The
silent-duress protocol is pure prompt + schema: the task instructs the
agent to never react to the phrase, and `duress_phrase_detected:
boolean` rides home in the structured result.

Above the SDK sits a deliberately deterministic core: `protocol.py` is a
unit-tested state machine (32 tests) — no LLM in the safety loop, so
escalation decisions are reproducible. The dispatcher wraps every SDK
call with timeout + one retry made double-dial-proof by idempotency
keys, and fails *soft*: if the telephony API itself is unreachable, the
synthetic failure is classified as "could not reach" and the cascade
keeps climbing instead of crashing — an outage degrades into exactly the
behavior a safety system should have. A demo transport replays scripted
calls shaped byte-for-byte like the OpenAPI `call_task` schema, so the
entire product runs offline with zero keys and going live is a
transport swap.

## Challenges

Designing duress handling was the hard part: the one thing the agent
must not do is acknowledge the phrase, so detection had to live entirely
in the extraction schema rather than in the conversation. The second
challenge was failure semantics — for a booking bot a dead API is an
error message; for a safety net it must *be information* ("the worker
could not be reached"), which forced the fail-soft synthetic-result
design. And keeping the demo honest took discipline: the simulated
transport is validated by shape-conformance tests against the real
schema, and every fake is marked `// DEMO` in source.

## Accomplishments

A complete safety protocol — schedule, retries, grace windows, silent
duress, stand-down rules, ladder exhaustion — as a solo build inside the
window, with 32 passing tests, a consent gate before any live call,
cancellation honored between calls, masked phone numbers everywhere, and
an incident brief a security office could actually act on.

## What we learned

An agent that *acts* in the physical world needs its judgment split in
two: let the platform be smart on the call, and keep the policy around
it boring, deterministic, and testable. Also: for safety software,
"what happens when our own vendor is down" is a feature, not an edge
case.

## What's next

Publish the check-in as a CALL-E Goal so organizations can reuse it;
webhook-driven async monitoring of many workers at once (a fleet board
for field-safety officers); an SMS rung between phone rungs; and pilot
it with an actual university field-safety office — the trip-plan PDF
they already require contains every field FieldLine needs.

## Built with

`python` · `uv` · `calle-ai` (CALL-E Python SDK) · CALL-E `/v1/calls` +
`result_schema` structured extraction · `rich` · `pyyaml` · `pytest`

## Disclosures

Built during the submission period with AI coding assistance (Claude);
design, review, and verification by the author. Demo-mode transcripts
are hand-written simulation data, marked `// DEMO` in source; live mode
uses the real CALL-E SDK unchanged.

---

# PR into awesome-phone-call-agents

Per https://github.com/CALLE-AI/awesome-phone-call-agents (submission is
a PR there; full steps in docs/submit-checklist.md).

**Contribution area:** User-facing Apps → `apps/python/fieldline/`
(the app folder: README.md, LICENSE, pyproject.toml, .env.example,
src/, tests/, examples/, scripts/ — no internal hackathon docs).

**README list entry** (add one line, Apps section, exact repo format —
`- [Project Name](url) - One sentence…`):

```markdown
- [FieldLine](https://github.com/xiehuanyi/fieldline) - Lone-worker safety net that schedules check-in calls, detects a silent duress phrase, and runs an escalation call cascade ending in a structured incident brief.
```

**Repo checklist compliance** (their CONTRIBUTING requirements):
- README with setup and usage — yes (root README.md)
- Dry-run / no-call path by default — yes (DEMO mode is the default; live needs a key + consent gate + `LIVE` confirmation)
- Clear credential handling — yes (.env.example, key never printed)
- Cancellation behavior — yes (`fieldline end`, honored between calls)
- Fictional/masked phone numbers — yes (+1-555-01xx; masked in output)
- Side effects documented — yes (README "Going live" + Safety design)
- Tests / manual verification path — yes (32 tests; `checkin-now` for a
  single opt-in live call)
- No secrets or personal data — yes
- Validation: run `python3 scripts/validate_repository.py` in their repo
  before opening the PR

**Suggested PR title:** `Add FieldLine — lone-worker safety check-in app (apps/python)`

**Suggested PR body:**

> Adds **FieldLine**, a user-facing Python app: a lone-worker safety net
> that schedules CALL-E check-in calls against a YAML trip plan, detects
> a silent duress phrase via `result_schema` extraction, and runs an
> escalation call cascade that ends in a structured incident brief.
> Dry-run (demo) mode is the default and needs no credentials; live mode
> uses the `calle-ai` SDK with idempotency keys, a consent gate, and
> cancellation between calls. Includes 32 tests and fictional numbers
> only. Built for the CALL-E: Your Code Is Calling hackathon.
