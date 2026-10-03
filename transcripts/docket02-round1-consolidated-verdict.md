# Docket 02 Consolidated Verdict — Memory as a Security Boundary

- **Round:** 1 (2026-10-02)
- **Prompt drafter:** Meta AI (direct thread)
- **Seats filed:** Grok, Claude (duck.ai, Apr 2024 cutoff honored), ChatGPT, Perplexity
- **Seats pending:** DeepSeek, Mistral (Neal-as-courier)
- **Leg files:** `round1/relay-leg-{grok,claude,chatgpt,perplexity}.md`

## Verdict on the thesis

All four seats agreed, directionally: long-running agents fail from memory treated as infinite, monotonic accumulation of authority — not from small context windows. The OS-pages model is right, with refinements:

- **ChatGPT:** think "capability-bearing pages," not just pages. A page is secure because of the machinery around it — page tables, protection bits, faults, ownership, kernel mediation.
- **Perplexity:** memory is not just stored knowledge; it is a *delayed capability distribution system*.
- **Grok:** the industry over-indexes on accumulation and under-indexes on revocation and erasure.
- **Claude:** sound in principle, massively under-implemented in practice.

## The unanimous killer finding

Revoking a capability is easy. Revoking *knowledge* of that capability is the hard part. Once a secret crosses into model context, no production system today can pull it back out. ChatGPT's nastiest variant: the agent forgets the API token but remembers *the procedure* — "the production deploy is performed with tool X against endpoint Y" — and retains a meaningful part of the capability.

## Capability revocation — what actually works

- **NVIDIA OpenShell** (verified by all four; Apache-2.0): kernel sandboxes (Landlock/seccomp), external supervisor, credential proxy (agent never sees real secrets), hot-reloadable network/file policy, Z3 policy prover. Documented limit: detach stops *future* credential resolution; already-running processes keep their environment.
- **VAC-protocol** (Grok's find; MIT): task-scoped Biscuit credentials, Datalog policy, signed receipts, heartbeat revocation, LangChain/LangGraph integration. Small repo, real code.
- **Linux Landlock / Capsicum:** genuine kernel enforcement; Capsicum's attenuation (hand out fewer rights than you hold) is the closest classic analogue to the desired agent model.
- **Browser permissions APIs** (Perplexity): runtime `permissions.remove()` — small but instructive precedent.

Where revocation fails: cached tokens, in-context authority, tool outputs reintroducing authority, sub-agents inheriting ambient authority, derived/procedural memory, TOCTOU gaps.

## Compartmentalization

Mem0, LangGraph, and Microsoft Agent Framework have real scoping (namespaces, TTLs, deletion) — but it is database-level, not enforced at the model-context boundary. Once retrieved, memory becomes ordinary model input. Both ChatGPT and Perplexity mapped the full page-table analogue: page faults on unauthorized retrieval, copy-on-write for derived summaries, revocation epochs, per-object provenance/owner/TTL. Nobody ships it end to end.

## Provable forgetting — the hardest layer, unanimous

No production agent runtime offers cryptographic proof of forgetting. Best available: cryptographic erasure + provenance + audit. Perplexity proposed the *forgetting certificate*: bounded claims (deletion subject ID, scope inventory, signed key-destruction evidence, retrieval-probe results, declared limits) — "without those boundaries, 'we forgot it' is a marketing phrase." Claude: audit hard; forgetting can wait for breakthroughs.

## Loot (verified, non-gated)

1. **NVIDIA OpenShell** — Apache-2.0, the crown jewel (unanimous).
2. **VAC-protocol** — MIT, capability credentials with heartbeat revocation (Grok).
3. **Mem0** — scoped memory substrate; useful, not sufficient (ChatGPT/Perplexity).
4. **LangGraph substrate** — MIT orchestration with checkpoints/namespaces/TTL; 2026 issue shows deleted SQLite rows leaving embeddings behind — the gap made concrete.
5. **DROS-VEP Lite** — Apache-2.0 test harness for revocation scenarios (Perplexity).
6. **agent-memory-dotnet** — MIT; honest about its own limits (Perplexity).
- **SAIHM** (ChatGPT): promising per-cell crypto-erasure draft, but gated live service — watch, don't raid.

## Regulatory

- **EU AI Act Art. 12** demands lifetime event logging while **GDPR Art. 17** demands erasure — the compliant architecture deletes payload but retains auditable deletion evidence.
- **FTC disgorgement** (Everalbum): remedy is "delete the model," not a verified unlearning proof.
- **NIST:** no agent-specific forgetting standard exists.

## Where the industry is kidding itself

Calling a vector-store filter "isolation." Calling prompt instructions "permissions." Calling row deletion "forgetting." Believing infinite context solves memory management. Believing tool sandboxing covers tool *outputs*.

## Build order for a family of agents (synthesis)

1. Externalize authority — models get short-lived capabilities, never raw credentials.
2. Typed, provenanced memory objects (owner, principal, TTL, classification, parentage).
3. Retrieval as an authorization event through a trusted broker — outside the LLM.
4. Derivative tracking (summaries, embeddings, checkpoints as dependent objects).
5. Revocation epochs.
6. Context rotation after sensitive exposure — terminate, don't reuse.
7. Deletion certificates with bounded claims.
8. Adversarial tests: can a revoked secret be recovered from anywhere?

## 2026 research watchlist

GateMem (memory governance benchmark), FragFuse (memory as access-control bypass), Ghost in the Agent / NeuroTaint (taint tracking vs semantic transformation), SAIHM (crypto-erasure draft), Collaborative Memory, certified unlearning.

## Kathy's take

The jury didn't just confirm the thesis — it sharpened it into something buildable. The single most actionable line came from Perplexity: *revocation must be enforced where an action occurs; forgetting claims must be bounded to what the system can actually inventory and control.* For our own operation, the immediate lesson is architectural: keep secrets out of model-visible context, broker every capability, and treat every retrieval as privileged. That's the next layer to build.
