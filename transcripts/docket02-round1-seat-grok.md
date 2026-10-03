# Docket 02 Jury Leg — Grok

- **Docket:** 02 — Memory as a Security Boundary
- **Seat:** Grok (fresh browser leg, memory-free; standing thread untouched)
- **Run:** 2026-10-02 ~14:37 CDT
- **Fidelity note:** Grok did not author a SOURCES section; the report ends at VERDICT with a "90 sources" button listing the web searches it ran instead. Inline hyperlinks captured: GitHub (OpenShell), GitHub (VAC-protocol), Docs.letta, GitHub (Microsoft multi-agent ref), Truescreen (EU AI Act), Casrai (FTC disgorgement).

--- BEGIN VERBATIM GROK RESPONSE ---

Grok Worked for 14s

SUMMARY
The thesis holds under field inspection: long-running agents fail from unbounded, monotonic accumulation of authority more than from raw context size. Real systems that treat tool access, credentials, and external resources as externally enforced, revocable, and auditable (OpenShell, capability protocols) demonstrate workable boundaries. Memory itself—episodic, semantic, working—remains mostly soft: frameworks provide tiering and decay, but few enforce hard permissioned, revocable, or provably forgettable isolation. "Memory as OS pages" is a productive model for prioritization; the industry currently over-indexes on accumulation and under-indexes on revocation and erasure.

FIELD REPORT 1 — Capability Revocation
Verified mechanisms (primary sources preferred):
• NVIDIA OpenShell 0.1.x (Apache-2.0, GitHub NVIDIA/OpenShell). Kernel-level sandboxes (Landlock for filesystem, seccomp/process isolation), external supervisor that inspects HTTP/GraphQL/MCP traffic, and credential injection that keeps real secrets outside the agent process. Policy (YAML + OPA/Rego formal analysis) is enforced outside the agent. Network/file rules can be hot-reloaded into a running sandbox after human (or policy-advisor) approval; deeper filesystem/process limits require a new sandbox. Agent never sees real credentials; supervisor swaps them only for approved endpoints. Denials are logged; agent can propose narrow rule expansions but cannot self-approve. Supports Codex, Claude Code, Pi, Hermes, and others without agent rewrite. Recent commits through 2026-10-02 confirm active development. [GitHub]
• Capability-based patterns (object-capability / OCap literature applied to agents). Revocable forwarders: grant a capability wrapped in a forwarder; destroy the forwarder to revoke without global ACL changes. Task-scoped capabilities expire with task lifetime. Concrete open implementation: VAC-protocol (MIT, GitHub certainly-param/VAC-protocol). Uses Biscuit tokens + Datalog policy + signed receipts. Control plane issues short-lived root credentials; sidecar verifies policy + receipts and injects real API keys. Heartbeat-based revocation (seconds). Agent never holds the key. Small repo (~12 commits at time of inspection), but code for sidecar, control plane, receipts, and LangChain/LangGraph integration is present. [GitHub]
• Agentic Credential Vault (GitHub Atomlaunch/agentic-vault). Per-agent scoped JIT tokens, proxy enforcement, revoke-by-token/session/task/agent, audit log. Agent calls through vault; vault injects secrets.
LangGraph / similar frameworks: Checkpointing and thread deletion exist (delete_thread removes checkpoints). No native mid-run tool-handle or credential revocation at the capability level; tool access is typically configured at graph compile or via external wrappers. State can be updated, but cached tool outputs or in-context tokens are not automatically neutered.

Where revocation fails in practice:
• Cached tokens or tool results already in the context window or vector store re-introduce authority.
• In-context "memory" of prior successful calls (agent simply retries or reconstructs the capability from description).
• Sub-agent spawning that inherits or reconstructs ambient authority.
• Credential material that leaked into model weights, logs, or external stores before revocation.
• Hot-reload works for network/file rules in OpenShell; process/filesystem sandbox bounds are harder to change without restart.
OS kernels (file handles, capabilities) and browser agents have long had revocation primitives; agent runtimes are only now externalizing them.

FIELD REPORT 2 — Compartmentalization
Episodic / semantic / working distinctions are widely discussed and partially implemented, but enforcement of permission boundaries is rare.
• Letta (ex-MemGPT) (Apache-2.0): Explicit hierarchy—core/in-context memory blocks (self-editable via tools), recall (conversation history), archival (vector). Agent manages paging via tools. Memory blocks can be scoped; recent work adds permission modes that constrain memory sub-agents to approved roots. Isolation is primarily per-agent storage backend, not hard page-table style. Self-editing is powerful and therefore a risk surface. [Docs.letta]
• Other open frameworks (from curated lists and repos): Graphiti/Zep (bi-temporal knowledge graphs), Cognee, Mem0, tokio-memory / tokio-agent-memory (episodic/semantic/working with decay and consolidation), agent-memory (Ebbinghaus-style decay), Hindsight, etc. Most provide retrieval, decay, or consolidation. Few implement principal-scoped or tool-scoped isolation with different permissions, eviction under policy, or copy-on-write. Vector/graph stores are typically shared or lightly namespaced.
• LangGraph: Threads and checkpointers provide conversation-scoped state; stores can be cross-thread. No built-in memory page tables or permissioned segments.

