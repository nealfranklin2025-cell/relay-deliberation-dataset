# Docket 02 Jury Leg — ChatGPT

- **Docket:** 02 — Memory as a Security Boundary
- **Seat:** ChatGPT (fresh browser leg "Memory Security Report", memory-free; standing thread untouched)
- **Run:** 2026-10-02 ~14:38 CDT
- **Fidelity note:** ChatGPT ran with web browsing ("Worked for 30s"). The leg de-duplicated rendering artifacts (each code snippet appeared twice, once raw and once normalized — no substantive content dropped). Inline citation badges like "GitHub +1"/"arXiv +1" omitted as UI artifacts; linked source names kept.

--- BEGIN VERBATIM CHATGPT RESPONSE ---

I'm treating this as an implementation audit, not a summary. Most current agent frameworks fail the most important test in the prompt: they treat memory as an infinitely growing blob of text with tool calls attached, rather than as a protected resource with distinct capabilities, lifetimes, and revocation boundaries. But there are real implementations — OpenShell, Landlock, Capsicum, Mem0, LangGraph's store system — that handle pieces of this with verifiable code. The gap is almost entirely at the model-context boundary: once information crosses into active context, all current systems degrade to trust. I'll separate verified implementations from documented limitations throughout.

# SUMMARY

Bottom line: the thesis survives the field test, with one important refinement. The security problem is not simply that agents accumulate too many tokens. It is that agent systems commonly mix three different things that operating systems deliberately separate:

1. Data — what the agent has learned or retrieved.
2. Authority — what the agent is currently allowed to do.
3. Execution state — what is already sitting in a process, browser, KV cache, checkpoint, or model context.

Today's systems are reasonably good at controlling #2 at external boundaries. OpenShell, Linux Landlock, capability systems, browser isolation, and approval middleware demonstrate that. They are much weaker at controlling #1 and #3 once information has crossed into the model context.

The most important finding is therefore:

Revoking a capability is currently much easier than revoking knowledge of that capability.

OpenShell is an unusually good example. Its provider system can detach credentials from a running sandbox and its network policies can hot-reload. But OpenShell explicitly documents that already-running processes retain the old credential placeholder environment, and that the system does not mutate a live process environment. The proxy can stop resolving those placeholders, but anything the agent has already copied into its own process/context is outside that revocation mechanism.

The memory side is even less mature. LangGraph, Mem0 and Microsoft Agent Framework provide real namespace/session/run scoping and deletion primitives, but they operate primarily on external memory stores, not on an OS-like protected representation of the model's active context. LangGraph even has a current 2026 issue where deleting SQLite-backed memory leaves corresponding embeddings behind.

And the research literature is beginning to expose exactly the failure mode in the thesis. GateMem reports that current shared-memory agent systems do not simultaneously achieve strong utility, access control, and reliable forgetting; FragFuse demonstrates that long-term memory can become a temporal channel through which access controls are bypassed.

So my assessment is:

The OS-page analogy is directionally right, but "memory page" needs to mean a security object, not merely a chunk of text. A real agent memory page would need an owner/principal, confidentiality and integrity labels, capability requirements, lifetime/TTL, provenance, revocation status, and an auditable lineage of every derivative created from it.

# FIELD REPORT 1 — CAPABILITY REVOCATION

## 1. What actually works today

### A. OpenShell — genuine runtime authority revocation

OpenShell is the strongest concrete implementation I found for the external-authority half of this problem. Its architecture puts an agent inside a sandbox whose filesystem, process and network access are enforced outside the model's reasoning. Filesystem restrictions use Linux Landlock; network requests pass through a policy proxy; provider credentials are resolved only for authorized endpoints.

Crucially, its provider system now supports:

- attach provider to a running sandbox;
- detach provider from a running sandbox;
- credential rotation;
- dynamic network-policy changes;
- audit/receipt information for changes.

Its network policy is explicitly hot-reloaded rather than requiring a restart.

But the implementation contains the exact limitation this investigation was looking for. OpenShell documents:

- credential effects apply to new process launches;
- already-running processes retain their environment;
- OpenShell does not mutate a live process environment;
- detaching a provider revokes future credential resolution;
- it does not remove placeholder strings from already-running process environments.

That's an excellent real-world example of the distinction between:

