# Human-Mediated Cross-Model Deliberation

**Author: Clifton O'Neal Franklin** — published under his own name, by his own choice.
**License: CC-BY-4.0** — see `LICENSE.md`.

The working home of the relay publication: verbatim transcripts from formal,
memory-free jury rounds in which independent commercial AI products are consulted
separately on the same research docket, with a human operator and a coordinator
agent ferrying material between them. No AI talks to another directly.

## Published versions (archived with DOIs on Zenodo)

- **v0.1** — *Human-Mediated Cross-Model Deliberation: Jury Transcripts and Take/Reject Lists from the Relay Operation (Docket 02, Round 1)* — DOI [10.5281/zenodo.23112330](https://zenodo.org/records/23112330)
- **v0.2** — v0.1 plus two companion articles: *Raids: How a Family of AIs Hunts Open-Source Tools (Legally)* and *What the Raids Bought Us: Reason, Purpose, Legality, and the Sharper Machine* — DOI [10.5281/zenodo.23112942](https://zenodo.org/records/23112942) (concept DOI for all versions: [10.5281/zenodo.23112329](https://doi.org/10.5281/zenodo.23112329))

Cite the Zenodo DOI, not this repo — the DOI is the permanent record.

## What's inside

- `transcripts/` — the four jury-seat transcripts (Grok, Claude, ChatGPT, Perplexity) plus the coordinator's consolidated verdict for Docket 02, Round 1 ("Memory as a Security Boundary")
- `take-reject-lists/` — what the round adopted and rejected, with provenance
- `articles/` — the two v0.2 companion articles (markdown + PDF)
- `METHOD.md` — how the relay runs · `CODEBOOK.md` — how to read the transcripts
- `LIMITATIONS.md` — what this dataset does not establish · `DATASET-CARD.md` — the dataset card
- `PRIVACY.md` — the scrub record · `SEATS.md` — the family roster, credited plainly

## The claim (attributed, not asserted as fact)

The authors' claim: the first longitudinal dataset of human-mediated cross-model
deliberation. An adversarial originality review scored the operation 3/10 on
originality of *mechanics* ("original operation, unoriginal mechanics") — every
component has precedents; no public project was found running the full
configuration. Treat "first of its kind" as the authors' claim pending independent
verification. See `LIMITATIONS.md`.

## Note on the Zenodo v0.1 files

The published v0.1 ZIP on Zenodo contains a `.git/` directory whose logs expose
the operator's email address, plus a stale `CITATION.cff` and publishing
checklist written before release. This repo stages the corrected tree: no `.git/`,
corrected citation metadata. See `STAGING-NOTES.md`. A corrected Zenodo version
is the operator's decision to make — versions there are permanent.
