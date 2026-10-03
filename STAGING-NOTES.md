# Staging notes (2026-10-03 — not for publication as-is)

This tree stages the relay publication for GitHub and Hugging Face. It mirrors
the published Zenodo v0.1 + v0.2 content with corrections. **Nothing here has
been pushed or uploaded anywhere.**

## What was corrected vs. the published Zenodo files

1. **`.git/` excluded.** The published v0.1 ZIP contains a `.git/` directory
   whose logs expose the operator's email address (10 occurrences across
   `.git/logs/HEAD` and `.git/logs/refs/heads/master` — address on file,
   redacted here).
   The package's own `PRIVACY.md` claims no emails exist in the package — the
   scrub pass did not cover `.git/`. This tree contains no `.git/`, so the
   privacy claim holds here.
2. **`CITATION.cff` corrected.** The published one says `0.1.0-draft`, DOI "TBD",
   URL "TBD", and preferred-citation type `unpublished` ("Draft — not yet
   published"). The staged copy carries the real DOIs, version 0.2.0, and a
   published citation. `repository-code` is a placeholder until the repo exists.
3. **`PUBLISHING-CHECKLIST.md` excluded.** Internal working doc; its headline
   ("Nothing has been uploaded or published anywhere") is false post-release.
4. **Repo `README.md` rewritten.** The packaged README said "DRAFT — not yet
   published." The staged README describes the live publication and links both
   DOIs. The original packaged README is superseded, not deleted from history.

## What was NOT changed

All transcripts, take/reject lists, method/codebook/limitations, dataset card,
privacy record, seats roster, license, and both v0.2 articles are byte-identical
to the published versions.

## Pending for a future version (operator's call)

- Mistral Small 4's Docket 02 seat transcript (filed, unpublished; contains a
  "Weavecte" typo vs. the source's "Weaviate").
- DeepSeek's Docket 02 seat (courier paste outstanding).
- The originality study (deliberately excluded from v0.1/v0.2).
- Provider-output republication rights review (flagged unchecked in the
  original checklist).
- A corrected Zenodo version (v0.3) superseding the v0.1 `.git` exposure —
  Zenodo versions are permanent; only a new version can supersede.