revoking an authority source and scrubbing an already-running computation's state.

### B. Linux Landlock

Linux Landlock is a genuine kernel enforcement mechanism, but it has an important OS-semantic limitation. Landlock rules restrict the process and its future children, and resources can retain the access scope they had when acquired.

The kernel documentation is unusually explicit: a file descriptor obtained while a particular permission exists can retain that scoped access afterward.

That is actually instructive for agents. An OS does not assume that changing the pathname permission magically revokes every capability already handed to a process. The resource itself carries authority. That is precisely how an agent memory system should probably behave.

### C. Capsicum — capability attenuation

Capsicum provides perhaps the clearest established analogue to the proposed agent model. A file descriptor becomes an unforgeable capability with an associated rights mask. Rights can be reduced with cap_rights_limit; capability mode prevents obtaining new authority through global namespaces.

The interesting property is attenuation: You can hand a process a capability with fewer rights than the one you possess. That's much closer to the desired agent architecture than "here is a tool description; please don't misuse it."

### D. LangGraph — approval, interruption and state control

LangGraph has a real runtime interruption mechanism. A tool call can be paused before execution, surfaced to a human, edited/rejected/approved, and then resumed.

This is meaningful authority control, but it is not equivalent to capability revocation. LangGraph's tool registry can be dynamically overridden in middleware, but the normal model is still application-level tool dispatch rather than kernel-style authority objects.

Its long-term store does support explicit delete, namespaces and TTLs.

So I would classify LangGraph as:

good orchestration-level control; not an OS-style security boundary.

## 2. Where revocation fails

There are four recurring failure modes.

### Failure A — cached credentials

If a token has been handed to the model, subprocess, browser, shell environment or another tool, revoking the upstream account does not erase the copy. OpenShell's own documentation demonstrates a milder version: detach prevents subsequent credential resolution but doesn't mutate an already-running process environment.

### Failure B — in-context authority

Suppose the model sees:

AWS_SESSION_TOKEN=...

and the runtime subsequently disables AWS.

The runtime can revoke AWS access. It cannot revoke the model's representation of the token. If the model can still output the string, send it to another tool, or reconstruct it from its context/KV state, "revocation" has only removed one route to authority.

### Failure C — tool output reintroducing authority

A revoked memory can return through another channel:

memory → retrieved document → tool result → context

Deleting the original memory doesn't necessarily delete:

- summaries,
- embeddings,
- checkpoints,
- browser state,
- cached tool results,
- logs,
- generated files,
- downstream-agent messages.

This is not theoretical. A current LangGraph issue reports deleted SQLite memory records leaving their vector embeddings behind.

### Failure D — derived authority

This is the nastiest one. An agent doesn't have to remember:

"I have GitHub write permission."

It can remember:

"The GitHub token is stored in $FOO." or: "Use the authenticated gh command." or: "The browser profile is already logged into production."

Authority has migrated from a credential into procedural memory. That's why ordinary credential revocation is insufficient for persistent agents.

# FIELD REPORT 2 — COMPARTMENTALIZATION

## What exists now

There are genuine pieces of the architecture.

| Layer | Existing mechanism | Security boundary? |
|---|---|---|
| Working state | LangGraph checkpoints | Partial |
| Long-term memory | LangGraph namespaces | Partial |
| User/agent/run memory | Mem0 | Partial |
| Session filesystem memory | Microsoft Agent Framework | Partial |
| Browser state | Browser Use profiles | Partial |
| OS filesystem | Landlock | Yes, at OS boundary |
| Credentials/network | OpenShell | Yes, at runtime boundary |
| Capability attenuation | Capsicum | Yes |
| Model-context ACL | General production agents | No convincing general implementation found |

### Mem0

Mem0 explicitly scopes memory using:

- user_id
- agent_id
- run_id
- app_id

and requires a scope identifier when adding memory.

It also implements deletion by memory ID or by user/agent/run filters.

That's real compartmentalization. But it is database compartmentalization, not processor-enforced compartmentalization. Once retrieved, the memory becomes ordinary model input.

### Microsoft Agent Framework

Microsoft's current Agent Framework has a file-memory provider that defaults to session isolation, with explicit scope options for sharing memory across sessions. The implementation maps each scope to a distinct storage namespace.

