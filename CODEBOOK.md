# Codebook

Field and schema definitions for the files in `transcripts/`.

## File naming

`docket02-round1-seat-<seat>.md` — one jury leg per seat.
`docket02-round1-consolidated-verdict.md` — the coordinator's synthesis.

`<seat>` is a lowercase product label: `grok`, `claude`, `chatgpt`, `perplexity`.

## Seat transcript structure

Each seat file has two parts:

### 1. Coordinator header (metadata, not seat content)

| Field | Meaning |
|---|---|
| Docket | Docket number and title under test |
| Seat | Product name + access route (e.g., "Claude Haiku 4.5 via duck.ai"); notes the leg was memory-free and the standing thread untouched |
| Run | Date and approximate time the leg ran (America/Chicago) |
| Fidelity note | Coordinator's record of anything affecting verbatim capture: truncation and continuation, UI/rendering artifacts removed, explicit refusals to invent data |

### 2. Verbatim seat response

Delimited by `--- BEGIN VERBATIM <SEAT> RESPONSE ---` /
`--- END VERBATIM <SEAT> RESPONSE ---`. Everything between the markers is the
seat's response as captured, unedited except for the privacy scrub documented
in PRIVACY.md.

Seat responses in Docket 02 Round 1 follow the docket brief's requested
structure:

| Section | Meaning |
|---|---|
| SUMMARY | The seat's bottom-line judgment on the thesis |
| FIELD REPORT 1–4 | Evidence gathered per field assignment (capability revocation; compartmentalization; provable forgetting; open-source raid) |
| LOOT | Concrete, verifiable, non-gated open-source finds the seat endorses ("taken") |
| Near-misses / NOT LOOT / Negative findings | Items the seat evaluated and explicitly rejected as loot, with reasons |
| VERDICT | The seat's final judgment and build priorities |
| SOURCES | Sources the seat cites (quality varies by seat; some seats refused to invent unverifiable details — those refusals are part of the record) |

**Evidence-ledger labels** (where a seat used them):

| Label | Meaning |
|---|---|
| Observed | Directly seen in a source the seat inspected |
| Inferred | Reasoned from sources, not directly seen |
| Speculating | Beyond the evidence; the seat's own extrapolation |

## Consolidated verdict structure

| Section | Meaning |
|---|---|
| Header | Round, date, prompt drafter, seats filed, seats pending, leg file index |
| Verdict on the thesis | Directional agreement/disagreement across seats, with per-seat refinements attributed |
| The unanimous killer finding | The single finding all filed seats converged on |
| Capability revocation / Compartmentalization / Provable forgetting | Synthesized evidence per field assignment |
| Loot (verified, non-gated) | Numbered list of accepted items — the "take" list |
| Regulatory | Synthesized regulatory findings |
| Where the industry is kidding itself | Shared critique points |
| Build order | Coordinator's synthesized priorities |
| 2026 research watchlist | Items flagged for monitoring, not taken |
| Coordinator's take | The coordinator's own labeled synthesis (not a seat response) |

## take-reject-lists/ structure

`take-reject-lists/loot-taken.md` — one entry per accepted item:

| Field | Meaning |
|---|---|
| Item | Name + license |
| Proposed by | Which seat(s) surfaced it, in which round |
| Disposition | TAKEN, with any qualification (e.g., "as substrate, not as security boundary") |
| Source file | Transcript file where the disposition appears |

`take-reject-lists/loot-rejected.md` — one entry per explicitly evaluated and
rejected item: what it was, who evaluated it, why it was rejected, and what it
was kept as instead (e.g., "watch item," "research lead").

Items never evaluated are not listed anywhere — absence from the lists means
"not considered," not "rejected."
