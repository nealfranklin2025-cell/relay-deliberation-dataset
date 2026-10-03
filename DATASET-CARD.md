# Dataset Card

## Motivation

**Why was this dataset created?**
To preserve, in publishable form, the deliberation record of a human-mediated
multi-AI jury: several independent commercial AI systems, consulted separately on
the same research question, with a human operator and a coordinator agent carrying
material between them. The authors' position is that no public dataset of this
kind of deliberation exists (see the originality study referenced in README.md).

**Who created it?**
Clifton O'Neal Franklin — the operator of the relay, credited under his own name by his
choice (2026-10-02) — and a coordinator agent ("Kathy," built on Meta's Muse
Spark models), running a standing research operation since September 2026. The
four seat responses were produced by the commercial AI products named in
`transcripts/`; the consolidated verdict and all packaging were produced by the
coordinator. Full contributor roster: `SEATS.md`.

## Composition

**What is in each instance?**
Five Markdown documents:

- 4 seat transcripts — one per AI product consulted (Grok, Claude via duck.ai,
  ChatGPT, Perplexity), each containing the docket brief metadata, a fidelity
  note from the coordinator, and the seat's verbatim response delimited by
  `BEGIN/END VERBATIM` markers.
- 1 consolidated verdict — the coordinator's synthesis of the four seats'
  findings, including the unanimous findings, disagreements, and the loot
  (accepted) vs. near-miss/watch (rejected) dispositions.

**Docket under test (Round 1):** "Memory as a Security Boundary" — the thesis
that long-running AI agents fail from memory treated as infinite, monotonic
accumulation of authority, not from small context windows; and that OS-level
memory semantics (revocation, compartmentalization, provable forgetting,
auditability) are the right model for the fix.

**How many instances?** 5 documents, ~99 KB total, covering one formal jury round
(2026-10-02).

**Is there anything the dataset does NOT include?**
Two further seats (DeepSeek, Mistral Small 4) were slated to file via the
operator-as-courier and had not filed at package build time. Earlier
investigations used precursor protocols (lane-based relay legs, blind
cross-grading) and are excluded — see METHOD.md. Personal information about the
operator was scrubbed — see PRIVACY.md.

## Collection process

1. The docket brief was drafted by one seat (Meta AI, in a direct thread) and
   reviewed by the operator.
2. The coordinator delivered the identical brief to each seat in a **fresh,
   memory-free conversation** (new chat / new browser leg; standing threads
   untouched), so no seat saw another seat's answer.
3. Each seat's full response was copied verbatim into a leg file with run
   metadata (date, time, seat identity, fidelity notes on truncation or UI
   artifacts).
4. The coordinator synthesized the four responses into the consolidated verdict,
   preserving agreements, disagreements, and per-seat attribution.
5. For publication packaging (2026-10-02), transcripts were cleaned only for
   privacy (see PRIVACY.md); no seat response text was altered, summarized, or
   reordered.

## Preprocessing / cleaning

- **Privacy scrub:** operator's name → "the operator"; one courier reference
  anonymized; one Perplexity thread URL redacted (ID removed). See PRIVACY.md
  for the full scrub log.
- **Fidelity preservation:** rendering artifacts noted in each file's fidelity
  note (e.g., duplicated paragraphs from DOM extraction, omitted UI citation
  badges). Where a seat refused to invent data (e.g., commit timestamps it could
  not verify), that refusal is preserved as part of the response.
- **No content editing:** beyond the privacy scrub, seat responses are verbatim.

## Intended uses

- Studying how independent commercial AI systems reason about the same
  technical question (agreement patterns, evidence standards, failure modes).
- Studying the jury/coordination protocol itself as a method for human-mediated
  multi-model deliberation.
- Baseline material for replication: run the same docket brief against the same
  (or newer) products and compare.

**Out-of-scope uses:** The dataset does not support claims about any AI
*model's* capabilities in general — seats are dated product snapshots (see
LIMITATIONS.md). It is not a benchmark with ground truth; several seats'
factual claims are unverified against primary sources by the authors.

## Distribution

Not yet distributed (DRAFT). Proposed: CC-BY-4.0 — see LICENSE.md (PROPOSED)
and PUBLISHING-CHECKLIST.md for venue options (Hugging Face Datasets, Zenodo
with DOI, GitHub).

## Maintenance

No update schedule is committed. If later jury rounds are run under the same
protocol, the authors intend to version the dataset (v1.1, v2.0) with a changelog.
Contact/maintenance identity is TBD by the operator before release.