Its Foundry memory provider also has an explicit EnsureStoredMemoriesDeletedAsync() implementation that deletes the memory store scope.

Again: legitimate isolation of persistent storage. Not a protected model-context address space.

### Browser agents

Browser Use has an interesting security pattern: sensitive credentials can be supplied separately from the model, with placeholder names visible to the LLM while the real values are injected into form fields by the execution layer.

That's exactly the right instinct: Don't make the model possess the secret just because the agent needs to use it.

But persistent browser profiles create another state compartment. Cookies and local storage can survive across tasks when the browser is kept alive. So the browser itself becomes a memory-bearing authority object.

## What page-table-like memory would look like

This is where I think the thesis gets particularly useful. Imagine every memory object as:

MemoryPage {
    id
    owner/principal
    confidentiality_label
    integrity_label
    provenance
    created_at
    expires_at
    capabilities
    revocation_epoch
    parent_pages[]
}

A retrieval would not simply be:

memory.search(query)

It would be conceptually:

PAGE_FAULT(
    principal = agent_A,
    capability = READ,
    memory_page = P123,
    purpose = "task_847"
)

The memory manager checks:

principal authorized?
purpose authorized?
capability current?
page expired?
page revoked?
provenance acceptable?
integrity sufficient?

Only then does it materialize a read view.

### Page fault

Unauthorized retrieval → no content. Not: "Here is the content, but the system prompt says don't reveal it."

### Copy-on-write

Suppose Agent A and Agent B both need a summary. Instead of giving B A's memory page:

P123 — confidential customer record

the system creates:

P123  original
 |
 +-- P456 — derived summary

with a narrower capability set.

Revoking P123 can then invalidate descendants according to policy.

### Shared pages

Two agents could share a page by reference without copying its plaintext. That means:

Agent A ─┐
         ├── Capability C → Page P
Agent B ─┘

rather than:

Agent A → plaintext
Agent B → plaintext

The latter creates two ungoverned copies.

### Page replacement / eviction

An agent shouldn't accumulate an eternal transcript. Pages could have:

- TTL;
- recency;
- sensitivity;
- task affinity;
- principal;
- provenance;
- revocation epoch.

Eviction then becomes a security operation, not just a token-saving optimization.

# FIELD REPORT 3 — PROVABLE FORGETTING

This is where the thesis encounters its hardest technical boundary.

## 1. Ordinary deletion is not forgetting

A memory database can prove:

"Row 123 was deleted."

It cannot automatically prove:

"No representation derived from row 123 can influence this agent again."

Those are very different propositions. The derivative graph might be:

raw PII
   ↓
memory record
   ↓
embedding
   ↓
summary
   ↓
checkpoint
   ↓
KV cache
   ↓
model context
   ↓
tool output
   ↓
new memory

A deletion API usually touches only one node.

## 2. Machine unlearning exists—but it is not agent forgetting

NIST now explicitly defines machine unlearning as selectively removing the influence of particular training records from a trained model. It distinguishes exact approaches such as retraining from approximate parameter-update approaches.

There are genuine theoretical guarantees. For example, a 2025 ICML paper on certified unlearning constructs formal guarantees using noisy fine-tuning of retained data.

But that is fundamentally different from an agent forgetting a secret it saw yesterday.

Training-data unlearning asks:

"Can I make this training example's influence on the parameters sufficiently equivalent to a model that never saw it?"

Agent forgetting asks:

"Can I prove that this secret cannot reappear from any persistent or transient state?"

The second is substantially harder.

## 3. Cryptographic erasure

Cryptographic erasure is the cleanest primitive for stored encrypted memory. The basic construction is:

plaintext
   ↓
encrypt with unique DEK
   ↓
ciphertext
   +
DEK stored separately

To erase:

destroy DEK

The ciphertext can remain physically present while becoming computationally inaccessible.

There is now a 2026 independent Internet-Draft, SAIHM, explicitly proposing per-memory-cell encryption, revocable sharing and cryptographic erasure.

However, it is important not to overstate this: SAIHM is an Independent Submission Internet-Draft, not an IETF standard. Its own repository says that explicitly.

And its open MCP server is partly a client for an operator endpoint rather than a complete cryptographic runtime itself. The live service requires authentication; offline demonstrations can run without an account.

So cryptographic erasure is a credible primitive, but it solves:

