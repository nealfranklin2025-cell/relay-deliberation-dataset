# Evaluated and Rejected (Docket 02, Round 1)

Items the jury explicitly considered and did **not** take as loot, with the
evaluating seat, the reason, and what each item was kept as instead. Absence
from this list means "not considered," not "rejected."

## 1. SAIHM — REJECTED as loot; kept as watch item

- **Evaluated by:** ChatGPT (LOOT section: "NOT LOOT — SAIHM"); consolidated
  verdict ("watch, don't raid").
- **What it is:** per-memory-cell encryption keys, revocable sharing,
  cryptographic erasure; an Independent Submission Internet-Draft (explicitly
  not an IETF standard).
- **Why rejected:** the open MCP server is a client to an operator endpoint
  and requires an endpoint/token for normal runtime; the hosted live service
  requires sign-in. Fails the round's non-gated rule.
- **Kept as:** "promising research/reference implementation — gated live
  service"; on the 2026 research watchlist.
- **Source file:** `transcripts/docket02-round1-seat-chatgpt.md`
  ("NOT LOOT — SAIHM"); `transcripts/docket02-round1-consolidated-verdict.md`
  (Loot item note; watchlist).

## 2. LangGraph as a security boundary — REJECTED (as boundary; taken as substrate)

- **Evaluated by:** Perplexity, ChatGPT, Claude.
- **Why rejected:** public repository evidence supports orchestration and
  state management, not a mandatory authorization/reference-monitor design.
  No verified built-in capability revocation, secret brokering, or complete
  mediation layer. A 2026 issue (deleted SQLite memory leaving embeddings
  behind) demonstrates the gap.
- **Kept as:** orchestration substrate — see `loot-taken.md` item 4.
- **Source file:** `transcripts/docket02-round1-seat-perplexity.md`
  (LOOT assessment); `transcripts/docket02-round1-seat-chatgpt.md`
  (section D); `transcripts/docket02-round1-seat-claude.md` (LOOT #1,
  qualified).

## 3. Claude's negative findings — REJECTED as loot

- **Evaluated by:** Claude (Field Report 4, "Negative findings").
- **Items:** LlamaIndex (no revocation/compartmentalization/forgetting);
  Microsoft Semantic Kernel (no agent-memory security model); OpenAI Function
  Calling spec (API-based, no revocation mechanism published); libsodium
  (secure memory wipe primitive only — a crypto library, not an agent
  framework); Glamorous Toolkit taint tracking (research, no agent
  implementation found).
- **Why rejected:** none implements the thesis's combined requirement
  (capability revocation + agent memory, compartmentalization + enforcement,
  provable forgetting + agent integration).
- **Source file:** `transcripts/docket02-round1-seat-claude.md`
  (FIELD REPORT 4).

## 4. ChatGPT's near-misses — REJECTED as loot

- **Evaluated by:** ChatGPT (Field Report 4, "Near-misses (not LOOT under
  strict rules)").
- **Items:** Letta, Graphiti, Mem0-adjacent stores (Graphiti/Zep), Mem0
  (beyond substrate use), tokio-agent-memory / tokio-memory,
  OWASP Agent Memory Guard (poisoning defense, not revocation/forgetting),
  capability papers without shipping runtimes.
- **Why rejected:** most memory frameworks are accumulation-focused; no
  shipping runtime combined the required properties.
- **Source file:** `transcripts/docket02-round1-seat-chatgpt.md`
  (FIELD REPORT 4); `transcripts/docket02-round1-seat-grok.md`
  ("Near-misses").

## 5. Perplexity's conditional leads — REJECTED as loot; kept as leads

- **Evaluated by:** Perplexity ("Conditional leads, not accepted loot").
- **Items:** Silo, LightAgent, AgentScope (promising documented scope/tenancy
  or sandboxing features, but isolation not independently audited beneath
  every read/write path); machine-unlearning repositories (research and
  evaluation frameworks, no end-to-end formally verified agent-level
  forgetting).
- **Why rejected:** public descriptions are leads, not proof; code-path and
  test verification not completed.
- **Source file:** `transcripts/docket02-round1-seat-perplexity.md`
  ("Conditional leads, not accepted loot").