Page-table-like protections for agent memory (what would be required):
• Address space isolation per task/principal/tool (separate vector indexes, graph partitions, or KV namespaces with capability tokens).
• Page faults on unauthorized access (runtime trap + audit instead of silent retrieval).
• Copy-on-write for shared semantic knowledge that must be attenuated per agent.
• Explicit eviction / swapping under policy (not just LRU or decay).
• Capability tokens for memory regions themselves (read/write/execute/forget rights).
• Audit of every map/unmap/read.
Today almost all of this is advisory (prompts, retrieval filters) rather than enforced. Microsoft multi-agent reference architecture notes memory as a high-risk surface requiring scope, governance, and eventual forgetting, but does not ship the primitives. [GitHub]

FIELD REPORT 3 — Provable Forgetting
This remains the hardest layer. No production agent runtime currently offers cryptographic proof that a secret, credential, or PII blob has been erased from all memory tiers, model activations, caches, and logs.

Primitives that exist (research / partial):
• Cryptographic erasure (encrypt-then-destroy-key) for storage media (NIST SP 800-88 Rev. 2 points to external standards; CE is one method).
• Machine unlearning with verifiability: SNARK-based proofs of correct unlearning + dataset membership (Eisenhofer et al., SaTML 2025 and earlier arXiv versions). FHorgEt and related FHE/MPC constructions for confidential unlearning.
• Execution-state unlearning for stateful agents: provenance-guided selective replay that crops KV cache and regenerates post-injection suffix (Yao et al., 2026 arXiv). Shows that simple memory deletion or instruction-based forgetting leaks under elicitation.
• Taint tracking, log redaction, secure enclaves—discussed but not standardized for agent memory pipelines.
• Provable storage (e.g., cryptographic receipts for every access) for lineage, not erasure.

Regulatory posture (verified from primary-ish sources):
• EU AI Act (Reg. 2024/1689): High-risk systems must support automatic event logging over lifetime (Art. 12). Providers retain logs under their control for at least 6 months (Art. 19), longer if required by other law. Documentation retained 10 years (Art. 18). Agents are covered under existing AI-system definitions. Tension with GDPR Art. 17 (right to erasure): personal data must be deletable, while audit logs must be retained. Architectural separation of PII-bearing memory from pseudonymized decision logs is the practical resolution. [Truescreen]
• FTC: Algorithmic disgorgement (model + data destruction) ordered in multiple cases (Cambridge Analytica, Everalbum, Kurbo, etc.) when models were built on unlawfully obtained data. Remedy is "delete the model," not a verified unlearning proof. Technical literature shows unlearning can fail under quantization or fine-tuning; FTC treats destruction as the enforceable act. [Casrai]
• NIST: Sanitization guidance exists; no agent-specific unlearning standard. An agent cannot currently prove it has forgotten a secret to a third party in a production setting. Best available is provenance + selective replay + external credential isolation + audit of what was never written.

FIELD REPORT 4 — Open-Source Raid (LOOT)
Only concrete, verifiable, non-gated finds:

LOOT 1
• Repo: https://github.com/NVIDIA/OpenShell
• License: Apache-2.0
• Last activity: commits and tags through 2026-10-02 (v0.1.3-pre series)
• What it actually does: Kernel sandboxes + external supervisor + credential proxy + formal policy analysis + mid-run network/file rule hot-reload. Agent cannot override. No signup/API key required for the runtime itself.
• Claims vs reality: Matches documented behavior; formal verification of policy changes is present.

LOOT 2
• Repo: https://github.com/certainly-param/VAC-protocol
• License: MIT
• Last activity: small but present codebase (sidecar, control plane, receipts, tests)
• What it actually does: Task-scoped Biscuit credentials, Datalog policy, signed receipts, heartbeat revocation, agent never sees API keys. LangChain/LangGraph example exists.
• No signup/API key required.

Near-misses (not LOOT under strict rules): Letta, Graphiti, Mem0, tokio-agent-memory, OWASP Agent Memory Guard (poisoning defense, not revocation/forgetting), various capability papers without shipping runtimes. Most memory frameworks are accumulation-focused.

VERDICT
"Memory as OS pages" is the right model for prioritization, even if current implementations are incomplete. Pages are mappable, permissioned, revocable, evictable, and auditable; agent memory is still mostly a growing heap with optional decay. Authority drift over weeks occurs because tool handles, credentials, and retrieved facts accumulate without a corresponding revocation or unmap primitive.

For a family of agents that must run weeks without authority drift, prioritize in this order:
1. External capability enforcement (OpenShell-style or VAC-style) so the agent never holds long-lived ambient authority.
2. Hard compartmentalization of memory stores by principal/task with capability tokens for access.
3. Explicit eviction + provenance so selective replay or cryptographic erasure becomes feasible.
4. Audit of every grant, use, and revoke.

The industry is kidding itself in three places: (a) treating vector stores and conversation history as "memory" without permission or eviction semantics; (b) relying on prompt instructions or soft tool filters for security; (c) claiming unlearning or "forget" while model weights, KV caches, and tool-output residues remain. OpenShell and the capability protocols show the direction is already practical for the tool boundary. Extending the same external, enforceable, revocable treatment to the memory boundary is the next necessary step.

90 sources

--- END VERBATIM GROK RESPONSE ---