"Can this encrypted persistent memory cell be made unreadable?"

It does not solve:

"Did the model already internalize its plaintext?"

## 4. Secure enclaves

Trusted Execution Environments can make an important guarantee:

plaintext exists only inside an attested enclave.

There are open implementations of agent inference/tool execution using TEEs, but this is primarily confidentiality and integrity, not forgetting.

An enclave can protect a secret from the host. It cannot automatically prove that a model which saw the secret no longer has an internal representation of it. That distinction is fundamental.

## 5. Taint tracking / information-flow control

This is probably the most promising bridge between OS memory and agent memory. The research is moving rapidly here. Microsoft Research published a 2025 approach to permissive information-flow analysis for LLMs that attempts to propagate only labels associated with inputs that actually influenced an output, rather than conservatively labeling everything.

More recent agent-security work is attempting to track:

- source;
- destination;
- semantic influence;
- causal influence;
- persistent memory propagation.

The 2026 Ghost in the Agent work explicitly argues that traditional taint tracking breaks because LLMs transform data semantically rather than merely copying bytes.

That is exactly the problem an OS page-table metaphor runs into. An OS can say:

register R7 came from page P.

An LLM can transform:

"My SSN is 123..."

into:

"The user has a particular identifying number."

or simply use the information to choose an action.

There may be no substring to taint.

## 6. The strongest recent warning: memory itself becomes an attack channel

FragFuse is particularly relevant to this investigation. The 2026 paper describes an attack in which prohibited information is fragmented across interactions, stored in apparently benign long-term memory, and later reconstructed during retrieval. It reports bypasses against multiple agent access-control mechanisms.

This directly supports the thesis's core concern: persistent memory isn't merely storage; it becomes part of the security control plane.

GateMem goes further. Its 2026 benchmark specifically evaluates:

- long-horizon utility;
- access control;
- active forgetting;
- cross-principal leakage.

Its reported result is that current methods do not simultaneously provide strong performance on all three governance dimensions.

That's unusually direct empirical evidence for the relay thesis.

# FIELD REPORT 4 — REGULATORY / FORMAL LANDSCAPE

## NIST

NIST now explicitly recognizes machine unlearning as a technique for removing particular training-data influence from models.

NIST's AI security material distinguishes exact and approximate unlearning, and its recent AI guidance discusses unlearning among mechanisms for reducing model knowledge.

Important limitation: NIST does not currently give agent developers an OS-like formal specification saying:

"An agent must prove that memory page X is no longer observable."

The conceptual ingredients exist; the end-to-end agent guarantee does not.

## FTC

The FTC has demonstrated that "delete the source data" can extend to derived AI products. The Everalbum settlement required deletion not only of user photos and face embeddings but also facial-recognition models and algorithms developed from the affected user data.

The final order required a sworn statement confirming deletion/destruction of the affected work product.

The FTC later summarized the principle bluntly: companies that improperly obtain or misuse consumer data may be required to delete algorithms, models and other data products derived from it.

That is highly relevant to agents because it rejects the simplistic argument:

"The original database row is gone, therefore the AI has forgotten it."

The regulator has already treated derived model artifacts as potentially subject to deletion.

## GDPR / EU

GDPR Article 17 establishes a right to erasure under specified circumstances and requires controllers to erase personal data without undue delay where applicable. It also addresses copies/replications when personal data have been made public.

The EDPB's 2024 AI-model opinion specifically considers:

- when AI models can be considered anonymous;
- use of personal data in model development/deployment;
- consequences where models were trained on unlawfully processed personal data.

The important distinction is: GDPR creates legal obligations around personal-data processing and erasure; it does not provide a magic technical definition of "the model has forgotten." That technical question remains open.

## EU AI Act

The EU AI Act is relevant, but not in the simplistic sense of "the AI Act mandates memory deletion." Article 12 requires high-risk AI systems to technically allow automatic recording of events over the system's lifetime.

That pushes in the opposite direction from naive deletion: auditability itself requires retained records.

So a compliant architecture may need two simultaneously true properties:

Forget protected content
          +
Retain auditable evidence that the forgetting operation occurred

That is another reason cryptographic receipts, provenance and deletion tombstones are attractive. The right architecture is not "delete all traces." It is: delete the sensitive payload while preserving the minimum auditable metadata necessary to establish that deletion occurred.

