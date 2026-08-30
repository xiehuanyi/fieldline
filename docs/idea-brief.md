# FieldLine — Idea Brief

**One-liner:** Researchers and other lone workers go into the field (reef
dives, desert sites, remote stations) with a safety plan that is a paper
form and a WhatsApp promise. FieldLine turns that plan into an agent that
**phones**: scheduled check-in calls, a silent duress phrase, and an
automatic escalation call cascade when a check-in is missed.

**Wow moment:** The worker answers the 18:00 check-in and slips the
pre-agreed duress phrase into small talk. The agent doesn't flinch on the
call — ends it normally — and the screen flips to
`DURESS PHRASE DETECTED — silent escalation engaged` as it dials the
safety officer. Second beat: a missed check-in triggering a live call
cascade (worker → retry → buddy → supervisor) that ends in a structured
incident brief.

**Golden path (demo):**
1. `uv run fieldline demo` — trip plan loads (Reef Station 3 sampling
   dive, 2 check-ins, 2-rung escalation ladder, duress phrase).
2. 14:00 check-in call → transcript streams → `SAFE`, structured result,
   next check-in scheduled.
3. 18:00 check-in → no answer → retry at 18:05 → still no answer →
   OVERDUE at 18:15.
4. Cascade: call buddy (last contact 13:30, truck still at the pier,
   he'll go check) → call supervisor (acknowledges, assumes
   coordination).
5. Incident brief written to disk: timeline, transcripts, structured
   results, evidence, recommended actions. `--scenario duress` shows the
   silent-duress beat.

**Why us:** The builder is a KAUST researcher; Red Sea boat work and
desert field sites are daily reality here, and university field-safety
plans really are static documents nobody is watching at 18:05.

**Rubric mapping:**
- *Real-world impact* — lone-worker safety is regulated territory
  (working-alone rules, university field-safety plans); a missed check-in
  today relies on a human remembering to worry.
- *Quality of idea / creativity* — not another booking bot: a safety
  **protocol** as an agent — silent duress detection, no-panic escalation
  scripts, a never-auto-dial-911 policy.
- *Technical implementation* — full use of the CALL-E surface:
  natural-language `task` + `result_schema` structured extraction per
  call, `task_completed`/`completion_confidence`/`evidence`, retry with
  `idempotency_key`, fail-soft dispatcher (API failure degrades to the
  no-answer path instead of crashing the safety loop), state-machine core
  with unit tests.
- *Product experience* — polished Rich terminal UI, YAML trip plans,
  masked phone numbers, incident brief as a readable artifact,
  `DEMO_MODE` that runs with zero keys.

**Explicit non-goals:** no web dashboard, no SMS/email channel, no GPS
integration, no auto-dialing emergency services (surfaced in the brief,
never dialed), no multi-trip scheduler daemon, no Gemini/LLM brain (the
decision core is a deterministic state machine — fewer failure modes;
CALL-E does the conversational intelligence).

**Riskiest assumption:** that CALL-E's structured extraction can carry a
"detect this exact phrase but do not react" instruction. Mitigation: the
instruction rides in the `task` prompt and the flag is a boolean in
`result_schema` — exactly the pattern the API is built for; live
verification is a 1-call test once the account exists.

## Candidates considered (scored: pain / wow / feasibility / demo / rubric / differentiation)

| Idea | Score | Why not |
|---|---|---|
| **FieldLine** (field-safety check-in + duress + cascade) | 5/5/5/5/5/4 | **Picked** |
| RxScout — fan-out calls to pharmacies for med stock | 4/5/5/5/5/3 | Canonical voice-agent demo; `quoterunner`/`hungrycall-cascade` already own fan-out in the awesome repo |
| Arabic bridge — agent calls Saudi couriers/clinics in Arabic for expats | 5/4/4/3/4/4 | Judges can't verify Arabic; demo weaker in an English video |
| Reagent chaser — call suppliers about stuck lab orders | 4/3/4/3/4/3 | Weak wow; `freshchain-resolver`/`quotewake` adjacent |
| Subscription-cancellation fighter | 3/4/4/4/3/2 | Common idea; impersonation/legality gray zone |

Existing apps checked in `CALLE-AI/awesome-phone-call-agents` (28 Python
apps): closest neighbors are `incidentbridge` (DevOps-style incident
escalation) and `metapelet-checkin` (companionship check-in call).
FieldLine differs from both: it is a *safety protocol* — schedule, duress
phrase, ladder policy, stand-down rules — not a single call.
