# Docket 02 Jury Leg — Perplexity

- **Docket:** 02 — Memory as a Security Boundary
- **Seat:** Perplexity (fresh browser leg, memory-free; Kathy's standing session untouched)
- **Run:** 2026-10-02 ~14:45 CDT
- **Thread:** standing relay thread (URL redacted for privacy)
- **Fidelity note:** Perplexity's page extraction duplicates each paragraph in the DOM; the leg deduplicated while preserving exact wording. 45 sources cited. The model explicitly refused to invent last-commit dates it could not verify.

--- BEGIN VERBATIM PERPLEXITY RESPONSE ---

SUMMARY
Finding: I agree with the thesis directionally: the dangerous failure mode in long-running agents is not merely insufficient context length; it is the uncontrolled conversion of past observations, outputs, and credentials into durable authority. The practical security boundary must be outside the model—in a capability-enforcing runtime, credential broker, memory service, and audit system—because a model cannot reliably "forget" text already placed in its context.
The strongest available pattern is: keep secrets and permissions out of agent-visible memory, issue narrow and short-lived capabilities at tool-call time, enforce every use at a non-bypassable boundary, and store memory in permissioned, versioned compartments with retention and deletion evidence. NVIDIA OpenShell is the clearest current implementation I could verify for the runtime half of this design; LangGraph is useful orchestration, but its public repository does not establish it as a revocation-enforcing security boundary.

FIELD REPORT 1 — Capability Revocation
Verified mechanisms
| System layer | Verified mechanism | What can be revoked during or around a run | Important limit |
| OS/kernel capability model | File descriptors, process privileges, namespaces, cgroups, firewall rules, and process termination are enforceable outside an agent | A process can lose access by closing/revoking a descriptor, killing the process, changing policy at a reference monitor, unmounting a resource, or severing network connectivity | Bytes already read into process memory, already-written files, and previously issued bearer secrets are not retroactively removed |
| Browser extension permissions | WebExtensions exposes permissions.remove() and an onRemoved event; permission state can change at runtime | Extension APIs and host permissions can be surrendered or revoked | A page snapshot, DOM data, cookies, tokens, or network responses already copied by the extension remain with the extension unless separately deleted |
| OpenShell | Sandboxed workload, kernel-mediated file/syscall/network controls, gateway policy checks, endpoint-bound credential injection, policy-change analysis | File/network/process authority can be restricted at the sandbox/gateway boundary; credentials are represented as brokered request behavior rather than given directly to the agent | This prevents future use at the boundary, not model recall of a secret or data already included in a prompt/tool response |
| LangGraph | Stateful graph execution, persistent checkpoints, human inspection/modification of state | An operator can interrupt/edit graph state and alter future flow | Public repository evidence supports orchestration and state management—not an intrinsic non-bypassable revocation monitor for credentials or tools |

OpenShell's stated architecture is notable: each agent runs isolated; the runtime enforces file, syscall, and network rules; every outbound connection is policy-checked; and the agent "never see[s] real credentials"—the gateway adds them only to requests for approved endpoints. Its source tree identifies distinct sandbox, supervisor, server/gateway, and policy-prover components, which makes the design more than a dashboard claim.
Its enforcement model is the right shape for revocation: removing a network rule or provider binding causes the next privileged operation to fail at the gateway/kernel boundary. That is materially different from telling an LLM in text, "you may no longer use GitHub."
Browser permissions provide a smaller but instructive precedent. Mozilla's WebExtensions API permits optional permissions at runtime, fires onRemoved on revocation, and provides permissions.remove(). Chromium's documentation distinguishes granted from active permissions, explicitly noting that removal reduces immediate active capabilities but may not erase the record that permission was once granted.

Where revocation fails
1. Bearer-token escape. If the agent ever receives a raw API key, OAuth refresh token, cookie, private key, signed URL, or service credential in model-visible text, a later policy change cannot guarantee the model will not reproduce or use it. Short-lived tokens reduce the window but do not erase a token copied into context, a file, a trace, or a downstream prompt.
2. Already-authorized side effects. Revocation blocks future operations, not past ones. A revoked write capability cannot undo an email already sent, a repository cloned, a database row exported, or a cloud resource created. Operations need idempotency, compensating actions, and transaction/audit identifiers.
3. In-context authority. A tool result can contain a secret, policy instruction, access URL, or inferred authorization. Once passed to the model, it becomes part of the agent's working context even if the original tool is disabled. Prompt-level "do not use that" controls are not revocation.
4. Caches and delegated copies. SDK token caches, browser session stores, credential helpers, subprocess environment variables, open file descriptors, copied workspace files, and child processes can retain authority after a control-plane revocation.
5. Output reintroduction. An agent can write a credential into its own scratchpad, a checkpoint, a vector store, a task note, a chat transcript, or a tool argument. Later retrieval re-imports the authority into a supposedly clean run.
6. TOCTOU gaps. A policy decision and credential issuance must be atomic. A governance checkpoint that approves an action but lets a later node fetch a generic secret leaves a time-of-check/time-of-use gap. A public LangGraph governance proposal identifies this exact risk and argues that the authorization decision and scoped credential resolution must occur in one operation; this is useful analysis, but it is an issue/proposal, not proof of a stock LangGraph security guarantee. (Ref: Trust-gated checkpoints and governance nodes for LangGraph #7303)

Implementation judgment
• Verified as enforcement-oriented: OpenShell's sandbox/gateway approach. It has a public Apache-2.0 repository, explicit architectural components, and claims kernel/gateway mediation of the relevant operations.
• Verified as an orchestration framework: LangGraph. It supports durable state, human state modification, and memory patterns, but I could not verify from its public repository that it ships mandatory capability revocation, secret brokering, or a complete mediation layer. Treat it as a place to integrate a security boundary—not the boundary itself.
• Not verified here: a production agent runtime that can retroactively revoke knowledge already shown to a model. I do not believe such a guarantee is technically credible without ending the model session and ensuring all persistent derivatives are addressed.

FIELD REPORT 2 — Compartmentalization
The three memory classes
The conventional labels are useful, but they are not protections by themselves:
| Memory class | Typical contents | Security risk | Required enforcement |
| Working memory | Current prompt, tool results, scratchpad, active plan | Highest immediate leakage risk; the model can act on anything present | Minimize inclusion, redact secrets before injection, use per-step capability labels, terminate/rotate context after sensitive exposure |
| Episodic memory | Events, task histories, transcripts, action traces, checkpoints | Cross-task leakage and replay of a past authority decision | Store under task/principal scope; append provenance, expiry, sensitivity, and retention policy; prevent unscoped retrieval |
| Semantic memory | Durable facts, preferences, embeddings, summaries, inferred profiles | Authority laundering: a credential or confidential fact becomes a "fact" and survives deletion of the original transcript | Separate public/shared facts from restricted claims; require provenance, review, TTL, revocation index, and revalidation on read |

The important distinction is that "episodic," "semantic," and "working" memory describe function. They do not establish access control. A vector database namespace, an agent framework's memory class, or a prompt-template convention becomes a security boundary only when a trusted storage/retrieval layer checks identity, purpose, labels, and policy before returning content.

What current frameworks enforce
LangGraph explicitly presents durable execution, long-running state, short-term working memory, and long-term persistent memory. It also enables human inspection/modification of state. Those are valuable controls for reliability and operator intervention, but the repository-level material I reviewed does not demonstrate OS-like mandatory access controls between principals, tools, and memory pages.
Several open-source projects advertise memory scopes and tenancy boundaries:
• agent-memory-dotnet claims owner-scoped long-term memory, scoped recall and clearing, read audit trails, decay/pruning, and a configurable isolation mode. Its own documentation also states an important caveat: short-term messages are readable by session handle, and "a session id is itself the capability." That is a real security model, but it shifts security onto secrecy and correct handling of session IDs.
• silo presents persistent, versioned, multi-tenant agent memory with strict per-project isolation and CAS-style writes under Apache-2.0. The available search evidence was insufficient to independently audit whether the isolation is enforced beneath every read/write path, so this should be evaluated from code and tests before depending on it.
• LightAgent documents MemoryPolicy and MemoryScope, including tenant isolation, provenance, trust, expiration, memory-write admission controls, allowed sources/scopes/agents, and an option to enforce expiry. This is the right API vocabulary. It is still application-layer enforcement unless paired with an external storage/control-plane boundary.
• AgentScope documents multi-tenant/multi-session serving and isolated execution backends such as Docker, Bubblewrap, Kubernetes, E2B, and others. That establishes useful workload isolation choices, but "multi-session" should not be equated with formal memory noninterference without auditing configuration and storage paths.

Page-table model for agents
A workable analogy is not "one page per fact." It is an object-capability memory map:
| OS notion | Agent-memory analogue | Enforcement behavior |
| Virtual page | Immutable memory object or chunk: transcript span, artifact, summary, embedding source set, credential reference | Object has stable ID, content hash, provenance, classification, creator, owner, TTL, and deletion state |
| Page table entry | Per-run grant | Maps a run/principal/tool/purpose to an object handle with read, append, derive, summarize, or delete-request rights |
| Address-space separation | Principal/task/workspace namespaces | A run does not enumerate or retrieve another namespace without an explicit cross-domain grant |
| Supervisor mode | Trusted memory broker | Model cannot query storage directly; broker validates every retrieval and write |
| Page fault | Retrieval request | Broker evaluates purpose, policy, trust level, budget, sensitivity, and current capability before materializing content |
| Copy-on-write | Derived note/summary | A child task can make a derivative without mutating parent memory; derivative retains source provenance and inherited classification |
| mprotect | Dynamic reclassification | Mark an object read-only, quarantined, export-prohibited, no-model-context, or revoked |
| Unmap | Revocation | Future reads fail; the broker excludes object and derivatives from retrieval |
| Swap/eviction | TTL/retention deletion | Expire by policy; cryptographically erase underlying keys where possible; retain minimal non-sensitive deletion evidence |
| Core dump/audit log | Tamper-evident access trail | Log every memory fault, grant, retrieval, transformation, export, revocation, and deletion outcome |

Example: A payroll agent receives an employee's bank-account update.
• The raw document is stored in a restricted evidence compartment accessible only to the payroll workflow and a human reviewer.
• The operational system receives only a structured action token: update_payment_destination(employee_id, verified_account_ref).
• The general HR assistant may receive a derived statement such as "payment details updated on 2026-10-02," but never the bank number.
• If the data subject's retention period expires, the memory broker revokes the document object and all retrieval handles, erases its encryption key, blocks retrieval of derived embeddings/summaries marked as dependent, and emits an auditable deletion record.
• The LLM session that read the raw data is not considered clean; it is terminated rather than reassigned to unrelated work.
This is stricter than typical agent memory systems because it treats retrieval as a privileged event, not a convenience function.

FIELD REPORT 3 — Provable Forgetting
Bottom line: An agent generally cannot prove it has forgotten a secret merely because a record was deleted. Once a secret appears in an LLM's prompt, outputs, checkpoint, trace, embedding, summary, tool call, or model weights, a complete proof must account for every derivative and for the model's possible retention of the information.
A defensible claim is narrower: "Under a specified threat model, the system revoked all future access paths, destroyed the cryptographic keys protecting the identified stored objects, deleted or quarantined indexed derivatives, and produced auditable evidence of those operations." That is provable loss of access to protected storage, not proof that a stochastic model has erased a fact from its activations or parameters.

Available primitives
| Primitive | What it can establish | What it cannot establish |
| Cryptographic erasure | Destroying per-object/per-tenant encryption keys can render retained ciphertext computationally inaccessible, assuming sound key isolation and no surviving plaintext/key copy | It does not erase plaintext copied into prompts, logs, screenshots, RAM, model weights, backups containing keys, or third-party systems |
| Secure enclaves/TEEs | Can reduce exposure of plaintext and keys, and may support remote attestation of a trusted code path | An enclave does not make the model unable to remember input it received; it also introduces hardware, side-channel, supply-chain, and attestation-scope assumptions |
| Information-flow/taint tracking | Can label sources and propagate labels through tool calls, documents, summaries, outputs, and storage operations | LLM semantic transformations make complete taint propagation difficult; summaries can obscure provenance and external systems may break the chain |
| Data lineage/dependency graph | Can enumerate raw objects and known derived embeddings, summaries, prompts, exports, and checkpoints for a deletion campaign | Completeness depends on instrumentation. An unlogged copy or manual export defeats completeness |
| Log redaction | Removes secrets from logs and reduces later retrieval exposure | It cannot reclaim logs already exported, backed up, indexed, or seen by a user/model |
| Machine unlearning | Can attempt to reduce a trained model's dependence on a forget set; some work evaluates indistinguishability from retraining without that set | It is generally probabilistic/empirical, costly, model-specific, and not equivalent to erasing every manifestation of a secret from an agent system |

A recent cryptographic-evaluation paper frames "certified removal" and unlearning evaluation using distinguishability-style games. That is intellectually closer to a proof than simple benchmark accuracy, but it remains a guarantee under defined assumptions and threat models—not a universal proof that every downstream agent component has forgotten an exposed secret.
The more direct operational approach is architectural prevention: never put revocable credentials or highly sensitive PII in the model-visible context in the first place. Give the model opaque references and expose a constrained operation through a broker. For example, provide credential_ref=payments-prod-write only to a gateway that can authorize POST /beneficiaries for a named principal and time interval; do not show an API key to the model.

Regulatory posture: The sources I verified do not create a general technical obligation called "provable forgetting for agents." They instead create governance, retention, minimization, deletion, documentation, and logging obligations that an agent architecture must reconcile.
• The FTC has described enforcement actions requiring retention schedules that keep consumer data only as long as necessary and mandating deletion of improperly obtained or unnecessarily retained data, including models and algorithms trained on such data. Its January 2024 AI guidance likewise says FTC actions have required deletion of models and algorithms developed wholly or partly with unlawfully obtained consumer data.
• The EU AI Act's Article 10 applies data-governance requirements to training, validation, and test data for high-risk AI systems. It states that special-category personal data used under the relevant exception must be deleted once bias correction is complete or the retention period ends, whichever comes first.
• The AI Act also includes record-keeping/logging requirements for high-risk systems. Article 19 requires providers to keep automatically generated logs under their control, tied to Article 12's logging framework. This creates a genuine tension: retain enough evidence for compliance, but minimize and protect personal data inside that evidence.
• NIST material located here discusses evaluating privacy-enhancing techniques such as differential privacy, but I did not find a NIST source in this investigation that mandates or certifies "agent forgetting."

What "proof" should mean operationally: For an agent system, require a forgetting certificate with bounded claims:
• A stable deletion subject ID and cryptographic hash of the original object.
• The exact scope: raw object, chunks, embeddings, summaries, tool traces, checkpoints, cache entries, exports, backup classes, and model-training inclusion status.
• An inventory generated from provenance and taint/dependency records.
• Signed evidence that object keys were destroyed or storage records deleted.
• Results of index scans, retrieval probes, and access-denial tests after revocation.
• A declaration of limits: "No claim is made regarding data supplied to an external model provider," "session X was terminated," or "model-level unlearning was not performed."
• Independent auditability and immutable timestamps.
Without those boundaries, "we forgot it" is a marketing phrase.

FIELD REPORT 4 — Open-Source Raid
LOOT: NVIDIA OpenShell
| Field | Assessment |
| Repository | NVIDIA/OpenShell — "OpenShell is the safe, private runtime for autonomous AI agents." |
| License | Apache License 2.0 |
| Last commit date | I could verify the repository has 1,610 commits, but the fetched GitHub content did not expose a trustworthy latest-commit timestamp. I will not invent one |
| Signup/API key | Core source and local installation are publicly accessible; no signup requirement is stated for the runtime itself. Running third-party model providers may separately require provider credentials |
| What code/runtime claims to do | Sandboxes agents; enforces filesystem, syscall, and network policy; routes network through policy checks; injects credentials only for approved endpoint-bound requests; includes a policy-prover component |
| What is verifiable from reviewed materials | Public Rust-oriented component layout includes CLI, server/gateway, sandbox runtime, supervisor, sandbox backend, and policy prover; README explicitly describes kernel/gateway enforcement and endpoint-bound credential handling |
| What is not established by this review | Retroactive model-memory deletion, comprehensive cross-system taint tracking, or a proof that content previously supplied to the model has been forgotten |
This is the strongest runtime-control loot found. It directly supports the thesis's "mappable, permissioned, revocable" authority model for external resources, although it is not itself a complete secure-memory system for model-visible content.

LOOT: DROS-VEP Lite
| Field | Assessment |
| Repository | Top-Celestial-Company-Ltd/DROS-VEP-lite |
| License | Apache License 2.0 |
| Last commit date | I could not verify a reliable last-commit date from the available result, so I will not state one |
| Signup/API key | Search material presents it as freely cloneable/reproducible; no signup/API-key requirement was identified |
| What it actually is | An open reference/evaluation environment for security controls at agent authorization-to-execution boundaries |
| Relevant implementation claim | Its published comparison labels hot revocation as enforced for its in-band model and points to seL4_CNode_Revoke() as the capability-system revocation analogue |
| Limitation | This is a benchmark/reference environment, not evidence of a broadly deployed agent-memory substrate with end-to-end provable forgetting |
DROS-VEP Lite is useful as a test harness for authority-revocation scenarios, especially "revoked authorization" and "dynamic invalidation," but it should not be represented as a mature generalized agent-memory platform without an independent code audit.

LOOT: agent-memory-dotnet
| Field | Assessment |
| Repository | joslat/agent-memory-dotnet |
| License | MIT |
| Last commit date | Not reliably available in the fetched result; not asserted |
| Signup/API key | No signup/API-key requirement was identified for the repository/library itself; integrations may have their own requirements |
| What it claims | Owner-scoped long-term memory, multi-tenant isolation, scoped recall and clearing, read audits, decay/pruning, and configurable isolation |
| What is concretely useful | The project clearly documents an ownership/scope model and explicitly states limitations, including that session IDs are capabilities and short-term messages by handle are an exception |
| Limitation | Application/library-level isolation is only as strong as the host's use of scopes and the storage/backend configuration. The stated "unscoped global behavior" compatibility mode is a dangerous default for multi-tenant deployment |
The project is worth studying because it names the essential controls—owner scope, recall filtering, audit, pruning—but its own documentation demonstrates why "memory scope" is not automatically a hard boundary.

Conditional leads, not accepted loot
• LangGraph: accepted as an open-source orchestration framework, not accepted as revocable-capability/memory-isolation loot. I could verify state persistence and human-in-the-loop state editing, but not a mandatory authorization/reference-monitor design.
• Silo, LightAgent, AgentScope: promising documented scope/tenancy or sandboxing features, but I did not complete code-path and test verification sufficient to call their security properties established. Their public descriptions are leads, not proof.
• Machine-unlearning repositories: I found research and evaluation frameworks, but no result that establishes end-to-end, agent-level, formally verified forgetting across prompt memory, vector stores, logs, tools, external providers, and model parameters.

VERDICT
Is the OS-page model right? Yes, as an architecture metaphor—but incomplete as a security guarantee. The right unit is not an undifferentiated "memory page"; it is a provenanced, policy-labeled memory object accessed through a trusted broker. The page-table analogy becomes useful when it forces explicit answers to:
• Who owns this object?
• Which principal, task, tool, and purpose may read it now?
• Can it enter model context, or only a deterministic tool?
• What derivatives exist?
• When does it expire?
• Can the system prove future retrieval has stopped?
• Which audit record shows the decision?
The analogy breaks if it suggests that unmapping a memory object makes the neural model forget it. OS pages are addressable state under a kernel. An LLM is a learned function plus a transient inference trajectory. Once data crosses the prompt boundary, access control becomes much weaker.

First build priorities
1. A real reference monitor outside the model. Put tool invocation, network egress, filesystem access, credential resolution, and memory retrieval behind a policy-enforcing service. Default deny. Every access is authenticated and authorized at use time.
2. Opaque, short-lived, operation-specific capabilities. Never give models raw cloud keys, OAuth refresh tokens, database passwords, signing keys, or broad browser session cookies. Use per-request or narrowly scoped credentials brokered only to the intended endpoint and action.
3. A typed memory object store. Every stored item should carry principal, task, source, sensitivity, allowed readers, expiry, retention basis, provenance parents, and a policy version. Retrieval must be filtered in the trusted layer—not by an LLM instruction.
4. Provenance and derived-data tracking. Treat summaries, embeddings, extracted facts, caches, checkpoints, and exports as dependent objects. Without a dependency graph, deletion is guesswork.
5. Context rotation after sensitive exposure. Do not reuse an agent context that has read a credential or high-risk PII for unrelated work. End it, record its exposure set, and create a fresh context. This is the closest practical response to in-context contamination.
6. Deletion certificates, not deletion assurances. Produce signed, queryable evidence of revocation, key destruction, object/index deletion, denied retrieval attempts, remaining exceptions, and model-provider boundaries.
7. Adversarial tests. Test whether a revoked secret can be recovered from the model, checkpoint, scratchpad, vector search, audit system, browser cache, subprocess, tool output, or another agent's shared workspace. A system that cannot run these tests does not know whether authority drift exists.

Where the industry is kidding itself
• Calling a vector-store filter "memory isolation" when the same application identity can query all namespaces.
• Calling prompt instructions "tool permissions."
• Calling deletion of a chat row "forgetting" while embeddings, summaries, logs, evaluation datasets, traces, backups, and downstream model-provider records survive.
• Giving agents broad bearer credentials and hoping a policy layer can retract them later.
• Treating long-term memory as benign personalization instead of a write-capable, cross-time authority channel.
• Claiming "AI unlearning" without naming the threat model, forget set, derivative systems, evaluation attack, and residual-risk statement.
The thesis is strongest if refined to this: memory is not just stored knowledge; it is a delayed capability distribution system. Secure long-running agents must make memory retrieval and authority use separately permissioned, continuously rechecked, and auditable. Revocation must be enforced where an action occurs; forgetting claims must be bounded to what the system can actually inventory and control.

SOURCES
• NVIDIA OpenShell public repository and architecture description: sandbox isolation, kernel-mediated controls, gateway policy checks, endpoint-bound credential injection, Apache-2.0 license, and component layout. (github)
• LangGraph public repository: durable execution, working/persistent memory, stateful workflow support, and human state modification; no verified built-in mandatory revocation boundary from reviewed material. (github)
• Mozilla and Chromium permission documentation: runtime optional permissions, removal/revocation events, and active-versus-granted permission distinction. (developer.mozilla)
• FTC publications on security through data management and AI privacy/confidentiality commitments: retention schedules and deletion of data, models, and algorithms in enforcement contexts. (ftc)
• European Commission AI Act Article 10 service material and Article 19 text: high-risk data governance, special-category data deletion condition, and logging retention. (ai-act-service-desk.europa)
• Machine-unlearning cryptographic evaluation paper: formalized evaluation/certified-removal framing and its assumptions. (arxiv)
• Open-source leads on scoped/multi-tenant memory and agent-runtime testing: agent-memory-dotnet, DROS-VEP Lite, LightAgent, AgentScope, and Silo. (github)
Plus 45 cited sources shown in the Sources pane: NVIDIA/OpenShell repo, langchain-ai/langgraph, OpenShell AGENTS.md, MDN WebExtensions permissions docs, Chromium permissions docs, LangGraph trust-gated checkpoints proposal #7303, joslat/agent-memory-dotnet (+ getting-started.md), navjyotnishant/silo, wanxingai/LightAgent, agentscope-ai/agentscope, arXiv "A Reliable Cryptographic Framework for Empirical Machine [Unlearning]", FTC "Lenses of security" and "AI Companies: Uphold Your Privacy and Confidentiality Commitments", EU AI Act Service Desk Article 10, artificialintelligenceact.eu Articles 10 & 19, NIST draft guidance on evaluating privacy protection, Top-Celestial-Company-Ltd/DROS-VEP-lite, ACM ERASURE framework, arXiv PECKER, langchain-ai/langgraph-bigtool, opendatahub-io/openshell, NVIDIA/OpenShell-Community, himorishige/pi-openshell-subagent, OpenShell Discussion #469, CNCF sandbox issue #522 (OpenShell), NVIDIA/OpenShell AGENTS.md, FTC final Everalbum order, FTC "AI Companies: Uphold Your Privacy and Confidentiality Commitments", EU AI Act Service Desk Article 10, artificialintelligenceact.eu Articles 10 & 19, NIST draft guidance on evaluating privacy protection, Top-Celestial-Company-Ltd/DROS-VEP-lite, ACM ERASURE framework, arXiv PECKER, langchain-ai/langgraph-bigtool, opendatahub-io/openshell, NVIDIA/OpenShell-Community, himorishige/pi-openshell-subagent, OpenShell Discussion #469, CNCF sandbox issue #522 (OpenShell).

--- END VERBATIM PERPLEXITY RESPONSE ---