# LOOT

## LOOT #1 — NVIDIA OpenShell

Status: LOOT
License: Apache-2.0.
Last-commit date: GitHub's public repository view was actively updated on the audit date, October 2, 2026, but its crawler did not expose the timestamp of the HEAD commit itself. I will not invent an exact commit timestamp. The repository reports 1,612 commits and current active development.
What it actually implements:

- sandbox isolation;
- Landlock filesystem enforcement;
- process restrictions;
- network policy proxy;
- provider-scoped credentials;
- runtime provider attach/detach;
- hot-reloaded network policies;
- audit logs;
- policy verification.

Its policy prover is real code, not a marketing claim: it encodes policy, credential scope and binary capability information into a Z3 SMT model and performs formal containment/reachability checks.

The prover itself is candid about its boundary: unsupported policy features return unsupported; passing a check does not prove that a running sandbox actually installed the restrictions.

Signup/API key: no. It is locally installable/open source. A model provider may separately require credentials, but OpenShell itself does not require a hosted account.

Why it matters: this is probably the closest production-oriented open-source system I found to the authority side of the proposed OS model.

## LOOT #2 — Mem0

Status: LOOT — memory layer, not security kernel
License: the repository currently exposes its OSS implementation and supports local deletion operations.
Last-commit date: the public crawler confirmed active repository code updated in late September 2026, but did not expose a reliable exact HEAD timestamp; I therefore won't manufacture one.
Actually implemented:

- memory scoped by user;
- agent;
- application;
- run/session;
- individual memory deletion;
- bulk deletion;
- entity-cascade deletion.

Signup/API key: the OSS SDK does not require a hosted Mem0 account for local operation. Some Platform functionality does.

Security assessment: useful substrate for compartmentalized memory, but not sufficient for the thesis because deletion of external memory doesn't erase already-materialized model context.

## LOOT #3 — LangGraph memory/checkpoint substrate

License: MIT.
Actually implemented:

- checkpointed execution;
- thread-scoped state;
- namespaces;
- persistent stores;
- deletion;
- TTL support where the backend implements it;
- human approval/interruption;
- runtime context.

Signup/API key: the open-source framework itself does not require one.

Important negative finding: LangGraph currently illustrates the gap between a memory API and a complete security boundary. A 2026 issue reports deleted SQLite memories leaving embeddings in store_vectors. That is precisely the kind of derivative-state problem a true memory security layer has to solve.

## NOT LOOT — SAIHM

This is the most interesting provable-erasure concept I found, but I am not putting it in LOOT under your raid rules.

Why? Its open MCP server is explicitly a client to an operator endpoint and requires an endpoint/token for the normal runtime. The hosted service requires GitHub sign-in for its free live trial; offline demos work without an account.

The protocol itself is also an independent Internet-Draft, explicitly not an IETF standard.

The cryptographic idea is nevertheless legitimate and worth watching: per-cell encryption keys, revocation and destruction of the DEK, with an auditable tombstone.

I'd classify it: PROMISING RESEARCH/REFERENCE IMPLEMENTATION — GATED LIVE SERVICE rather than verified raid loot.

# VERDICT

## My judgment on the thesis

I agree with the thesis, with one correction: memory should be treated less like "pages" and more like "capability-bearing pages."

An OS page isn't secure merely because it is a page. Its security comes from the machinery around it:

- page tables;
- protection bits;
- address spaces;
- faults;
- privilege levels;
- ownership;
- kernel mediation;
- process isolation.

Agent memory needs the equivalent.

### What I would build first

For a family of agents expected to run for weeks, I'd build these in this order:

### 1. Externalize authority

Never make the model the bearer of long-lived credentials. The model gets:

capability: github.issue.create
scope: repo=X
expires: 10 minutes

not:

GITHUB_TOKEN=xxxxxxxx

OpenShell's credential proxy model demonstrates that this is practical.

### 2. Make memory objects scoped

Every memory item should have:

principal
task
agent
tenant
classification
provenance
expiry
parentage
capability

Mem0, Microsoft Agent Framework and LangGraph demonstrate pieces of this architecture already.

### 3. Make retrieval an authorization event

This is the big missing piece. Don't ask:

"Does this memory match the query?"

Ask: "Is this principal currently authorized to materialize this memory into this context?"

