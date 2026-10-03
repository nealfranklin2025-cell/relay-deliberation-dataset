# Method: The Relay Protocol

## The operation in brief

The relay is a privately run research operation (active since September 2026)
with three roles:

1. **The operator** — a human. Sets dockets (research questions), reviews
   outputs, and acts as courier where a seat requires it.
2. **The coordinator ("Kathy")** — a Muse-based agent. Drafts and delivers
   briefs, ferries messages between seats, synthesizes verdicts, maintains the
   archive. The AIs never contact each other; the coordinator is the only bridge.
3. **The seats** — commercial AI chat products, each consulted in its own
   separate conversation (Grok, ChatGPT, Perplexity, Claude via duck.ai,
   Meta AI, and others in the wider operation).

There is **no direct AI-to-AI contact** anywhere in the protocol. Every
cross-seat exchange passes through the coordinator (or the operator-as-courier)
as a discrete, logged handoff.

## The formal jury protocol (what this dataset records)

A **jury round** proceeds as follows:

1. **Docket.** The operator commissions a research question ("docket") with a
   thesis to test. Docket 02: *"Memory as a Security Boundary"* — long-running
   agents fail from memory treated as infinite monotonic authority; OS-level
   memory semantics (revocation, compartmentalization, provable forgetting,
   auditability) are the fix.
2. **Brief.** A single docket brief is drafted (for Docket 02, drafted by the
   Meta AI seat and reviewed by the operator) and frozen.
3. **Memory-free legs.** The coordinator delivers the identical brief to each
   seat in a **fresh conversation** — new chat session or new browser leg, with
   the seat's standing thread untouched and no prior context. Each seat therefore
   answers blind to the others' responses. (This is the "memory-free jury"
   property: it is enforced procedurally, by using fresh sessions, not by any
   technical blinding of the products.)
4. **Verbatim capture.** Each seat's full response is copied into a leg file
   with metadata: docket, seat identity (product name + access route), run
   date/time, and a fidelity note recording any truncation, continuation, or
   rendering artifacts.
5. **Consolidated verdict.** The coordinator synthesizes the legs: unanimous
   findings, directional agreements, disagreements, per-seat attribution, and
   the take/reject dispositions (see `take-reject-lists/`).
6. **Evidence-ledger convention.** Seats are instructed to separate
   **Observed** (directly seen in sources), **Inferred** (reasoned from
   sources), and **Speculating** (beyond the evidence). The dataset preserves
   these labels where seats used them.

## Jury rounds vs. precursor relay legs

Not everything the operation ran qualifies as a jury round. The dataset includes
**only formal jury rounds** (currently: Docket 02, Round 1).

**Excluded precursors** (listed for transparency; transcripts not included):

| Investigation | What ran | Why excluded |
|---|---|---|
| September 11 investigation (2026-09-29) | Lane-based relay legs (each seat got a different sub-topic), then blind cross-grading + independent adjudication | Seats did not answer the same brief; not a jury round |
| AI future datacenters (2026-09-28) | Per-seat round-1 reports + cross-examination grading | Adversarial grading protocol, not a memory-free jury on one brief |
| AI data-center investigation (2026-09-23) | Per-seat reports, coordinator final report | No jury procedure at all |

These precursors informed the jury protocol's design but do not meet its
definition and are therefore out of scope for this release.

## Replicating a jury round

1. Freeze a docket brief: a thesis plus 3–5 field assignments (e.g., "find
   what actually implements capability revocation; cite primary sources").
2. Open a fresh, memory-free session with each seat product; paste the identical
   brief; save the full response with date/time and product identity.
3. Do not show any seat another seat's response until all legs are filed.
4. Synthesize: record unanimous findings, disagreements with attribution, and
   explicit take/reject dispositions for every concrete proposal.
5. Have each seat (and the synthesis) keep the observed/inferred/speculating
   separation.

## What the protocol does NOT do

- It does not blind seats to their own training data or product behavior.
- It does not prevent seats from having correlated errors (shared training
  corpora, shared web sources) — see LIMITATIONS.md on convergence.
- It does not verify seats' factual claims against primary sources; the
  coordinator records fidelity (what the seat said, and any refusal to invent)
  but fact-checking is a separate step.
