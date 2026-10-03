# Privacy

## The operator's decision (2026-10-02)

Clifton O'Neal Franklin chose to be publicly credited as the operator and author of this
dataset, under his full legal name — his words: "that's the name I want," and
nothing else goes with it. The name alone is public, by his explicit choice;
no address, phone, location, biography, family, health, finances, or workplace
accompanies it, anywhere in this package. The anonymization below was performed
first and is documented for the record; the name is public by decision, not by leak.

## Scrub rule

Neal's personal life stays out of this dataset by standing rule — his choice to
be named as the operator does not open the rest. The dataset contains no address,
phone, family members, biography, health, finances, workplace, or location.

## Scrub pass performed (2026-10-02, packaging)

Every transcript was searched for the operator's name, hometown and home
place names, addresses, phone numbers, family names, workplace, and account
identifiers (emails, thread URLs). Findings and actions:

| Finding | Location | Action |
|---|---|---|
| Operator's first name in a courier-role label | `transcripts/docket02-round1-consolidated-verdict.md` header | Replaced with "operator-as-courier" |
| Perplexity thread URL containing a thread UUID | `transcripts/docket02-round1-seat-perplexity.md` header | URL replaced with "standing relay thread (URL redacted for privacy)" |
| No emails, phone numbers, addresses, or workplace mentions | all transcripts | None found; nothing to remove |

Post-scrub verification: zero occurrences of the operator's first name and
zero occurrences of the thread UUID across all packaged files.

## Retained as operational identity (documented decision)

- **"Kathy"** — the coordinator agent's name — is retained wherever it refers
  to the agent's operational role (e.g., "relayed by Kathy," "Kathy's take").
  Redacting the coordinator's own name would make provenance incoherent. It
  appears only as the agent's identifier, never with any personal detail.
- **"Clifton O'Neal Franklin"** — the operator's name — appears as author credit by his
  choice. "The operator" remains in method text as role shorthand.
- **Seat product names** (Grok, ChatGPT, Perplexity, Claude, Meta AI) are
  retained; they identify commercial products, not people.

## Residual risk

The docket brief and seat responses discuss public technology topics only.
No transcript contained personal narrative. The remaining theoretical risk is
stylometric: a sufficiently motivated analyst could compare the operator's
brief-writing style against other public writing. This is accepted and noted;
the operator reviewed and accepted it.