That should be enforced outside the LLM.

### 4. Track derivatives

If:

P1 → summary P2 → embedding P3 → checkpoint P4

then deleting P1 should at least identify P2/P3/P4 as descendants.

This is where provenance graphs and information-flow labels become essential.

### 5. Add epochs

Every capability should have a revocation epoch:

github-write:
    epoch 41

After revocation:

epoch 42

Anything carrying epoch 41 becomes invalid.

This handles a surprisingly large fraction of authority-drift problems.

### 6. Make forgetting cryptographically cheap where possible

For persistent sensitive memory:

unique memory key
       ↓
encrypted memory
       ↓
destroy key
       ↓
append deletion receipt

That gives you something much stronger than "the database returned zero rows." But it must be combined with provenance/taint tracking to handle plaintext that already reached computation.

## Where I think the industry is kidding itself

The biggest self-deception is treating memory deletion APIs as equivalent to forgetting. They're not. A vector database returning:

0 results

doesn't establish that:

- the model didn't see the item;
- an embedding wasn't retained;
- a checkpoint doesn't contain it;
- a browser cookie doesn't encode the authority;
- another agent didn't receive a copy;
- a summary doesn't preserve it;
- a tool output didn't reintroduce it;
- the model won't reconstruct it from earlier context.

The second major self-deception is believing that prompt instructions can enforce security boundaries. "Do not use this memory after revocation" is not a security primitive. A kernel doesn't say: "Please don't read page 0xABC." It makes the access fail. Agents need the equivalent.

And the third is perhaps the most interesting: the dangerous thing an agent remembers isn't necessarily a secret. It is often the procedure for exercising authority. An agent that forgets the API token but remembers:

"The production deploy is performed with tool X against endpoint Y"

may still retain a meaningful part of the capability.

That's why I would make the relay's central design object not memory, but:

memory + provenance + capability + revocation state.

The strongest current evidence points in that direction: OpenShell demonstrates externally enforced authority boundaries; capability systems demonstrate attenuated authority; modern memory frameworks demonstrate namespace/TTL/deletion primitives; and 2026 research such as GateMem and FragFuse demonstrates that persistent memory itself can defeat access-control assumptions.

Final field verdict: the proposed architecture is not science fiction. Most of its pieces already exist independently. What does not yet exist as a mature general-purpose agent runtime is the thing that combines them into an enforceable, auditable address space in which knowledge, authority, provenance and revocation travel together. That's the missing layer I'd build.

# SOURCES

### Primary implementations / specifications

- NVIDIA OpenShell GitHub — sandbox runtime, provider credentials, policy enforcement and prover.
- OpenShell provider revocation documentation — explicit limitations of detach against already-running processes.
- OpenShell policy prover — SMT-based formal policy containment.
- Linux Landlock documentation — kernel-enforced filesystem/network restrictions.
- Capsicum capability implementation documentation — file-descriptor capabilities and rights attenuation.
- LangGraph — checkpoints, stores, interrupts and runtime state.
- Mem0 — scoped persistent memory and deletion.
- Microsoft Agent Framework — session-scoped file memory and scoped Foundry memory deletion.

### Regulatory / standards

- NIST machine-unlearning glossary
- NIST adversarial ML taxonomy
- FTC Everalbum enforcement record
- FTC final Everalbum order
- GDPR Article 17 / EUR-Lex
- EDPB AI-model opinion
- EU AI Act, Article 12

### Research worth putting on the relay watchlist

- GateMem — memory governance, access control and active forgetting benchmark. (arXiv)
- FragFuse — memory-based access-control bypass. (arXiv)
- Collaborative Memory — dynamic multi-principal memory access control and provenance. (arXiv)
- Ghost in the Agent / NeuroTaint — information-flow tracking across semantic transformation and persistent memory. (alphaXiv)
- Permissive Information-Flow Analysis for LLMs — Microsoft Research, dynamic information-flow/taint approach. (Microsoft)
- Certified Unlearning for Neural Networks — formal machine-unlearning guarantees. (Proceedings of Machine Learning Research)
- SAIHM Internet-Draft — cryptographic agent-memory erasure concept; explicitly an independent draft, not an IETF standard. (IETF Datatracker)

--- END VERBATIM CHATGPT RESPONSE ---
