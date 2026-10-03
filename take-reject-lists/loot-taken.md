# LOOT — Taken (Docket 02, Round 1)

Items the jury accepted as verified, concrete, non-gated finds. Dispositions
are from the consolidated verdict's "Loot (verified, non-gated)" section;
per-seat provenance is noted.

## 1. NVIDIA OpenShell — TAKEN (unanimous; "the crown jewel")

- **License:** Apache-2.0
- **Proposed by:** all four seats independently (Grok, Claude, ChatGPT,
  Perplexity) — the round's only unanimous loot item.
- **What it is:** kernel sandboxes (Landlock/seccomp), external supervisor,
  credential proxy (agent never sees real secrets), hot-reloadable network/file
  policy, Z3 policy prover.
- **Qualification (recorded in the verdict):** detach stops *future* credential
  resolution; already-running processes keep their environment. Accepted as the
  strongest available runtime authority boundary, not as a complete
  secure-memory system.
- **Source file:** `transcripts/docket02-round1-consolidated-verdict.md`
  ("Loot (verified, non-gated)" item 1); each seat transcript's LOOT section.

## 2. VAC-protocol — TAKEN

- **License:** MIT
- **Proposed by:** Grok.
- **What it is:** task-scoped Biscuit credentials, Datalog policy, signed
  receipts, heartbeat revocation, LangChain/LangGraph integration. Small repo,
  real code.
- **Source file:** `transcripts/docket02-round1-consolidated-verdict.md`
  (item 2); `transcripts/docket02-round1-seat-grok.md` (LOOT 2).

## 3. Mem0 — TAKEN (as substrate; "useful, not sufficient")

- **License:** open-source (OSS implementation; some Platform features gated —
  see qualification).
- **Proposed by:** ChatGPT, Perplexity.
- **What it is:** scoped memory substrate (user/agent/run scoping, deletion by
  ID or filter).
- **Qualification:** accepted as a compartmentalized memory substrate only;
  deletion of external memory does not erase already-materialized model
  context. Not sufficient for the thesis on its own.
- **Source file:** `transcripts/docket02-round1-consolidated-verdict.md`
  (item 3); `transcripts/docket02-round1-seat-chatgpt.md` (LOOT #2);
  `transcripts/docket02-round1-seat-perplexity.md` (compartmentalization
  discussion).

## 4. LangGraph memory/checkpoint substrate — TAKEN (as orchestration substrate only)

- **License:** MIT
- **Proposed by:** ChatGPT, Perplexity.
- **What it is:** checkpointed execution, thread-scoped state, namespaces,
  persistent stores, deletion, TTL support where the backend implements it,
  human approval/interruption.
- **Qualification:** accepted as orchestration/state substrate. Explicitly
  **rejected** as a security boundary: Perplexity could not verify mandatory
  capability revocation or secret brokering in the public repo. The round also
  recorded a 2026 issue where deleted SQLite-backed memory left embeddings
  behind — "the gap made concrete."
- **Source file:** `transcripts/docket02-round1-consolidated-verdict.md`
  (item 4); `transcripts/docket02-round1-seat-chatgpt.md` (LOOT #3);
  `transcripts/docket02-round1-seat-perplexity.md` (LOOT assessment).

## 5. DROS-VEP Lite — TAKEN (as test harness)

- **License:** Apache-2.0
- **Proposed by:** Perplexity.
- **What it is:** open reference/evaluation environment for security controls
  at agent authorization-to-execution boundaries.
- **Qualification:** accepted as a test harness for revocation scenarios, not
  as a deployed agent-memory platform.
- **Source file:** `transcripts/docket02-round1-consolidated-verdict.md`
  (item 5); `transcripts/docket02-round1-seat-perplexity.md`
  (LOOT: DROS-VEP Lite).

## 6. agent-memory-dotnet — TAKEN ("honest about its own limits")

- **License:** MIT
- **Proposed by:** Perplexity.
- **What it is:** owner-scoped long-term memory, multi-tenant isolation,
  scoped recall and clearing, read audits, decay/pruning, configurable
  isolation.
- **Qualification:** its own documentation states limits (session IDs are
  capabilities; short-term messages readable by session handle). Taken as worth
  studying, not as a hard boundary.
- **Source file:** `transcripts/docket02-round1-consolidated-verdict.md`
  (item 6); `transcripts/docket02-round1-seat-perplexity.md`
  (LOOT: agent-memory-dotnet).
